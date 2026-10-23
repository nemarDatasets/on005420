[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on005420-blue)](https://doi.org/10.82901/nemar.on005420)

References
----------
Appelhoff, S., Sanderson, M., Brooks, T., Vliet, M., Quentin, R., Holdgraf, C., Chaumon, M., Mikulan, E., Tavabi, K., Höchenberger, R., Welke, D., Brunner, C., Rockhill, A., Larson, E., Gramfort, A. and Jas, M. (2019). MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. Journal of Open Source Software 4: (1896). https://doi.org/10.21105/joss.01896

Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., Oostenveld, R. (2019). EEG-BIDS, an extension to the brain imaging data structure for electroencephalography. Scientific Data, 6, 103. https://doi.org/10.1038/s41597-019-0104-8


## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 1 error + 1226 warnings to 0 errors + 1154 warnings. None of the raw `.edf` files were modified — every change is to a text sidecar.

**Participants table (`participants.tsv`)**
- The `sex` column was filled in as `F` for every row. The cells were previously `None`, which is not one of the declared levels (`F`, `M`) in `participants.json` and so was flagged as an invalid value. The dataset title states the cohort is "females from 60 to 80 years old", so `F` is the only defensible value for every participant. The other per-subject columns (`age`, `hand`, `weight`, `height`) were left as `n/a` because individual values are not documented anywhere in the dataset.

**Recording sidecars (`_eeg.json`, all 72 recordings)**
- The channel-count field was spelled `MiscChannelCount`; BIDS uses the all-uppercase `MISCChannelCount`. The key was renamed (the value was already correct) so the validator recognizes it on every recording instead of repeatedly warning that the field is missing.

**Acquisition times (scans.tsv, all 37 recordings) — left exactly as published**
- EEGDash's loader stripped the trailing `Z` (UTC indicator) from every `acq_time` cell, but the published values (e.g. `2016-10-14T12:37:25.000000Z`) are already valid BIDS, so they were restored to the source form rather than having the loader's stripped output baked in.

**Dataset description (`dataset_description.json`)**
- Updated `BIDSVersion` from `1.7.0` to `1.11.1` (the version the current validator checks against).
- `GeneratedBy` was left absent, exactly as the source published it — nothing was added there.

**Remaining warnings (1154) — left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that isn't in the dataset: manufacturer name and model, software versions, device serial number, cap manufacturer and model, hardware filters, head circumference, subject-artefact description, instructions, institution name and address and department, cognitive-atlas IDs, task description, and HED version (the dataset has no HED tags). `GeneratedBy` is among these remaining warnings — it is recommended but not required, and was left absent to reflect what the source actually published rather than inventing provenance.
- The 72 "events file missing" warnings are also kept on purpose: the recordings are resting state (eyes open and eyes closed) with no per-event annotations, so an empty events file would add noise without information.
- Per the curation policy (only apply fixes that are 100% defensible from existing dataset content), these gaps are left for the original authors to supply.
