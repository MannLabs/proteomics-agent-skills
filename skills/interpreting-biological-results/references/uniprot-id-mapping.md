---
name: uniprot-id-mapping
description: Map UniProt accession IDs to gene names and other identifiers using the UniProt REST API in Python.
---

# UniProt ID Mapping via REST API (Python)

API base: `https://rest.uniprot.org`
Limit: 100,000 IDs per job. Results stored 7 days.
Docs: https://www.uniprot.org/help/api_idmapping

## Batch Mapping Function

The API is asynchronous: submit job → poll status → retrieve results. Paginate large result sets via `Link` header.

```python
import re, time
import requests
import pandas as pd
from requests.adapters import HTTPAdapter, Retry

API_URL = "https://rest.uniprot.org"

session = requests.Session()
session.mount("https://", HTTPAdapter(
    max_retries=Retry(total=5, backoff_factor=0.25, status_forcelist=[500, 502, 503, 504])
))

def map_uniprot_ids(
    ids: list[str],
    from_db: str = "UniProtKB_AC-ID",
    to_db: str = "Gene_Name",
    batch_size: int = 50_000,
) -> pd.DataFrame:
    all_dfs = []
    for i in range(0, len(ids), batch_size):
        batch = ids[i:i + batch_size]
        # Submit
        resp = session.post(f"{API_URL}/idmapping/run",
                            data={"from": from_db, "to": to_db, "ids": ",".join(batch)})
        resp.raise_for_status()
        job_id = resp.json()["jobId"]
        # Poll
        while True:
            status = session.get(f"{API_URL}/idmapping/status/{job_id}").json()
            if "jobStatus" not in status or status.get("jobStatus") == "FINISHED":
                break
            time.sleep(3)
        # Fetch (paginated)
        endpoint = "uniprotkb/results" if to_db == "UniProtKB" else "results"
        url = f"{API_URL}/idmapping/{endpoint}/{job_id}"
        results = []
        while url:
            r = session.get(url)
            r.raise_for_status()
            results.extend(r.json().get("results", []))
            match = re.search(r'<(.+?)>; rel="next"', r.headers.get("Link", ""))
            url = match.group(1) if match else None
        # Parse
        rows = []
        for entry in results:
            to_val = entry.get("to", "")
            if isinstance(to_val, dict):
                to_val = to_val.get("primaryAccession", to_val.get("id", str(to_val)))
            rows.append({"from": entry.get("from", ""), "to": str(to_val)})
        all_dfs.append(pd.DataFrame(rows))
        time.sleep(1)
    return pd.concat(all_dfs, ignore_index=True)
```

## Common Database Identifiers

**From** (source): `UniProtKB_AC-ID`, `Gene_Name`, `Ensembl`, `Ensembl_Protein`, `RefSeq_Protein`, `GeneID`, `PDB`

**To** (target): `Gene_Name`, `GeneID` (Entrez), `Ensembl`, `RefSeq_Protein`, `KEGG`, `STRING`, `PDB`, `GeneCards`, `UniProtKB` (returns full annotations)

Full list: https://rest.uniprot.org/configure/idmapping/fields

## Notes

- Strip isoform suffixes before mapping (`P05067-2` → `P05067`).
- One-to-many targets (GO, InterPro, PubMed) are not supported as `to_db`. Instead map to `UniProtKB` and request those as return fields via `?fields=go_id&format=tsv`.
- Obsolete/deleted accessions silently return no results.
