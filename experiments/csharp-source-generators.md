---

---

Following  the pluralsight course on [C# source generators](https://app.pluralsight.com/library/courses/c-sharp-10-developing-source-generators/)

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <!-- Do not add net8, source generators need to be only netstandard2.0 -->
    <TargetFramework>netstandard2.0</TargetFramework>
    <RootNamespace>My.SourceGenerators</RootNamespace>
    <EnforceExtendedAnalyzerRules> true </EnforceExtendedAnalyzerRules>
    <LangVersion>latest</LangVersion>
    <IsRoslynComponent>true</IsRoslynComponent>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.CodeAnalysis.Analyzers"/>
    <PackageReference Include="Microsoft.CodeAnalysis.CSharp"/>
  </ItemGroup>
</Project>
```

```csharp
    [Generator]
    public class MyGenerator : IIncrementalGenerator
    {
        public void Initialize(IncrementalGeneratorInitializationContext context) => throw new System.NotImplementedException();
    }
```


```xml
    <PropertyGroup>
    <EmitCompilerGeneratedFiles>true</EmitCompilerGeneratedFiles>
    </PropertyGroup>


    <ProjectReference Include="../My.SourceGenerators/My.SourceGenerators.csproj"
                      OutputItemType="Analyzer"
                      ReferenceOutputAssembly="false"/>

```


`View -> Other Windows -> Syntax Visualizer`

To see the emmited generated files `obj/Debug/net8.0/generated`

To be able to debug the source generator create a Roslyn Debug profile

```json
//My.SourceGenerators/launchSettings.json
{
  "profiles": {
    "Profile 1": {
      "commandName": "DebugRoslynComponent",
      "targetProject": "..\\MyProject\\MyProject.csproj"
    }
  }
}
```


To create a nuget package with the analyzer

```xml
  <PropertyGroup>
    <GeneratePackageOnBuild>true</GeneratePackageOnBuild>
    <IncludeBuildOutput>false</IncludeBuildOutput>
  </PropertyGroup>
  <ItemGroup>
    <!--  Package the generator in the analyzer directory -->
    <None Include="$(OutputPath)\$(AssemblyName).dll"
          Pack="true"
          PackagePath="analyzers/dotnet/cs"
          Visible="false" />
  </ItemGroup>
```
