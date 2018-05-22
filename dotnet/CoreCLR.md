# Building CoreCLR on FreeBSD

## General Information

There are instructions as to how to build CoreCLR on FreeBSD already available on the [CoreCLR GitHub repository](https://github.com/dotnet/coreclr/blob/master/Documentation/building/freebsd-instructions.md). This process completes **successfully** on FreeBSD.

## Build Steps

Using _FreeBSD 11.1-RELEASE_ and the _master_ branch:

<<<<<<< HEAD
1. `pkg install cmake git llvm39 icu libunwind bash python2 krb5 lttng-ust`
2. `./build.sh -clang3.9 -release`
=======
1. `pkg update; pkg upgrade`
2. `mkdir git; cd git`
3. `git clone https://github.com/dotnet/coreclr.git`
4. `cd coreclr`
5. `git checkout release/2.0.0`
6. `./build.sh release`

### Using the release 2.1.0-rc1

1. Download the [release 2.1.0-rc1](https://github.com/dotnet/coreclr/archive/v2.1-rc1.zip)
2. `./build.sh release`
>>>>>>> 0788051f8c25543a36eed3c00ba478fafb006bf9
