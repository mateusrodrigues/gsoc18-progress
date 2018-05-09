# Building the .NET Core CLI on FreeBSD

I can confirm the CLI can be built and installed on FreeBSD! By reproducing the steps on [issue #3131](https://github.com/dotnet/core-setup/issues/3131) I was able to build and install the CLI by doing:

1. `git clone https://github.com/dotnet/core-setup`
2. `cmake src/corehost -G 'Unix Makefiles' -DCLI_CMAKE_PLATFORM_ARCH_AMD64=1 -DCLI_CMAKE_RUNTIME_ID:STRING=unix-x64 -DCLI_CMAKE_HOST_VER:STRING=2.0.0 -DCLI_CMAKE_HOST_FXR_VER:STRING=2.0.0 -DCLI_CMAKE_HOST_POLICY_VER:STRING=2.0.0 -DCLI_CMAKE_PKG_RID:STRING=unix-x64 -DCLI_CMAKE_COMMIT_HASH:STRING=HASH -DCLI_CMAKE_APPHOST_VER=2.0.0`
3. `make`
4. `make install`

The build in the master branch is OK without changing any files.