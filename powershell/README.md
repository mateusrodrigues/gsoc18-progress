# Notes on building PowerShell Core on FreeBSD

## Managed Parts

Building the managed parts on FreeBSD using bootstrapped CLI completes **successfully**.

1. Restore packages for top powershell-unix project
```
cd src/powershell-unix
dotnet restore
```

2. Generate the strongly-typed resource files
```
cd src/ResGen
dotnet restore
dotnet run
```

3. Generate the Type Catalog
```
targetFile="Microsoft.PowerShell.SDK/obj/Microsoft.PowerShell.SDK.csproj.TypeCatalog.targets"
cat > $targetFile <<-"EOF"
<Project>
    <Target Name="_GetDependencies"
            DependsOnTargets="ResolveAssemblyReferencesDesignTime">
        <ItemGroup>
            <_RefAssemblyPath Include="%(_ReferencesFromRAR.HintPath)%3B"  Condition=" '%(_ReferencesFromRAR.NuGetPackageId)' != 'Microsoft.Management.Infrastructure' "/>
        </ItemGroup>
        <WriteLinesToFile File="$(_DependencyFile)" Lines="@(_RefAssemblyPath)" Overwrite="true" />
    </Target>
</Project>
EOF
dotnet msbuild Microsoft.PowerShell.SDK/Microsoft.PowerShell.SDK.csproj /t:_GetDependencies "/property:DesignTimeBuild=true;_DependencyFile=$(pwd)/TypeCatalogGen/powershell.inc" /nologo
```
```
cd ../TypeCatalogGen
dotnet restore
dotnet run ../System.Management.Automation/CoreCLR/CorePsTypeCatalog.cs powershell.inc
```

4. Build the PowerShell top directory

```
cd src/powershell-unix
dotnet build
```

## Native Parts

PowerShell on *nix relies on a component called **libpsl-native** to provide functionality missing from .NET via system calls.

