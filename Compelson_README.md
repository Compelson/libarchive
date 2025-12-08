# Building libarchive for use in MOBILedit (Windows / Visual Studio)

This guide explains how to build **libarchive** on Windows using **Visual Studio** and a set of manually built external dependencies.

## Dependencies

The following libraries must be built from source:
- [BZip2](https://github.com/Compelson/bzip2)
- [LibLZMA (XZ Utils)](https://github.com/Compelson/xz)
- [LZ4](https://github.com/Compelson/lz4)
- [ZSTD](https://github.com/Compelson/zst)

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

Next, copy **ZLIB**, **OpenSSL**, and **Expat** from `ME_SUPPORT` into the same structure and rename the files if needed so CMake can detect them (`zlib.lib`, `libssl.lib`, `expat.lib`, etc.). See this [Example Script](#example-script-for-copying-external-libraries).

## 4. Configure and Build libarchive

1. Open the libarchive project in **Visual Studio**.
2. Configure the project (Debug, Release). 
3. Verify that CMake detects all dependencies.
4. Build and install the project (Debug, Release).


## Example Script for Copying External Libraries

```cmd
@ECHO OFF
echo Configuring libarchive for MOBILedit

setlocal
setlocal ENABLEEXTENSIONS

cd %~dp0

set BZIP2="..\bzip2"
set XZ="..\xz"
set LZ4="..\lz4\build\cmake"
set ZSTD="..\zstd\build\cmake"

set EXTERNAL="external\"
mkdir "%EXTERNAL%\include"
mkdir "%EXTERNAL%\lib"
mkdir "%EXTERNAL%\include"
mkdir "%EXTERNAL%\lib"

REM ZLIB
xcopy /V /Y "%ME_SUPPORT%\Include\zlib\*.*" "%EXTERNAL%\include\"
copy  /V /Y "%ME_SUPPORT%\Lib\zlib1x64vc143.lib" "%EXTERNAL%\lib\zlib.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\zlib1x64vc143d.lib" "%EXTERNAL%\lib\zlibd.lib"

REM EXPAT
xcopy /V /Y "%ME_SUPPORT%\Include\expat\*.*" "%EXTERNAL%\include\"
copy  /V /Y "%ME_SUPPORT%\Lib\libExpatx64vc143.lib" "%EXTERNAL%\lib\libexpat.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\libExpatx64vc143.pdb" "%EXTERNAL%\lib\libexpat.pdb"
copy  /V /Y "%ME_SUPPORT%\Lib\libExpatx64vc143d.lib" "%EXTERNAL%\lib\libexpatd.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\libExpatx64vc143d.pdb" "%EXTERNAL%\lib\libexpatd.pdb"

REM OPENSSL
xcopy /V /Y "%ME_SUPPORT%\Include\OpenSSL\*.*" "%EXTERNAL%\include\openssl\" 
copy  /V /Y "%ME_SUPPORT%\Lib\libcrypto-1_1x64vc143.lib" "%EXTERNAL%\lib\libcrypto.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\libssl-1_1x64vc143.lib" "%EXTERNAL%\lib\libssl.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\libcrypto-1_1x64vc143d.lib" "%EXTERNAL%\lib\libcryptod.lib"
copy  /V /Y "%ME_SUPPORT%\Lib\libssl-1_1x64vc143d.lib" "%EXTERNAL%\lib\libssld.lib"

REM BZ2
copy /V /Y "%BZIP2%\out\install\windows-x64-debug\include\bzlib.h" "%EXTERNAL%\include\bzlib.h"
copy /V /Y "%BZIP2%\out\install\windows-x64-debug\lib\bz2_static.lib" "%EXTERNAL%\lib\bz2d.lib"
copy /V /Y "%BZIP2%\out\install\windows-x64-debug\lib\bz2_static.pdb" "%EXTERNAL%\lib\bz2d.pdb"
copy /V /Y "%BZIP2%\out\install\windows-x64-release\lib\bz2_static.lib" "%EXTERNAL%\lib\bz2.lib"
copy /V /Y "%BZIP2%\out\install\windows-x64-release\lib\bz2_static.pdb" "%EXTERNAL%\lib\bz2.pdb"

REM XZ
xcopy /V /Y "%XZ%\out\install\windows-x64-debug\include\lzma\*.*" "%EXTERNAL%\include\lzma\" 
copy  /V /Y "%XZ%\out\install\windows-x64-debug\include\lzma.h" "%EXTERNAL%\include\lzma.h"
copy  /V /Y "%XZ%\out\install\windows-x64-debug\lib\lzma.lib" "%EXTERNAL%\lib\lzmad.lib"
copy  /V /Y "%XZ%\out\install\windows-x64-debug\lib\lzma.pdb" "%EXTERNAL%\lib\lzmad.pdb"
copy  /V /Y "%XZ%\out\install\windows-x64-release\lib\lzma.lib" "%EXTERNAL%\lib\lzma.lib"
copy  /V /Y "%XZ%\out\install\windows-x64-release\lib\lzma.pdb" "%EXTERNAL%\lib\lzma.pdb"

REM LZ4
xcopy /V /Y "%LZ4%\out\install\windows-x64-debug\include\*.*" "%EXTERNAL%\include\" 
copy  /V /Y "%LZ4%\out\install\windows-x64-debug\lib\lz4.lib" "%EXTERNAL%\lib\lz4d.lib"
copy  /V /Y "%LZ4%\out\install\windows-x64-debug\lib\lz4.pdb" "%EXTERNAL%\lib\lz4d.pdb"
copy  /V /Y "%LZ4%\out\install\windows-x64-release\lib\lz4.lib" "%EXTERNAL%\lib\lz4.lib"
copy  /V /Y "%LZ4%\out\install\windows-x64-release\lib\lz4.pdb" "%EXTERNAL%\lib\lz4.pdb"

REM ZSTD
xcopy /V /Y "%ZSTD%\out\install\windows-x64-debug\include\*.*" "%EXTERNAL%\include\" 
copy  /V /Y "%ZSTD%\out\install\windows-x64-debug\lib\zstd_static.lib" "%EXTERNAL%\lib\zstdd.lib"
copy  /V /Y "%ZSTD%\out\install\windows-x64-debug\lib\zstd_static.pdb" "%EXTERNAL%\lib\zstdd.pdb"
copy  /V /Y "%ZSTD%\out\install\windows-x64-release\lib\zstd_static.lib" "%EXTERNAL%\lib\zstd.lib"
copy  /V /Y "%ZSTD%\out\install\windows-x64-release\lib\zstd_static.pdb" "%EXTERNAL%\lib\zstd.pdb"

endlocal
echo.
echo libarchive configure done
```