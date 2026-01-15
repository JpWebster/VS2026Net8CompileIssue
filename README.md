After upgrade to Visual Studio 2026 we are experiencing an issue with some generated source code that compiles successfully when using .NET 10 but when we use the .NET 8 SDK it fails with the following error:

Program.cs(15,24,15,27): error CS8078: An expression is too long or complex to compile

Our application still targets .NET 8, and we use global.json to specify the SDK requirement like so:
{
  "sdk": {
    "version": "8.0.416",
    "rollForward": "latestFeature"
  }
}
When building in Visual Studio: 
- Target "CoreCompile" in file "C:\Program Files\Microsoft Visual Studio\18\Enterprise\MSBuild\Current\Bin\Roslyn\Microsoft.CSharp.Core.targets"
- Task "Csc" - which uses ... 
1>         C:\Program Files\Microsoft Visual Studio\**18\Enterprise\MSBuild\Current\Bin\Roslyn\csc.exe** 
-  Microsoft (R) Visual C# Compiler version **5.0.0-2.26054.2** (ec501e4c)

**Version Used**:  
SDK version 8.0.416 <- uses compiler **5.0.0-2.26054.2** from VS 2026 MSBuild folder as noted above. 
SDK version 10.0.101 <- uses compiler **5.0.0-2.25569.105** from dotnet\sdk\10.0.101 folder as noted below.

**Steps to Reproduce**:

1. Compile the attached source code and get CS8078 error. (Note that this is not our production code but rather some bogus C# code to trigger the CS8078 error so I can test using different csc versions and .NET SDK's)

2. Switch the global.json to use 10.0.101 SDK and the code compiles fine.
{
  "sdk": {
    "version": "10.0.102",
    "rollForward": "latestFeature"
  }
}
Results in:
-	Target "CoreCompile" in file "C:\Program Files\**dotnet\sdk\10.0.101**\Roslyn\Microsoft.CSharp.Core.targets"
-	C:\Program Files\dotnet\sdk\10.0.101\Roslyn\binfx\..\bincore\csc.exe
-	Microsoft (R) Visual C# Compiler version **5.0.0-2.25569.105** (fad253f5)


**Diagnostic Id**:
CS8078: An expression is too long or complex to compile

**Expected Behavior**:
Upgrade to VS 2026 should not break our .NET 8 builds

**Actual Behavior**:
Upgrade to VS 2026 breaks our .NET 8 builds.

We can look to refactor our code to address the issue, however, we'd like to understand what changed in the CSC compiler to cause this error in .NET 8 builds within Visual Studio 2026. Where .NET 10 builds fine in 2026.

I'm not able to determine what recent changes to the compiler may have caused this problem.
