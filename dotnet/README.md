# Notes on building the .NET SDK on FreeBSD

## Priority list

- [ ] #1 - Finish instructions from [Wiki](https://github.com/dotnet/corefx/wiki/Building-.NET-Core--2.x-on-FreeBSD)
- [ ] #2 - Try to run tests and report outcome (see [#24255](https://github.com/dotnet/corefx/issues/24255) and [#15674](https://github.com/dotnet/coreclr/issues/15674))
- [ ] #3 - Write more scripts to automate creation of FreeBSD SDK from step 1
- [ ] #4 - Get official RID for BSD and possibly fix tools and JSON files to use it
- [ ] #5 - [#24259](https://github.com/dotnet/corefx/issues/24259) could be a problem for PowerShell when it tries to use colors. Check it out.
  - As part of [#26966](https://github.com/dotnet/corefx/issues/26966), it would probably be ok to fallback to "dumb" terminal if terminfo lookup fails.

## References

- [Building .NET Core 2.x on FreeBSD](https://github.com/dotnet/corefx/wiki/Building-.NET-Core--2.x-on-FreeBSD) by Russell Haley and Tomas Weinfurt
- [Build CoreCLR on FreeBSD](https://github.com/dotnet/coreclr/blob/release/2.1/Documentation/building/freebsd-instructions.md) by Microsoft
