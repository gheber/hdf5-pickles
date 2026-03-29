# SHAPE5: Specification for HDF5 Analysis, Parsing, and Encoding

`SHAPE5` is a machine-readable specification of the HDF5 file format using [GNU poke](https://www.jemarch.net/poke/) pickles.

The goal is to describe HDF5 on-disk structures as executable binary format definitions that can be loaded in GNU poke to inspect, validate, and reason about HDF5 files.

This repository is a work in progress. The current pickles focus on core HDF5 metadata structures, including the superblock, object headers, and related messages.

## Repository Layout

- `pickles/common.pk`: shared helpers and common definitions
- `pickles/superblock.pk`: HDF5 superblock definitions
- `pickles/ohdr.pk`: object header definitions
- `pickles/messages.pk`: object header message definitions
- `pickles/lookup3.pk`: implementation of the lookup3 hash function used for checksums

## Quick Tutorial

This short tutorial shows how to explore the sample HDF5 file `file.h5` from the poke REPL using the pickles in this repository. It assumes GNU poke is installed and that you start from the repository root.

Start poke with the repository `pickles/` directory on the load path:

```sh
cd <THIS DIRECTORY>
POKE_LOAD_PATH=$PWD/pickles poke file.h5
```

At the `(poke)` prompt, load the pickles needed for the superblock and object headers:

```poke
load common
load superblock
load ohdr
load lookup3
```

These commands do not print anything on success; poke simply returns to the prompt.

### 1. Map the superblock

The HDF5 superblock begins at byte offset `0`:

```poke
var sb = superblock @ 0#B
sb.super_vers
var root_addr = bytes_to_off (sb.super.v2_v3.root_obj_addr_raw)
root_addr
```

Expected output:

```text
(poke) sb.super_vers
2UB
(poke) root_addr
48UL#B
```

This tells us that `file.h5` uses a version 2 superblock and that the root object header starts at byte offset `48`.

### 2. Decode the root object header

```poke
var root = ohdr @ root_addr
root
```

Expected output snippet:

```text
ohdr {sig_peek=[79UB,72UB,68UB,82UB],_ohdr=struct {v2=struct {signature=[79UB,72UB,68UB,82UB],version=2UB,flags=32UB,timestamps=Timestamps {access=1773447782U,modification=1773447782U,change=1773447782U,birth=1773447782U},chunk0_size=[120UB],_msg_chunk=struct {msg_chunk=[2UB,18UB,0UB,0UB,0UB,0UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB,10UB,2UB,0UB,1UB,0UB,0UB,6UB,26UB,0UB,0UB,1UB,0UB,15UB,68UB,105UB,114UB,101UB,99UB,116UB,67UB,104UB,117UB,110UB,107UB,68UB,97UB,116UB,97UB,195UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,58UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB]},chksum=[7UB,68UB,33UB,252UB]}}}
```

We are looking at a version 2 object header. Unlike the earlier version, it comes with a checksum. You can verify the checksum with the `lookup3_hashlittle` function from `lookup3.pk`:

```poke
lookup3_u32_le(root._ohdr.v2.chksum)
```

Expected output:

```text
4230038535U
```

Let's calculate the checksum ourselves to see how it works. The checksum is computed over the *entire object header* (including the prefix) except for the checksum field ( 4 bytes) itself, which is located at the end of the header.

```poke
lookup3_hashlittle(byte[root'size as offset<uint<64>,B> - 4UL#B] @ root_addr, 0)
```

Expected output:

```text
4230038535U
```

Phew! This confirms that the checksum is correct and that we understand how to compute it.

Print the root object header's decoded messages:

```poke
root.get_messages ()
```

Expected output snippet:

```text
Message 0...
msg_prefix {
  v2_msg_prefix=struct {
    msg_type=2UB,
    msg_size=18UH,
    msg_flags=0UB
  }
}
H5O_msg_linfo {
  version=0UB,
  flags=0UB,
  fheap_addr_raw=[255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB],
  name_bt2_addr_raw=[255UB,255UB,255UB,255UB,255UB,255UB,255UB,255UB]
}

Message 2...
msg_prefix {
  v2_msg_prefix=struct {
    msg_type=6UB,
    msg_size=26UH,
    msg_flags=0UB
  }
}
H5O_msg_link {
  version=1UB,
  flags=0UB,
  lnk_name=[68UB,105UB,114UB,101UB,99UB,116UB,67UB,104UB,117UB,110UB,107UB,68UB,97UB,116UB,97UB],
  ohdr_addr_raw=[195UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB]
}
```

The interesting part here is the link message: the byte array in `lnk_name` is the ASCII string `DirectChunkData`, and `ohdr_addr_raw` points to the child object header at byte offset `195`.

### 3. Follow the link to the dataset object header

Now map that child object header and decode its messages:

```poke
var dset = ohdr @ 195#B
dset.get_messages ()
```

Expected output snippet:

```text
Message 0...
H5O_msg_sdspace {
  version=2UB,
  space=struct {
    v2=struct {
      ndims=2UB,
      flags=1UB,
      space_type=1UB,
      dim_size=[8UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB],
      max=[8UB,0UB,0UB,0UB,0UB,0UB,0UB,0UB]
    }
  }
}

Message 1...
H5O_msg_dtype {
  hdr=dtype_hdr {
    flags=2064U,
    elm_size=4U
  },
  types=struct {
    fixed_point=struct {
      bit_offset=0UH,
      bit_precision=32UH
    }
  }
}

Message 4...
H5O_msg_layout {
  version=3UB,
  layout=struct {
    v3=struct {
      layout_class=2UB,
      properties=struct {
        chunked=struct {
          ndims=3UB,
          idx_addr_raw=[223UB,1UB,0UB,0UB,0UB,0UB,0UB,0UB],
          dim_size=[4U,4U,4U]
        }
      }
    }
  }
}
```

This shows the sort of machine-readable structure the pickles expose: dataspace, datatype, filter pipeline, and layout information are all decoded directly from the file.

### 4. Inspect the available types

You can also ask poke what each pickle defines:

```poke
.info type superblock
.info type ohdr
```

This is useful when extending the pickles or when you want to discover methods such as `get_messages ()` directly from the REPL.

### 5. Try a write on a disposable copy

Poke maps are writable. To avoid modifying the sample file in the repository, make a copy first:

```sh
cp file.h5 file-edit.h5
POKE_LOAD_PATH=$PWD/pickles poke file-edit.h5
```

Then edit a scalar field through the mapped object header:

```poke
load common
load ohdr
var root = ohdr @ 48#B
root._ohdr.v2.timestamps.birth
root._ohdr.v2.timestamps.birth = 0U
root._ohdr.v2.timestamps.birth
```

Expected output:

```text
(poke) root._ohdr.v2.timestamps.birth
1773447782U
(poke) root._ohdr.v2.timestamps.birth = 0U
(poke) root._ohdr.v2.timestamps.birth
0U
```

This demonstrates byte-level write-through via the mapped pickle types. It does not automatically update higher-level HDF5 consistency metadata, so for real edits you may also need to recompute dependent fields such as checksums.

## Acknowledgments

> This material is based upon work supported by the U.S. National Science Foundation under Federal Award No. 2534078. Any opinions, findings, and conclusions or recommendations expressed in this material are those of the author(s) and do not necessarily reflect the views of the National Science Foundation.
