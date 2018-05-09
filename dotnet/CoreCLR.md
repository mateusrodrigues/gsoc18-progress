# Building CoreCLR on FreeBSD

## General Information

There are instructions as to how to build CoreCLR on FreeBSD already available on the [CoreCLR GitHub repository](https://github.com/dotnet/coreclr/blob/master/Documentation/building/freebsd-instructions.md). This process completes **successfully** and the bluid products are **corerun** and **libcoreclr.so**.

## Build Steps

Using _FreeBSD 11.1-RELEASE_ and the _release/2.0.0_ branch:

1. `pkg update; pkg upgrade`
2. `mkdir git; cd git`
3. `git clone https://github.com/dotnet/coreclr.git`
4. `cd coreclr`
5. `git checkout release/2.0.0`
6. `./build.sh release`