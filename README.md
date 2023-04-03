Having built a suite of software, in this case GCC, it needs to be packaged so that it can be installed on users' systems.

The build scripts are available in these repositories:
* [for native compilers](https://github.com/simonjwright/building-gcc-macos-native), both Intel and Apple silicon, and
* [for cross-compilers](https://github.com/simonjwright/building-gcc-macos-arm-eabi) to ARM Cortex MCUs (`arm-eabi`).

_This_ repository contains
* scripts to create installable packages, and
* releases made with those scripts.
