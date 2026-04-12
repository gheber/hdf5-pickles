# Marker List

The `marker_scan` tool uses the concrete on-disk markers explicitly defined in the [HDF5 file format specification](https://support.hdfgroup.org/documentation/hdf5/latest/_f_m_t4.html), as well as the markers defined in the [Onion revision-history format](https://support.hdfgroup.org/releases/hdf5/documentation/rfc/Onion_VFD_RFC_211122.pdf).

Unless noted otherwise, each 4-character marker matches its own name spelled out as
literal ASCII bytes (e.g. `TREE` matches the four bytes `54 52 45 45`).

## HDF5 file format markers

- `HDF5_SIGNATURE` = `89 48 44 46 0D 0A 1A 0A`
- `TREE`
- `BTHD`
- `BTIN`
- `BTLF`
- `SNOD`
- `HEAP`
- `GCOL`
- `FRHP`
- `FHDB`
- `FHIB`
- `FSHD`
- `FSSE`
- `SMTB`
- `SMLI`
- `OHDR`
- `OCHK`
- `FAHD`
- `FADB`
- `EAHD`
- `EAIB`
- `EASB`
- `EADB`

## Onion revision-history markers

- `OHDH`
- `OWHR`
- `ORRS`

