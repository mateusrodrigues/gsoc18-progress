# Building CoreFX on FreeBSD

Building the Framework Native Components using the built-in build script still fails because it needs to download an already pre-compiled version of the .NET Core CLI, which is not yet availble for FreeBSD.

## For the Framework Native Components

On _FreeBSD 11.1-RELEASE_ and the _release/2.0.0_ branch:

1. `src/Native/build-native.sh freebsd x64 debug`

**FAILS**

```
	[ 13%] Building CXX object System.Native/CMakeFiles/System.Native-Static.dir/pal_networking.cpp.o
	/usr/home/mateus/git/corefx/src/Native/Unix/System.Native/pal_networking.cpp:1180:45: error: unknown type name 'in_pktinfo'; did you mean 'in6_pktinfo'?
	    return (isIPv4 != 0 ? CMSG_SPACE(sizeof(in_pktinfo)) : 0) + (isIPv6 != 0 ? CMSG_SPACE(sizeof(in6_pktinfo)) : 0);
	                                            ^~~~~~~~~~
                                            in6_pktinfo
```

**SOLVED**

1. `src/Native/build-native.sh freebsd x64 debug cmakeargs –DHAVE_IN_PKTINFO=0`

**FAILS**

```
	[ 63%] Building CXX object System.Net.Security.Native/CMakeFiles/System.Net.Security.Native.dir/pal_gssapi.cpp.o
	/usr/home/mateus/git/corefx/src/Native/Unix/System.Net.Security.Native/pal_gssapi.cpp:16:10: fatal error: 'gssapi/gssapi_ext.h' file not found
	#include <gssapi/gssapi_ext.h>
	         ^~~~~~~~~~~~~~~~~~~~~
	1 error generated.
*** [System.Net.Security.Native/CMakeFiles/System.Net.Security.Native.dir/pal_gssapi.cpp.o] Error code 1
```

Adding yet another flag to the compiler, it finds the headers, but still fails:

1. `src/Native/build-native.sh freebsd x64 release cmakeargs -DHAVE_IN_PKTINFO=0 cmakeargs -DHAVE_HEIMDAL_HEADERS=1`

```
	[ 63%] Building CXX object System.Net.Security.Native/CMakeFiles/System.Net.Security.Native.dir/pal_gssapi.cpp.o
	/usr/home/mateus/git/corefx/src/Native/Unix/System.Net.Security.Native/pal_gssapi.cpp:33:38: error: use of undeclared identifier 'GSS_C_DCE_STYLE'; did you mean 'PAL_GSS_C_DCE_STYLE'?
	static_assert(PAL_GSS_C_DCE_STYLE == GSS_C_DCE_STYLE, "");
                                     ^~~~~~~~~~~~~~~
```

## For the Framework Managed Components

Building the Framework Managed Components needs a Windows machine. 

### Building System.Private.CoreLib.dll

Completes successfully and the products are **System.Private.CoreLib.dll** (CoreCLR) with the following command:

On _Windows_ and on the _release/2.0.0_ branch.

1. `D:\git\coreclr> build.cmd freebsdmscorlib`

### Building the rest of the framework

1. `D:\git\corefx> build-managed.cmd -os=Linux -target-os=Linux -SkipTests`

This step doesn't complete successfully. The command on the docs is wrong – as pointed out here: [Issue #6650](https://github.com/dotnet/coreclr/issues/6650).

Also tried:

1. `build-managed.cmd -os=FreeBSD -SkipTests -OSGroup=FreeBSD`

But version on branch release/2.0.0 fails with the following error:

```
System.Data.forwards.cs(10,83): error CS0234: The type or namespace name 'ODBC32' does not exist in the namespace 'Syst
em.Data.Odbc' (are you missing an assembly reference?) [C:\Users\Mateus\Git\corefx\src\shims\manual\System.Data.csproj]
```
	
Take a look at this PR about it: [PR #25557](https://github.com/dotnet/corefx/pull/25557).
