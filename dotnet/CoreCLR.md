# Building CoreCLR on FreeBSD

## General Information

There are instructions as to how to build CoreCLR on FreeBSD already available on the [CoreCLR GitHub repository](https://github.com/dotnet/coreclr/blob/master/Documentation/building/freebsd-instructions.md). This process completes **successfully** on FreeBSD.

## Building

### Native Parts

Using _FreeBSD 11.1-RELEASE_ and the _master_ branch:

1. `pkg install cmake git llvm39 icu libunwind bash python2 krb5 lttng-ust`
2. `./build.sh -clang3.9`

### Managed Parts

On a Windows machine, I cloned the coreclr repository on the _master_ branch:

1. `.\build.cmd -freebsdmscorlib`

Then I copied over to `coreclr/bin/Product/FreeBSD.x64.Debug` on the FreeBSD machine the files **System.Private.CoreLib.dll** and **PDB\System.Private.CoreLib.pdb**.

## Testing

### PAL Tests

On the CoreCLR repository root, I ran:

1. `./src/pal/tests/palsuite/runpaltests.sh $PWD/bin/obj/FreeBSD.x64.Debug $PWD/bin/paltestout`

As of right now, only **one** test failed, which was: `threading/NamedMutex/test1/paltest_namedmutex_test1. Exit code: 1`

### Managed Tests

TBD
