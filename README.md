# SHAPE5: Specification for HDF5 Analysis, Parsing, and Encoding

`SHAPE5` is a machine-readable specification of the HDF5 file format using [GNU poke](https://www.jemarch.net/poke/) pickles.

The goal is to describe HDF5 on-disk structures as executable binary format definitions that can be loaded in GNU poke to inspect, validate, and reason about HDF5 files.

This repository is a work in progress. The current pickles focus on core HDF5 metadata structures, including the superblock, B-trees, object headers, and related messages.

## Repository Layout - Pickles

- [`pickles/common.pk`](pickles/common.pk): shared helpers and common definitions
- [`pickles/superblock.pk`](pickles/superblock.pk): HDF5 superblock definitions
- [`pickles/btree.pk`](pickles/btree.pk): B-tree definitions
- [`pickles/ohdr.pk`](pickles/ohdr.pk): object header definitions
- [`pickles/messages.pk`](pickles/messages.pk): object header message definitions
- [`pickles/lookup3.pk`](pickles/lookup3.pk): implementation of the lookup3 hash function used for checksums
- [`pickles/construct.pk`](pickles/construct.pk): helpers for constructing version 2 metadata in memory

## Acknowledgments

> This material is based upon work supported by the U.S. National Science Foundation under Federal Award No. 2534078. Any opinions, findings, and conclusions or recommendations expressed in this material are those of the author(s) and do not necessarily reflect the views of the National Science Foundation.
