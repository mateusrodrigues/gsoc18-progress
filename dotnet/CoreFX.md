# Building CoreFX on FreeBSD

Building the Framework Native Components using the built-in build script still fails because it needs to download an already pre-compiled version of the .NET Core CLI, which is not yet availble for FreeBSD.

## Building

### Native Parts

On _FreeBSD 11.1-RELEASE_ and the _master_ branch:

1. `src/Native/build-native.sh -clang3.9`

### Managed Parts

On a **Windows** machine:

### Building the rest of the framework

1. `build-managed.cmd -os=FreeBSD -SkipTests -OSGroup=FreeBSD`
