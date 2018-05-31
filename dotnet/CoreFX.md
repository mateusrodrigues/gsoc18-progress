# Building CoreFX on FreeBSD

Building the Framework Native Components using the built-in build script still fails because it needs to download an already pre-compiled version of the .NET Core CLI, which is not yet availble for FreeBSD.

## Building

### Native Parts

On _FreeBSD 11.1-RELEASE_ and the _master_ branch:

1. `src/Native/build-native.sh -clang3.9`

### Managed Parts

On a **Windows** machine:

### Building the rest of the framework

1. `build-managed.cmd -os=FreeBSD -OSGroup=FreeBSD -BuildTests -SkipTests -SkipManagedPackageBuild`

**FAILS:**
```
C:\Users\mateu\Git\corefx\src\Microsoft.XmlSerializer.Generator\tests\Microsoft.XmlSerializer.Generator.Tests.csproj(59
,5): error MSB3073: The command "C:\Users\mateu\Git\corefx\bin/testhost/netcoreapp-FreeBSD-Debug-x64/dotnet C:\Users\ma
teu\Git\corefx\bin/AnyOS.AnyCPU.Debug/Microsoft.XmlSerializer.Generator.Tests/netcoreapp/dotnet-Microsoft.XmlSerializer
.Generator.dll C:\Users\mateu\Git\corefx\bin/AnyOS.AnyCPU.Debug/Microsoft.XmlSerializer.Generator.Tests/netcoreapp/Micr
osoft.XmlSerializer.Generator.Tests.dll --force --quiet" exited with code 9009.
```
