# Building SDK from Source

This section explains how to [build](#build_sdk_cpp) and [install](#install-artifacts) the MAVSDK C++ library from source for all our target platforms.
It also shows how to build the SDK with extensions and build the API Reference documentation.


## Build the C++ Library {#build_sdk_cpp}

This section explains how to build the SDK C++ library from source, along with its unit and integration tests.
Build artifacts are created in the **build** subdirectory.

### macOS {#build_cpp_mac_os}

First install:
* [XCode](https://developer.apple.com/xcode/) (for *clang*)
* [Homebrew](https://brew.sh/) (for *cmake*).
  Once you have installed brew, you can install *cmake* via the terminal:
  ```
  brew install cmake
  ```

Then follow the instructions for building the library on [Linux](#build_cpp_linux).

### Linux {#build_cpp_linux}

To build the MAVSDK C++ Library on Linux (or macOS after installing the [preconditions above](#build_cpp_mac_os)):

1. First install the dependencies
   ```bash
   sudo apt-get update -y
   sudo apt-get install cmake build-essential colordiff git doxygen -y
   ```
   > **Note** If the build reports a missing dependency, confirm that the set above matches the requirements in the [appropriate docker file for your platform](https://github.com/mavlink/MAVSDK/tree/{{ book.github_branch }}/docker).

1. Clone the [MAVSDK repository](https://github.com/mavlink/MAVSDK) (or your fork):
   ```sh
   git clone https://github.com/mavlink/MAVSDK.git
   cd MAVSDK
   ```
1. Checkout the release/branch you want to build (the `develop` branch is checked out by default).
   * Latest stable build (`master`):
     ```
     git checkout master
     ```
   * Head revision with latest features (`develop`):
     ```
     git checkout develop
     ```
1. Update the submodules:
   ```sh
   git submodule update --init --recursive
   ```
1. Build the (debug) C++ library by calling:
   ```sh
   cmake -DCMAKE_BUILD_TYPE=Debug -DBUILD_SHARED_LIBS=ON -Bbuild/default -H.
   cmake --build build/default
   ```
   > **Tip** You can build *release* binaries by setting `CMAKE_BUILD_TYPE=Release` as shown below:
     ```sh
     cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -Bbuild/default -H.
     cmake --build build/default
     ```
1. "Install" the MAVSDK [as described below](#install-artifacts).


### Windows {#build_cpp_windows}

To build the library in Windows, you need:

- [Build Tools for Visual Studio 2017](https://www.visualstudio.com/downloads/#58852): Download and install (only the "Visual C+ Build Tools" are needed from installer).
- [cmake](https://cmake.org/download/): Download the installer and run it.
  Make sure to tick "add to PATH" during the installation.

To build the MAVSDK C++ Library on Windows:
1. Clone the [MAVSDK repository](https://github.com/mavlink/MAVSDK) (or your fork):
   ```sh
   git clone https://github.com/mavlink/MAVSDK.git
   cd MAVSDK
   ```
1. Checkout the release/branch you want to build (the `develop` branch is checked out by default).
   * Latest stable build (`master`):
     ```
     git checkout master
     ```
   * Head revision with latest features (`develop`):
     ```
     git checkout develop
     ```
1. Update the submodules:
   ```sh
   git submodule update --init --recursive
   ```
1. Then build the MAVSDK in Windows:
   ```sh
   cd /your/path/to/MAVSDK
   cmake -G "Visual Studio 15 2017" -DBUILD_SHARED_LIBS=ON -Bbuild/default -H.
   cmake --build build/default --config Debug
   ```

   > **Tip** You can generate *release* binaries by setting `--config Release` in the build step:
     ```sh
     cmake --build build --config Release
     ```
   <span></span>
   > **Tip** To use more than one CPU core to build set this before building:
     ```sh
     set CL=/MP
     ```

1. "Install" the SDK [as described below](#install-artifacts).


## Install the SDK {#install-artifacts}

*Installing* builds the SDK **and** copies the libraries and header files into a "public" location so that they can be referenced by C++ applications (see [Building C++ Apps](../guide/toolchain.md)).
The SDK supports installation system-wide by default. You can also install files locally/relative to the MAVSDK tree if needed.

> **Warning** System-wide installation is not yet supported on Windows (see [#155](https://github.com/mavlink/MAVSDK/issues/155)) so you will need to [install the SDK locally](#sdk_local_install).
>
> Windows gurus, we'd [love your help](../README.md#getting-help) to implement this).

### System-wide Install {#sdk_system_wide_install}

System-wide installation copies the SDK headers and binaries to the standard system-wide locations for your platform (e.g. on Ubuntu Linux this is **/usr/local/**).

> **Warning** System-wide installation overwrites any previously installed version of the SDK.

To install the SDK system-wide:

```sh
sudo cmake --build build/default --target install # sudo is required to install to system directories!

# First installation only
sudo ldconfig  # update linker cache
```

> **Note** The first time you build the SDK you may also need to [update the linker cache](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html).
  On Ubuntu this is done with `sudo ldconfig`, as shown above.


### Local Install {#sdk_local_install}

Local installation copies the SDK headers/library to a user-specified location inside the SDK source directory.

On Linux/macOS use the `CMAKE_INSTALL_PREFIX` variable to specify a path relative to the folder from which you call `cmake`
(or an absolute path).
For example, to install into the **MAVSDK/install/** folder you would call:

```sh
cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=install -Bbuild/default -H.
cmake --build build/default --target install
```

> **Tip** If you already have run cmake without setting `CMAKE_INSTALL_PREFIX`, you may need to clean the build first:
     ```sh
     rm -rf build/default
     ```

## Build for Android {#build_cpp_android}

> **Tip** You must first build the C++ Library (as shown above).

To build for Android devices or simulators, you first need to install:
- [Android NDK](https://developer.android.com/ndk/downloads/index.html)
- [Android SDK](https://developer.android.com/studio/index.html)

Also, you need to set these three environment variables:

- `NDK_ROOT` to `<your-android-ndk>`
- `ANDROID_TOOLCHAIN_CMAKE` to `<your-android-ndk>/build/cmake/android.toolchain.cmake`
- `ANDROID_CMAKE_BIN` to `<your-android-sdk>/cmake/<version>/bin/cmake`

E.g. you can add the lines below to your .bashrc, (or .zshrc for zshell users, or .profile):

```sh
export NDK_ROOT=$HOME/Android/android-ndk-r13
export ANDROID_TOOLCHAIN_CMAKE=$HOME/Android/android-ndk-r13/build/cmake/android.toolchain.cmake
export ANDROID_CMAKE_BIN=$HOME/Android/Sdk/cmake/3.6.3155560/bin/cmake
```

Then you build for all Android architectures:
```sh
make android install
```


## Build for iOS {#build_cpp_iOS}

> **Warning** You must first build the C++ Library (as shown above).

To build for real iOS devices on macOS:

```sh
cmake -DCMAKE_TOOLCHAIN_FILE=tools/ios.toolchain.cmake -Bbuild/ios -H.
cmake --build build/ios
```

Build for the iOS simulator on macOS:

```sh
cmake -DCMAKE_TOOLCHAIN_FILE=tools/ios.toolchain.cmake -DPLATFORM=SIMULATOR64 -Bbuild/ios_simulator -H.
```

## Build SDK Extensions {#sdk_extensions}

The MAVSDK can be extended with plugins and integration tests that are defined "out of tree". These are declared inside a parallel directory that is included into the SDK at compile time (by specifying `EXTERNAL_DIR` in the `make` command).

The commands to build the SDK with an extension library are:
```sh
cmake -DEXTERNAL_DIR=relative_path_to_external_directory -Dbuild/default -H.
cmake --build build/default
```
See [SDK Extensions](../guide/sdk_extensions.md) for more information.

## Building the Backend {#build_backend}

The MAVSDK programming-language-specific libraries (e.g. [Swift](http://dronecode-sdk-swift.s3.eu-central-1.amazonaws.com/docs/master/index.html), [Python](https://github.com/mavlink/MAVSDK-Python#dronecodesdk-python)) share a common backend, which may optionally be built as part of the C++ library.

The cmake configuration step additionally depends on the `-DBUILD_BACKEND=ON` option. Otherwise the build is exactly the same as usual. 

> **Tip** When building the backend, we usually like to link all the dependencies statically, and therefore we set `-DBUILD_SHARED_LIBS=OFF` (or don't specify it, because the default is `OFF`).

### Ubuntu {#build_backend_ubuntu}

To build the backend on Ubuntu:
1. [Setup the C++ Library on Linux](#build_cpp_linux)
1. Navigate into the SDK directory and build the project
   ```
   cd MAVSDK
   cmake -DBUILD_BACKEND=ON -Bbuild/default -H.
   cmake --build build/default
   ```

### macOS {#build_backend_macos}

To build the backend on macOS:
1. [Setup the C++ Library on macOS](#build_cpp_mac_os)
1. Navigate into the SDK directory and build the project
   ```
   cd MAVSDK
   cmake -DBUILD_BACKEND=ON -Bbuild/default -H.
   cmake --build build/default
   ```

### Windows {#build_backend_windows}

To build the backend on Windows:
1. [Setup the C++ Library on Windows(#build_cpp_windows)
1. Navigate into the SDK directory and build the project
   ```
   cmake -G "Visual Studio 15 2017" -DBUILD_BACKEND=ON -Bbuild/default -H.
   cmake --build build/default
   ```

## Build API Reference Documentation {#build_api_reference}

The C++ source code is annotated using comments using [Doxygen](http://doxygen.nl/manual/index.html) syntax.

Extract the documentation to markdown files (one per class) on macOS or Linux using the commands:
```bash
rm -rf tools/docs # Remove previous docs
./tools/generate_docs.sh
```
The files are created in **/install/docs/markdown**.

> **Note** Extracting the API reference does not yet work automatically on Windows.

<span></span>
> **Note** The *generate_docs.sh* script [builds the library](../contributing/build.md), installs it locally to **/install**, and then uses *DOxygen* to create XML documentation in **/install/docs/xml**.
> The [generate_markdown_from_doxygen_xml.py](https://github.com/mavlink/MAVSDK/blob/{{ book.github_branch }}/generate_markdown_from_doxygen_xml.py) script
> is then run on all files in the */xml* directory to generate markdown files in **/install/docs/markdown**.


## Troubleshooting

The vast majority of common build issues can be resolved by updating submodules and cleaning the distribution:
```
cd MAVSDK
git submodule update --recursive
rm -rf build
```

Then attempt to build again.
