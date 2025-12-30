# Priority list for developing pickles of file format primitives
An HDF5 file is made up of the following objects:
- A superblock
- Object headers (prefix + messages)
- Object data
- B-tree nodes
- Indexes for chunked datasets
- Heap blocks
- Free space

I plan to create pickles for the format specification in the following order and they will be pushed to the repository https://github.com/HDFGroup/hdf5-pickles/ on completion.  The ordering is tentative and will be adjusted accordingly as some objects may be intertwined with each other.

1. Superblock: there are 4 versions of the superblocks (DONE)
1. Object header: it is composed of a prefix and a set of messages:
    * There are two versions of the object header prefixes—version 1 and version 2
    * There is a total of 24 object header messages.  I will tackle the following 12 messages first that are more widely used for objects:
        1. Dataspace message
        2. Link info message
        3. Datatype message
        4. Link message
        5. Data layout message
        6. Group info message
        7. Filter pipeline message
        8. Attribute message
        9. Object header continuation message
        10. Symbol table message
        11. B-tree ‘K’ values message
        12. Attribute info message
1. Version 1 and 2 B-trees
1. Group symbol table nodes
1. The 5 chunk indexes
1. The remaining object header messages
1. Local heap/global heap/fractal heap
1. Free-space manager
1. Shared object header message table



