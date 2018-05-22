# Building CoreCLR on FreeBSD

## General Information

There are instructions as to how to build CoreCLR on FreeBSD already available on the [CoreCLR GitHub repository](https://github.com/dotnet/coreclr/blob/master/Documentation/building/freebsd-instructions.md). This process completes **successfully** on FreeBSD.

## Build Steps

Using _FreeBSD 11.1-RELEASE_ and the _master_ branch:

1. `pkg install cmake git llvm39 icu libunwind bash python2 krb5 lttng-ust`
2. `./build.sh -clang3.9 -release`