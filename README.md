# Header-only Linked List in C

This is a header-only linked list library written in C.

## Table of Contents

1. [About the Project](#about-the-project)
2. [Project Status](#project-status)
3. [Getting Started](#getting-started)
    1. [Requirements](#requirements)
        1. [git-lfs](#git-lfs)
        1. [Meson Build System](#meson-build-system)
    2. [Getting the Source](#getting-the-source)
    3. [Building](#building)
4. [Configuration Options](#configuration-options)
5. [Documentation](#documentation)
6. [Need Help?](#need-help)
7. [Contributing](#contributing)
8. [Further Reading](#further-reading)
9. [Authors](#authors)
10. [License](#license)
11. [Acknowledgments](#acknowledgements)

# About the Project

This is a header-only linked list library implemented in C. You can copy this header to your own projects, or you can consume this repository as a Meson subproject. With Meson, the header can be added to the relevant build components with the `c_linked_list_dep` variable.

To use this library, embed an `ll_t` element in a structure:

```c
typedef struct
{
    ll_t node;
    size_t size;
    char* block;
} alloc_node_t;
```

You then need to declare a linked list variable using the `LIST_INIT` macro:

```c
LIST_INIT(free_list);
```

Operations on the list primarily operate through your `ll_t` `struct` element.

To add a new element to the list, you can use `list_add` (or `list_add_tail`, or `list_insert`) to add the `ll_t` element to the desired list variable.

```c
// Note the pointer to the node element!
list_add(&new_memory_block->node, &free_list);
```

Removing an element only requires the `node` variable:

```c
list_del(&found_block->node);
```

Functions for iterating over the list are also provided:

```c
// Declare a variable to hold a pointer to the current element 
// in the processing loop
alloc_node_t* current_block = NULL;

// Iterate over each element in the list
// First param is a variable in your struct type
// Second param is the list to iterate over
// Third param is the ll_t element in your struct type.
list_for_each_entry(current_block, &free_list, node)
{
    // perform an operation on current_block
}
```

Full documentation and a complete list of available functions can be found in the `ll.h` file.

For an full example of this library in action, see [embeddedartistry/libmemory](https://github.com/embeddedartistry/libmemory) and the ["freelist" implementation](https://github.com/embeddedartistry/libmemory/blob/master/src/malloc_freelist.c).

**[Back to top](#table-of-contents)**

# Project Status

This header implementation has been constant for years.

Now that this header is a standalone repository, I would like to add test cases.

**[Back to top](#table-of-contents)**

## Getting Started

### Requirements

This project uses [Embedded Artistry's standard Meson build system](https://embeddedartistry.com/fieldatlas/embedded-artistrys-standardized-meson-build-system/), and dependencies are described in detail [on our website](https://embeddedartistry.com/fieldatlas/embedded-artistrys-standardized-meson-build-system/).

At a minimum you will need:

* [`git-lfs`](https://git-lfs.github.com), which is used to store binary files in this repository
* [Meson](#meson-build-system) is the build system
* Some kind of compiler for your target system.
    - This repository has been tested with:
        - gcc-7, gcc-8, gcc-9
        - arm-none-eabi-gcc
        - Apple clang
        - Mainline clang

#### git-lfs

This project stores some files using [`git-lfs`](https://git-lfs.github.com).

To install `git-lfs` on Linux:

```
sudo apt install git-lfs
```

To install `git-lfs` on OS X:

```
brew install git-lfs
```

Additional installation instructions can be found on the [`git-lfs` website](https://git-lfs.github.com).

#### Meson Build System

The [Meson](https://mesonbuild.com) build system depends on `python3` and `ninja-build`.

To install on Linux:

```
sudo apt-get install python3 python3-pip ninja-build
```

To install on OSX:

```
brew install python3 ninja
```

Meson can be installed through `pip3`:

```
pip3 install meson
```

If you want to install Meson globally on Linux, use:

```
sudo -H pip3 install meson
```

**[Back to top](#table-of-contents)**

### Getting the Source

This project uses [`git-lfs`](https://git-lfs.github.com), so please install it before cloning. If you cloned prior to installing `git-lfs`, simply run `git lfs pull` after installation.

This project is hosted on GitHub. You can clone the project directly using this command:

```
git clone --recursive git@github.com:embeddedartistry/project-skeleton.git
```

If you don't clone recursively, be sure to run the following command in the repository or your build will fail:

```
git submodule update --init
```

**[Back to top](#table-of-contents)**

### Building

If Make is installed, the library can be built by issuing the following command:

```
make
```

This will build all targets for your current architecture.

You can clean builds using:

```
make clean
```

You can eliminate the generated `buildresults` folder using:

```
make distclean
```

You can also use  `meson` directly for compiling.

Create a build output folder:

```
meson buildresults
```

And build all targets by running

```
ninja -C buildresults
```

Cross-compilation is handled using `meson` cross files. Example files are included in the [`build/cross`](build/cross/) folder. You can write your own cross files for your specific processor by defining the toolchain, compilation flags, and linker flags. These settings will be used to compile the project.

Cross-compilation must be configured using the meson command when creating the build output folder. For files stored within `build/cross`, we provide a Makefile `CROSS` to simplify the process. This variable will automatically supply the proper Meson argument, `build/cross/` prefix, and `.txt` filename extension.

You can use a single file, or you can layer multiple files by separating the names with a colon.

```
make CROSS=arm:cortex-m4_hardfloat
```

You can also do this manually with the Meson interface. Note, however, that you will need to include a special `--cross-file=build/cross/embvm.txt` cross file to ensure that the required Embedded VM settings are applied.

```
meson buildresults --cross-file build/cross/arm.txt --cross-file build/cross/cortex-m4_hardfloat.txt --cross-file=build/cross/embvm.txt
```

Following that, you can run `make` (at the project root) or `ninja -C buildresults` to build the project.

> **Note:** Tests will not be cross-compiled. They will only be built for the native platform.

**Full instructions for working with the build system, including topics like using alternate toolchains and running supporting tooling, are documented in [Embedded Artistry's Standardized Meson Build System](https://embeddedartistry.com/fieldatlas/embedded-artistrys-standardized-meson-build-system/) on our website.**

**[Back to top](#table-of-contents)**

### Testing

The tests for this library are written with CMocka, which is included as a subproject and does not need to be installed on your system. You can run the tests by issuing the following command:

```
make test
```

By default, test results are generated for use by the CI server and are formatted in JUnit XML. The test results XML files can be found in `buildresults/test/`.

**[Back to top](#table-of-contents)**

## Configuration Options

The following meson project options can be set for this library when creating the build results directory with `meson`, or by using `meson configure`:

* `disable-builtins` will tell the compiler not to generate built-in function
* `disable-stack-protection` will tell the compiler not to insert stack protection calls
* `enable-pedantic`: Turn on `pedantic` warnings
* `enable-pedantic-error`: Turn on `pedantic` warnings and errors

Options can be specified using `-D` and the option name:

```
meson buildresults -Ddisable-builtins=false
```

The same style works with `meson configure`:

```
cd buildresults
meson configure -Ddisable-builtins=false
```

**[Back to top](#table-of-contents)**

## Documentation

Documentation can be built locally by running the following command:

```
make docs
```

Documentation can be found in `buildresults/docs`, and the root page is `index.html`.

**[Back to top](#table-of-contents)**

## Need help?

If you need further assistance or have any questions, please file a GitHub issue or send us an email using the [Embedded Artistry Contact Form](http://embeddedartistry.com/contact).

You can also [reach out on Twitter: mbeddedartistry](https://twitter.com/mbeddedartistry/).

## Contributing

If you are interested in contributing to this project, please read our [contributing guidelines](docs/CONTRIBUTING.md).

## Authors

* **[Phillip Johnston](https://github.com/phillipjohnston)**

## License

Copyright © 2020 Embedded Artistry LLC

See the [LICENSE](LICENSE) file for licensing details.

For other open-source licenses, please see the [Software Inventory](docs/software_inventory.xlsx).

## Acknowledgments

Make any public acknowledgments here

**[Back to top](#table-of-contents)**

