# Building libarchive for use in MOBILedit (Windows / Visual Studio)

This guide explains how to build **libarchive** on Windows using **Visual Studio** and a set of manually built external dependencies.

## Dependencies

The following libraries must be built from source:
- [BZip2](https://github.com/Compelson/bzip2)
- [LibLZMA (XZ Utils)](https://github.com/Compelson/xz)
- [LZ4](https://github.com/Compelson/lz4)
- [ZSTD](https://github.com/Compelson/zstd)

Additional prebuilt libraries required from `ME_SUPPORT`:
- ZLIB
- OpenSSL
- Expat

## 1. Build External Libraries

For each of the source-based dependencies listed above:

1. Clone the repository.
2. Checkout the `compelson` branch.
3. Open the project in **Visual Studio**.
4. Verify that Visual Studio loads correct configurations from `CMakePresets.json`
5. Configure, build and install the project (Debug, Release).
6. Check the resulting artifacts in `out\install\<configuration>`:
   - `include/*.h` header files
   - `lib/*.lib` library files
   - `lib/*.pdb` debug symbol files

## 2. Prepare the `external` Directory

In the **root of the libarchive project**, create the following directory structure:

```
external/
├── debug/
│   ├── include/
│   └── lib/
└── release/
    ├── include/
    └── lib/
```

## 3. Populate the `external` Directory

Copy the artifacts from step 1 into the matching folders:

- Headers → `external/<config>/include/`
- Libraries, PDBs → `external/<config>/lib/`

Ensure the include directory structure matches CMake expectations.

Next, copy **ZLIB**, **OpenSSL**, and **Expat** from `ME_SUPPORT` into the same structure and rename the files if needed so CMake can detect them (`zlib.lib`, `libssl.lib`, `expat.lib`, etc.).

## 4. Configure and Build libarchive

1. Open the libarchive project in **Visual Studio**.
2. Configure the project (Debug, Release). 
3. Verify that CMake detects all dependencies.
4. Build and install the project (Debug, Release).
