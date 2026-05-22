[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on005420-blue)](https://doi.org/10.82901/nemar.on005420)

References
----------
Appelhoff, S., Sanderson, M., Brooks, T., Vliet, M., Quentin, R., Holdgraf, C., Chaumon, M., Mikulan, E., Tavabi, K., Höchenberger, R., Welke, D., Brunner, C., Rockhill, A., Larson, E., Gramfort, A. and Jas, M. (2019). MNE-BIDS: Organizing electrophysiological data into the BIDS format and facilitating their analysis. Journal of Open Source Software 4: (1896). https://doi.org/10.21105/joss.01896

Pernet, C. R., Appelhoff, S., Gorgolewski, K. J., Flandin, G., Phillips, C., Delorme, A., Oostenveld, R. (2019). EEG-BIDS, an extension to the brain imaging data structure for electroencephalography. Scientific Data, 6, 103. https://doi.org/10.1038/s41597-019-0104-8


## NEMAR curation changes (2026-05-21)

BIDS validator: 1 error + 1226 warnings -> 0 errors + 1153 warnings. Raw `.edf` binary payloads unchanged.

### `sub-*/sub-*_scans.tsv` (36 files)
- Removed trailing `Z` UTC suffix from `acq_time` cells (e.g. `2016-10-14T12:37:25.000000Z` -> `2016-10-14T12:37:25.000000`). Why: BIDS-spec `acq_time` is a local datetime without timezone designator; the trailing `Z` is non-canonical and causes downstream loaders (mne-bids / EEGDash) to repair the file on every read.

### `participants.tsv`
- Set `sex` column from `None` to `F` for all 36 participants. Why: closes the `TSV_VALUE_INCORRECT_TYPE:sex` error (the value `None` did not match the declared `Levels: {F, M}` in `participants.json`). The dataset's own `dataset_description.json` title states "EEG ... in females from 60 to 80 years old", which makes `F` the only defensible value for every row. Other columns (`age`, `hand`, `weight`, `height`) remain `n/a` because per-subject values are not documented in the dataset.

### `dataset_description.json`
- Bumped `BIDSVersion` from `1.7.0` to `1.8.0`. Why: 1.8.0 is the current stable BIDS schema; pre-1.8 versions fire schema-mismatch warnings under newer validators.
- Added `GeneratedBy: [{"Name": "nemar-cli", "Version": "0.8.8", "CodeURL": "https://github.com/nemar-org/nemar-cli"}]`. Why: closes `JSON_KEY_RECOMMENDED:GeneratedBy` and records the curation provenance for this rehost pass. `DatasetType: "raw"` was already present, so derivative-rules cascade was not a risk.

### `sub-*/eeg/sub-*_task-*_eeg.json` (72 files)
- Renamed key `MiscChannelCount` (camelCase) to `MISCChannelCount` (BIDS-canonical all-uppercase). Why: BIDS spec uses `MISCChannelCount`; the validator did not recognise the camelCase key and continued to warn the field was missing on every recording (72 warnings closed in one pass).

### Tier-C warnings intentionally left in place
1153 `SIDECAR_KEY_RECOMMENDED` / `JSON_KEY_RECOMMENDED` / `EVENTS_TSV_MISSING` warnings remain. They require external information not present anywhere in the dataset:
- Equipment / cap: `ManufacturersModelName`, `SoftwareVersions`, `DeviceSerialNumber`, `CapManufacturer`, `CapManufacturersModelName`.
- Method: `HardwareFilters`, `HeadCircumference`.
- Subject narrative: `SubjectArtefactDescription`, `Instructions`.
- Institution: `InstitutionName`, `InstitutionAddress`, `InstitutionalDepartmentName`.
- Ontology: `CogAtlasID`, `CogPOID`.
- Task: `TaskDescription`.
- HED: `HEDVersion` (the dataset has no HED tags).
- `EVENTS_TSV_MISSING` x 72: the recordings are resting state (task-oa = eyes open, task-oc = eyes closed); no per-event annotations exist. Generating an empty events.tsv would add noise without information.

Per the curation policy (only apply fixes with 100% defensibility from existing dataset content), these are not filled and are left for the original authors to supply.
