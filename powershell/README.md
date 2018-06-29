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

3. Copy resulting library to powershell executable folder

```
cp src/powershell-unix/libpsl-native.so src/powershell-unix/bin/Debug/netcoreapp2.1/
```

### *libpsl-native* Test Results

```
[mateus@mateus-freebsd libpsl-native]$ ctest --verbose
UpdateCTestConfiguration  from :/home/mateus/git/powershell/src/libpsl-native/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/mateus/git/powershell/src/libpsl-native/DartConfiguration.tcl
Test project /home/mateus/git/powershell/src/libpsl-native
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: psl-native-test

1: Test command: /home/mateus/git/powershell/src/libpsl-native/test/psl-native-test "--gtest_output=xml:native-tests.xml"
1: Test timeout computed to be: 9.99988e+06
1: [==========] Running 32 tests from 14 test cases.
1: [----------] Global test environment set-up.
1: [----------] 2 tests from CreateHardLinkTest
1: [ RUN      ] CreateHardLinkTest.FilePathNameDoesNotExist
1: [       OK ] CreateHardLinkTest.FilePathNameDoesNotExist (1 ms)
1: [ RUN      ] CreateHardLinkTest.VerifyLinkCount
1: [       OK ] CreateHardLinkTest.VerifyLinkCount (0 ms)
1: [----------] 2 tests from CreateHardLinkTest (1 ms total)
1:
1: [----------] 4 tests from CreateSymLinkTest
1: [ RUN      ] CreateSymLinkTest.FilePathNameDoesNotExist
1: [       OK ] CreateSymLinkTest.FilePathNameDoesNotExist (2 ms)
1: [ RUN      ] CreateSymLinkTest.SymLinkToFile
1: [       OK ] CreateSymLinkTest.SymLinkToFile (2 ms)
1: [ RUN      ] CreateSymLinkTest.SymLinkToDirectory
1: [       OK ] CreateSymLinkTest.SymLinkToDirectory (1 ms)
1: [ RUN      ] CreateSymLinkTest.SymLinkAgain
1: [       OK ] CreateSymLinkTest.SymLinkAgain (2 ms)
1: [----------] 4 tests from CreateSymLinkTest (7 ms total)
1:
1: [----------] 4 tests from IsExecutableTest
1: [ RUN      ] IsExecutableTest.FilePathNameDoesNotExist
1: [       OK ] IsExecutableTest.FilePathNameDoesNotExist (0 ms)
1: [ RUN      ] IsExecutableTest.NormalFileIsNotIsexecutable
1: [       OK ] IsExecutableTest.NormalFileIsNotIsexecutable (0 ms)
1: [ RUN      ] IsExecutableTest.FilePermission_700
1: [       OK ] IsExecutableTest.FilePermission_700 (0 ms)
1: [ RUN      ] IsExecutableTest.FilePermission_777
1: [       OK ] IsExecutableTest.FilePermission_777 (0 ms)
1: [----------] 4 tests from IsExecutableTest (0 ms total)
1:
1: [----------] 5 tests from isSymLinkTest
1: [ RUN      ] isSymLinkTest.FilePathNameDoesNotExist
1: [       OK ] isSymLinkTest.FilePathNameDoesNotExist (1 ms)
1: [ RUN      ] isSymLinkTest.NormalFileIsNotSymLink
1: [       OK ] isSymLinkTest.NormalFileIsNotSymLink (1 ms)
1: [ RUN      ] isSymLinkTest.SymLinkToFile
1: [       OK ] isSymLinkTest.SymLinkToFile (2 ms)
1: [ RUN      ] isSymLinkTest.NormalDirectoryIsNotSymbLink
1: [       OK ] isSymLinkTest.NormalDirectoryIsNotSymbLink (2 ms)
1: [ RUN      ] isSymLinkTest.SymLinkToDirectory
1: [       OK ] isSymLinkTest.SymLinkToDirectory (1 ms)
1: [----------] 5 tests from isSymLinkTest (7 ms total)
1:
1: [----------] 3 tests from IsFileTest
1: [ RUN      ] IsFileTest.RootIsFile
1: /home/mateus/git/powershell/src/libpsl-native/test/test-isfile.cpp:13: Failure
1: Value of: IsFile("/")
1:   Actual: true
1: Expected: false
1: [  FAILED  ] IsFileTest.RootIsFile (1 ms)
1: [ RUN      ] IsFileTest.BinLsIsFile
1: [       OK ] IsFileTest.BinLsIsFile (0 ms)
1: [ RUN      ] IsFileTest.CannotGetOwnerOfFakeFile
1: [       OK ] IsFileTest.CannotGetOwnerOfFakeFile (0 ms)
1: [----------] 3 tests from IsFileTest (1 ms total)
1:
1: [----------] 3 tests from IsDirectoryTest
1: [ RUN      ] IsDirectoryTest.RootIsDirectory
1: [       OK ] IsDirectoryTest.RootIsDirectory (0 ms)
1: [ RUN      ] IsDirectoryTest.BinLsIsNotDirectory
1: [       OK ] IsDirectoryTest.BinLsIsNotDirectory (0 ms)
1: [ RUN      ] IsDirectoryTest.ReturnsFalseForFakeDirectory
1: [       OK ] IsDirectoryTest.ReturnsFalseForFakeDirectory (0 ms)
1: [----------] 3 tests from IsDirectoryTest (0 ms total)
1:
1: [----------] 1 test from GetFullyQualifiedNameTest
1: [ RUN      ] GetFullyQualifiedNameTest.ValidateLinuxGetFullyQualifiedDomainName
1: [       OK ] GetFullyQualifiedNameTest.ValidateLinuxGetFullyQualifiedDomainName (30953 ms)
1: [----------] 1 test from GetFullyQualifiedNameTest (30953 ms total)
1:
1: [----------] 3 tests from getLinkCountTest
1: [ RUN      ] getLinkCountTest.FilePathNameDoesNotExist
1: [       OK ] getLinkCountTest.FilePathNameDoesNotExist (0 ms)
1: [ RUN      ] getLinkCountTest.LinkCountOfSinglyLinkedFile
1: [       OK ] getLinkCountTest.LinkCountOfSinglyLinkedFile (1 ms)
1: [ RUN      ] getLinkCountTest.LinkCountOfMultiplyLinkedFile
1: [       OK ] getLinkCountTest.LinkCountOfMultiplyLinkedFile (0 ms)
1: [----------] 3 tests from getLinkCountTest (1 ms total)
1:
1: [----------] 1 test from GetComputerNameTest
1: [ RUN      ] GetComputerNameTest.Success
1: [       OK ] GetComputerNameTest.Success (7 ms)
1: [----------] 1 test from GetComputerNameTest (7 ms total)
1:
1: [----------] 1 test from GetUserName
1: [ RUN      ] GetUserName.Success
1: [       OK ] GetUserName.Success (2 ms)
1: [----------] 1 test from GetUserName (2 ms total)
1:
1: [----------] 1 test from GetCurrentProcessId
1: [ RUN      ] GetCurrentProcessId.simple
1: [       OK ] GetCurrentProcessId.simple (1 ms)
1: [----------] 1 test from GetCurrentProcessId (1 ms total)
1:
1: [----------] 1 test from GetUserFromPid
1: [ RUN      ] GetUserFromPid.Success
1: /home/mateus/git/powershell/src/libpsl-native/test/test-getuserfrompid.cpp:13: Failure
1: Value of: expected
1:   Actual: "mateus"
1: Expected: GetUserFromPid(getpid())
1: Which is: NULL
1: [  FAILED  ] GetUserFromPid.Success (0 ms)
1: [----------] 1 test from GetUserFromPid (0 ms total)
1:
1: [----------] 1 test from LocaleTest
1: [ RUN      ] LocaleTest.Success
1: /home/mateus/git/powershell/src/libpsl-native/test/test-locale.cpp:21: Failure
1: Value of: "UTF-8"
1: Expected: nl_langinfo(0)
1: Which is: "US-ASCII"
1: [  FAILED  ] LocaleTest.Success (0 ms)
1: [----------] 1 test from LocaleTest (0 ms total)
1:
1: [----------] 2 tests from GetFileOwnerTest
1: [ RUN      ] GetFileOwnerTest.CanGetOwnerOfRoot
1: [       OK ] GetFileOwnerTest.CanGetOwnerOfRoot (0 ms)
1: [ RUN      ] GetFileOwnerTest.CannotGetOwnerOfFakeFile
1: [       OK ] GetFileOwnerTest.CannotGetOwnerOfFakeFile (0 ms)
1: [----------] 2 tests from GetFileOwnerTest (1 ms total)
1:
1: [----------] Global test environment tear-down
1: [==========] 32 tests from 14 test cases ran. (30981 ms total)
1: [  PASSED  ] 29 tests.
1: [  FAILED  ] 3 tests, listed below:
1: [  FAILED  ] IsFileTest.RootIsFile
1: [  FAILED  ] GetUserFromPid.Success
1: [  FAILED  ] LocaleTest.Success
1:
1:  3 FAILED TESTS
1/1 Test #1: psl-native-test ..................***Failed   31.01 sec

0% tests passed, 1 tests failed out of 1

Total Test time (real) =  31.01 sec

The following tests FAILED:
          1 - psl-native-test (Failed)
Errors while running CTest
```

As a result, the resulting powershell binary has weird behaviors that may be fixed when all tests are passing.

To run powershell:

```
dotnet src/powershell-unix/bin/Debug/netcoreapp2.1/pwsh.dll
```
