# Notes on building the .NET SDK on FreeBSD

## Priority list

- [x] #1 - ~Finish instructions from [Wiki](https://github.com/dotnet/corefx/wiki/Building-.NET-Core--2.x-on-FreeBSD) to get a working SDK on FreeBSD~
  - [These](https://github.com/dotnet/corefx/wiki/Building-.NET-Core--2.x-on-FreeBSD#building-freebsd-sdk) were the steps used to build the SDK if you'd like to reproduce this work.
  - SDK zipped file available [here](http://distcache.FreeBSD.org/local-distfiles/dbn/dotnet/dotnetcli-freebsd.11-x64.zip). Bootstrap based on `dotnet-2.2.100-preview1-008958`.
- [x] #2 - Try to run tests and report outcome (see [#24255](https://github.com/dotnet/corefx/issues/24255) and [#15674](https://github.com/dotnet/coreclr/issues/15674))
  - List of failing tests [here](https://github.com/dotnet/coreclr/issues/18067#issuecomment-403099320)
- [ ] #3 - Write more scripts to automate creation of FreeBSD SDK from step 1
- [ ] #4 - Get official RID for BSD and possibly fix tools and JSON files to use it
- [ ] #5 - [#24259](https://github.com/dotnet/corefx/issues/24259) could be a problem for PowerShell when it tries to use colors. Check it out.
  - As part of [#26966](https://github.com/dotnet/corefx/issues/26966), it would probably be ok to fallback to "dumb" terminal if terminfo lookup fails.
  
## Notes regarding the experimental FreeBSD .NET Core SDK

Some changes are still necessary to get the SDK working. These are temporary as we work on fixing them.

### Environment Variables

```
# Regarding crossgen
export COMPlus_ZapDisable=1
export COMPlus_ReadyToRun=0

# Regarding HTTP not working by default
export SSL_CERT_FILE=/usr/local/share/certs/ca-root-nss.crt
export DOTNET_SYSTEM_NET_HTTP_USESOCKETSHTTPHANDLER=0

# Regarding Shared Compilation not working
# export UseSharedCompilation=false [FIXED]

# Optional parameters
export DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1
export DOTNET_CLI_TELEMETRY_OPTOUT=1
```

## References

- [Building .NET Core 2.x on FreeBSD](https://github.com/dotnet/corefx/wiki/Building-.NET-Core--2.x-on-FreeBSD) by Russell Haley and Tomas Weinfurt
- [Build CoreCLR on FreeBSD](https://github.com/dotnet/coreclr/blob/release/2.1/Documentation/building/freebsd-instructions.md) by Microsoft
