# Marker List

The `marker_scan` tool uses the concrete on-disk markers explicitly defined in the [HDF5 file format specification](https://support.hdfgroup.org/documentation/hdf5/latest/_f_m_t4.html), as well as the markers defined in the [Onion revision-history format](https://support.hdfgroup.org/releases/hdf5/documentation/rfc/Onion_VFD_RFC_211122.pdf).

Unless noted otherwise, each 4-character marker matches its own name spelled out as
literal ASCII bytes (e.g. `TREE` matches the four bytes `54 52 45 45`).

## HDF5 file format markers

- `HDF5_SIGNATURE` = `89 48 44 46 0D 0A 1A 0A` - HDF5 file signature
- `TREE` - Version 1 B-tree node
- `BTHD` - Version 2 B-tree header
- `BTIN` - Version 2 B-tree internal node
- `BTLF` - Version 2 B-tree leaf node
- `SNOD` - Symbol table node
- `HEAP` - Local heap
- `GCOL` - Global heap collection
- `FRHP` - Fractal heap header
- `FHDB` - Fractal heap direct block
- `FHIB` - Fractal heap indirect block
- `FSHD` - Free-space manager header
- `FSSE` - Free-space section information
- `SMTB` - Shared Object Header Message table
- `SMLI` - Shared message record list
- `OHDR` - Version 2 object header
- `OCHK` - Version 2 object header continuation block
- `FAHD` - Fixed Array header
- `FADB` - Fixed Array data block
- `EAHD` - Extensible Array header
- `EAIB` - Extensible Array index block
- `EASB` - Extensible Array secondary block
- `EADB` - Extensible Array data block

## Onion revision-history markers

- `OHDH` - Onion History Data Header
- `OWHR` - Onion Whole-History Record
- `ORRS` - Onion Revision Record Signature