The code for the native parts have been migrated to [PowerShell/PowerShell-Native](https://github.com/PowerShell/PowerShell-Native). Fixes for FreeBSD-specific issues have been made through PR [#2](https://github.com/PowerShell/PowerShell-Native/pull/2).

1. Build libpsl-native

```
cd src/libpsl-native
cmake -DCMAKE_BUILD_TYPE=Debug .
make
```

2. Test

```
ctest --verbose
```

### *libpsl-native* Test Results

**ALL** tests are passing successfully.

```
Start testing: Jun 28 20:51 -03
----------------------------------------------------------
1/1 Testing: psl-native-test
1/1 Test: psl-native-test
Command: "/home/mateus/git/PowerShell-mateusrodrigues/src/libpsl-native/test/psl-native-test" "--gtest_output=xml:native-tests.xml"
Directory: /home/mateus/git/PowerShell-mateusrodrigues/src/libpsl-native/test
"psl-native-test" start time: Jun 28 20:51 -03
Output:
----------------------------------------------------------
[==========] Running 32 tests from 14 test cases.
[----------] Global test environment set-up.
[----------] 2 tests from CreateHardLinkTest
[ RUN      ] CreateHardLinkTest.FilePathNameDoesNotExist
[       OK ] CreateHardLinkTest.FilePathNameDoesNotExist (5 ms)
[ RUN      ] CreateHardLinkTest.VerifyLinkCount
[       OK ] CreateHardLinkTest.VerifyLinkCount (1 ms)
[----------] 2 tests from CreateHardLinkTest (6 ms total)

[----------] 4 tests from CreateSymLinkTest
[ RUN      ] CreateSymLinkTest.FilePathNameDoesNotExist
[       OK ] CreateSymLinkTest.FilePathNameDoesNotExist (7 ms)
[ RUN      ] CreateSymLinkTest.SymLinkToFile
[       OK ] CreateSymLinkTest.SymLinkToFile (2 ms)
[ RUN      ] CreateSymLinkTest.SymLinkToDirectory
[       OK ] CreateSymLinkTest.SymLinkToDirectory (2 ms)
[ RUN      ] CreateSymLinkTest.SymLinkAgain
[       OK ] CreateSymLinkTest.SymLinkAgain (5 ms)
[----------] 4 tests from CreateSymLinkTest (16 ms total)

[----------] 4 tests from IsExecutableTest
[ RUN      ] IsExecutableTest.FilePathNameDoesNotExist
[       OK ] IsExecutableTest.FilePathNameDoesNotExist (0 ms)
[ RUN      ] IsExecutableTest.NormalFileIsNotIsexecutable
[       OK ] IsExecutableTest.NormalFileIsNotIsexecutable (0 ms)
[ RUN      ] IsExecutableTest.FilePermission_700
[       OK ] IsExecutableTest.FilePermission_700 (1 ms)
[ RUN      ] IsExecutableTest.FilePermission_777
[       OK ] IsExecutableTest.FilePermission_777 (1 ms)
[----------] 4 tests from IsExecutableTest (2 ms total)

[----------] 5 tests from isSymLinkTest
[ RUN      ] isSymLinkTest.FilePathNameDoesNotExist
[       OK ] isSymLinkTest.FilePathNameDoesNotExist (4 ms)
[ RUN      ] isSymLinkTest.NormalFileIsNotSymLink
[       OK ] isSymLinkTest.NormalFileIsNotSymLink (2 ms)
[ RUN      ] isSymLinkTest.SymLinkToFile
[       OK ] isSymLinkTest.SymLinkToFile (1 ms)
[ RUN      ] isSymLinkTest.NormalDirectoryIsNotSymbLink
[       OK ] isSymLinkTest.NormalDirectoryIsNotSymbLink (2 ms)
[ RUN      ] isSymLinkTest.SymLinkToDirectory
[       OK ] isSymLinkTest.SymLinkToDirectory (4 ms)
[----------] 5 tests from isSymLinkTest (13 ms total)

[----------] 3 tests from IsFileTest
[ RUN      ] IsFileTest.RootIsFile
[       OK ] IsFileTest.RootIsFile (0 ms)
[ RUN      ] IsFileTest.BinLsIsFile
[       OK ] IsFileTest.BinLsIsFile (0 ms)
[ RUN      ] IsFileTest.CannotGetOwnerOfFakeFile
[       OK ] IsFileTest.CannotGetOwnerOfFakeFile (0 ms)
[----------] 3 tests from IsFileTest (0 ms total)

[----------] 3 tests from IsDirectoryTest
[ RUN      ] IsDirectoryTest.RootIsDirectory
[       OK ] IsDirectoryTest.RootIsDirectory (0 ms)
[ RUN      ] IsDirectoryTest.BinLsIsNotDirectory
[       OK ] IsDirectoryTest.BinLsIsNotDirectory (0 ms)
[ RUN      ] IsDirectoryTest.ReturnsFalseForFakeDirectory
[       OK ] IsDirectoryTest.ReturnsFalseForFakeDirectory (0 ms)
[----------] 3 tests from IsDirectoryTest (0 ms total)

[----------] 1 test from GetFullyQualifiedNameTest
[ RUN      ] GetFullyQualifiedNameTest.ValidateLinuxGetFullyQualifiedDomainName
[       OK ] GetFullyQualifiedNameTest.ValidateLinuxGetFullyQualifiedDomainName (30658 ms)
[----------] 1 test from GetFullyQualifiedNameTest (30658 ms total)

[----------] 3 tests from getLinkCountTest
[ RUN      ] getLinkCountTest.FilePathNameDoesNotExist
[       OK ] getLinkCountTest.FilePathNameDoesNotExist (0 ms)
[ RUN      ] getLinkCountTest.LinkCountOfSinglyLinkedFile
[       OK ] getLinkCountTest.LinkCountOfSinglyLinkedFile (0 ms)
[ RUN      ] getLinkCountTest.LinkCountOfMultiplyLinkedFile
[       OK ] getLinkCountTest.LinkCountOfMultiplyLinkedFile (0 ms)
[----------] 3 tests from getLinkCountTest (1 ms total)

[----------] 1 test from GetComputerNameTest
[ RUN      ] GetComputerNameTest.Success
[       OK ] GetComputerNameTest.Success (24 ms)
[----------] 1 test from GetComputerNameTest (24 ms total)

[----------] 1 test from GetUserName
[ RUN      ] GetUserName.Success
[       OK ] GetUserName.Success (1 ms)
[----------] 1 test from GetUserName (1 ms total)

[----------] 1 test from GetCurrentProcessId
[ RUN      ] GetCurrentProcessId.simple
[       OK ] GetCurrentProcessId.simple (0 ms)
[----------] 1 test from GetCurrentProcessId (0 ms total)

[----------] 1 test from GetUserFromPid
[ RUN      ] GetUserFromPid.Success
[       OK ] GetUserFromPid.Success (1 ms)
[----------] 1 test from GetUserFromPid (1 ms total)

[----------] 1 test from LocaleTest
[ RUN      ] LocaleTest.Success
[       OK ] LocaleTest.Success (1 ms)
[----------] 1 test from LocaleTest (1 ms total)

[----------] 2 tests from GetFileOwnerTest
[ RUN      ] GetFileOwnerTest.CanGetOwnerOfRoot
[       OK ] GetFileOwnerTest.CanGetOwnerOfRoot (0 ms)
[ RUN      ] GetFileOwnerTest.CannotGetOwnerOfFakeFile
[       OK ] GetFileOwnerTest.CannotGetOwnerOfFakeFile (0 ms)
[----------] 2 tests from GetFileOwnerTest (0 ms total)

[----------] Global test environment tear-down
[==========] 32 tests from 14 test cases ran. (30724 ms total)
[  PASSED  ] 32 tests.
<end of output>
Test time =  30.75 sec
----------------------------------------------------------
Test Passed.
"psl-native-test" end time: Jun 28 20:52 -03
"psl-native-test" time elapsed: 00:00:30
----------------------------------------------------------

End testing: Jun 28 20:52 -03
```

> A note on **LocaleTest.Success**:
> the environment variable **LANG** must be set to `LANG=en_US.UTF-8`.

To run powershell:

```
dotnet src/powershell-unix/bin/Debug/netcoreapp2.1/pwsh.dll
```
