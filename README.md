A build-only package to add references for the Unturned, Unity, and RocketMod/LDM libraries.

Available to install from NuGet: [Unturned.MSBuild](https://www.nuget.org/packages/Unturned.MSBuild).

# Features
* Easily reference Unturned and LDM (RocketMod) libraries.
* Optionally copy them to your project directory allowing someone to work on the project without Unturned installed, and keep them updated simply by building the project while having Unturned installed.
  * This is also useful for automated CI/CD build tools.
* Copies documentation from Unity installation if found.
* Adds default global usings to improve development experience.
* Adds #define constants for the current version of Unity.
* Enables `Microsoft.Build.CopyOnWrite` to add CoW support to MSBuild for file systems that support it.
* Directly references the game using the `<Reference>` item, doesn't actually contain the Unturned assemblies.
* Compatible with Publicizer.
* Automatically references the latest installed version of the game without having to update the package.
  * Certain updates like UnityEngine a version change may require a package update.

# Usage
This package is installed using the csproj file which needs to use the new .NET SDK format. If your project was created for .NET Framework and uses the old csproj format, you can upgrade to the new format using [this tool](https://github.com/hvanbakel/CsprojToVs2017/tree/master?tab=readme-ov-file#quick-start).
```xml
<ItemGroup>

    <!-- Add a reference to this package -->
    <PackageReference Include="Unturned.MSBuild" Version="1.0.0" PrivateAssets="all" />

</ItemGroup>

<ItemGroup>

    <!-- List needed Unturned and Unity libraries -->
    <UnturnedReference Include="Assembly-CSharp" />
    <UnturnedReference Include="SDG.NetTransport" />
    <UnturnedReference Include="com.rlabrecque.steamworks.net" />
    <UnturnedReference Include="UnityEngine" />
    <UnturnedReference Include="UnityEngine.CoreModule" />
    <UnturnedReference Include="UnityEngine.PhysicsModule" />
    
    <!-- If using rocket, list needed Rocket libraries -->
    <RocketReference Include="Rocket.API" />
    <RocketReference Include="Rocket.Core" />
    <RocketReference Include="Rocket.Unturned" />

</ItemGroup>

<PropertyGroup>

    <!-- Optionally keep a copy of the files in the repository for remote development or to allow for CI actions. -->
    <CopyUnturnedReferencesToProject>True</CopyUnturnedReferencesToProject>
    
    <!-- Removes the copy of a library when you remove it's UnturnedReference or RocketReference. -->
    <DeleteUnusedUnturnedReferences>True</DeleteUnusedUnturnedReferences>

</PropertyGroup>
```

## Use Directory.Build.props

The [Directory.Build.props](https://learn.microsoft.com/en-us/visualstudio/msbuild/customize-by-directory?view=visualstudio#directorybuildprops-and-directorybuildtargets) file can be added to a folder higher up the hierarchy than your project to apply to all projects in sub-folders. This is a good way to configure custom locations for the Unity or Unturned installation properties without committing them to your project.

### Example
Adding the following file will configure all projects within the 'repos' folder to reference documentation from Unity version **2021.3.29f1** and look for Unturned at **C:\Dev\SteamCMD**.

`C:\Users\<name>\source\repos\Directory.Build.props`
```xml
<Project>
    <PropertyGroup>
        <UnityVersion>2021.3.29f1</UnityVersion>
        <UnturnedInstallDir>C:\Dev\SteamCMD</UnturnedInstallDir>
    </PropertyGroup>
</Project>
```

# Properties
The following properties can be added to a `<PropertyGroup>` to modify this package's behavior.

| Property                          | Default Value                             | Description                                                                                                    |
| --------------------------------- | ----------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| UnityVersion                      | 2022.3.62f3                               | The version of UnityEngine to pull documentation from.                                                         |
| UnturnedBuild                     | Server                                    | Whether to reference U3DS: `Server`, or Unturned: `Client`.                                                    |
| UnturnedReferencesCopyOutput      | `$(MSBuildStartupDirectory)/Libraries`    | When reference copying to project is enabled, the location where they're copied to.                            |
| CopyUnturnedReferencesToOutput    | False                                     | If UnturnedReferences should be copied to the output directory.                                                |
| CopyUnturnedReferencesToProject   | False                                     | If UnturnedReferences should be copied to the repository to use as a fallback reference.                       |
| DeleteUnusedUnturnedReferences    | False                                     | If files in UnturnedReferencesCopyOutput that aren't referenced and are present in the game should be removed. |
| AddUnturnedGlobalUsings           | True                                      | Adds global usings for various commonly used types (see below).                                                |
| UnturnedReferenceDebugLog         | False                                     | Logs where the package is looking for Unity and Unturned to the build output.                                  |
| UnityInstallDir*                  | OS-dependent                              | Overrides the path of the Unity installation used to find documentation.                                       |
| UnturnedInstallDir*               | OS-dependent                              | Overrides the path of the referenced Unturned installation.                                                    |

\* If this value doesn't exist it will fallback to the default value.

Note: this library also disables `AppendRuntimeIdentifierToOutputPath` and `AppendTargetFrameworkToOutputPath` if they haven't been explicitly set (unless targeting multiple frameworks).

# Items

The following items can be included in an `<ItemGroup>` to add references.

| Item              | Description                                                            |
| ----------------- | ---------------------------------------------------------------------- |
| UnturnedReference | Adds a reference to an assembly in the `Unturned_Data/Managed` folder  |
| RocketReference   | Adds a reference to an assembly in the `Extras/Rocket.Unturned` folder |

# Errors

The following error codes are added.

| Code   | Description |
| ------ | ----------- |
| UNT003 | Emitted when referencing Newtonsoft.Json using `<UnturnedReference>` instead of `<PackageReference>`. |

# Legal

Usage of the libraries referenced by this package are subject to Unturned's EULA, https://store.steampowered.com/eula/304930_eula_1, Unity's reference-only license, https://unity.com/legal/licenses/unity-reference-only-license, and any licenses of third-party libraries used by Unturned.

The content of this package (build files, etc) is licensed under the MIT license.

The shape of the MSBuild Logo used in the package image is property of Microsoft.