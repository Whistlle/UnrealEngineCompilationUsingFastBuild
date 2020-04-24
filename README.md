# Compile Unreal Engine 4.24 using FastBuild

## Implementation notes
#### Currently supported platforms
- Win64 - tested with Windows 10, Visual Studio 2019 Community, Unreal Engine 4.24.2, FastBuild v0.99.

#### Not test
- Durango was fully supported using VS2015 at some point in the past, but VS2015 is not supported since 4.22;
- Orbis will require some changes.

#### Not supported platforms
- Android;
- iOS;
- Linux;
- and others...

#### Changes
- ParseOriginalCommandPathAndArguments() now checks link-filter.exe.
- Add UE4ISPCCompiler to compile ispc files.
- Modify AddCompileAction() to compile ispc header/obj, cc.obj correctly.
- Modify AddLinkAction() to check link-filter.exe command and generate Link Action correctly.

## Main idea
The main difference from Yassine Riahi & Liam Flookes version is in added support for dependency files.
It was implemented using FastBuild "-report" argument.
Generated report (see ReportFilePath) contains include files usage.
Since there is no source code for cl-filter.exe, it is not possible to guarantee same result.
Moreover it is fact, that output contains more include files than when using cl-filter.exe.
Maybe more precise filtration could be applied, some ideas:
- exclude pch includes usages from dependent compilation units;
- exclude files from outside engine folder;
- exclude using cl-filter.exe.sn-dbs-tool.ini.

## FastBuild version
Don't use FastBuild 0.96 or older versions, since it has processes management error.

For more information see https://github.com/fastbuild/fastbuild/issues/223.

## Installation steps
1. Save copy of this file as ...\Engine\Source\Programs\UnrealBuildTool\System\FASTBuild.cs;
2. Add it to UnrealBuildTool project;
3. Apply fix from https://github.com/TheEmidee/UE4FastBuild:
	VS2019 required same behaviour as for VS2017;
4. Add FastBuild executor to ExecuteActions(...) inside ActionGraph.cs:
```
...
// Figure out which executor to use
ActionExecutor Executor;
if (FASTBuild.IsAvailable())
{
	Executor = new FASTBuild();
}
else if (BuildConfiguration.bAllowHybridExecutor && HybridExecutor.IsAvailable())
...
```
5. Add "public" access modifier to GetVCToolPath64(...) method inside VCEnvironment.cs.


## Original version
https://github.com/liamkf/Unreal_FASTBuild
