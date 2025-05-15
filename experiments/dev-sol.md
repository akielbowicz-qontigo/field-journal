# Some notes about the developer solution

To be able to work in a monorepo by refactoring code into their own package and being able to edit them as a monolith
I added the following magic that transforms packagereferences into projectreferences

```xml
<Project>
  <!--Sadly this needs to live here (in .targets) because '$(ProjectName)' is not resolved in ".props"-->
  <Choose>
    <When Condition="$([System.Text.RegularExpressions.Regex]::IsMatch('$(ProjectName)','Test'))">
      <PropertyGroup>
        <SrcRelativePath>..\..\src</SrcRelativePath>
      </PropertyGroup>
    </When>
    <Otherwise>
      <PropertyGroup>
        <SrcRelativePath>..</SrcRelativePath>
      </PropertyGroup>
    </Otherwise>
  </Choose>

  <PropertyGroup>
    <SolutionFileContent>$([System.IO.File]::ReadAllText($(SolutionPath)))</SolutionFileContent>
  </PropertyGroup>

  <ItemGroup>
    <!--Check if package starts with "MyNamespace."-->
    <SomePackage Include="@(PackageReference)">
      <IsPackage Condition="$([System.Text.RegularExpressions.Regex]::Match(%(Identity),'^MyNamespace.*').Success)">true</IsPackage>
      <IsInSolution>$(SolutionFileContent.Contains('\%(Identity).csproj'))</IsInSolution>
    </SomePackage>
    <PackageInSolution Include="@(SomePackage -> WithMetadataValue('IsPackage','true'))"
                          Exclude="@(SomePackage -> WithMetadataValue('IsInSolution','false'))"/>
    <ProjectReference Include="@(PackageInSolution -> '$(SrcRelativePath)\%(Identity)\%(Identity).csproj')" />
    <PackageReference Remove="@(PackageInSolution -> '%(Identity)')"/>
  </ItemGroup>

  <!--Used for debug addition of ProjectReferences-->
  <Target Name="Messages" AfterTargets="BeforeBuild" Condition="false">
    <Message Importance="High" Text="@(PackageReference -> 'Project:$(ProjectName) -> Package:%(Identity)' )"/>
    <Message Importance="High" Text="@(ProjectReference -> '### Project:$(ProjectName) -> ProjPath:%(Identity)' )"/>
  </Target>
</Project>

```

Inspired by [dotnet/sdk#1151](https://github.com/dotnet/sdk/issues/1151)
Did some msbuild black magic. It works by:
Get All PackageReference
-> Tag if Name (%(Identity)) starts with MyNamespace
-> Filter ones that start with MyNamespace
-> Add as ProjectReference
-> Remove PackageReference.

Some extra documentation needed to understand what's happening:
https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-transforms?view=vs-2022
https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-well-known-item-metadata?view=vs-2022
https://learn.microsoft.com/en-us/visualstudio/msbuild/msbuild-conditions?view=vs-2022