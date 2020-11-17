Within your Visual Studio solution, right click your project, click *"Manage NuGet Packages..."*, ensure *"Include prerelease*" is checked, and search for and install the `Microsoft.Toolkit.Uwp.Notifications` [NuGet package](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) (install the 7.0 preview).

> [!IMPORTANT]
> .NET Framework Win32 apps that still use packages.config must migrate to PackageReference, otherwise the Windows 10 SDKs won't be referenced correctly. In your project, right-click on "References", and click "Migrate packages.config to PackageReference".
> 
> .NET Core 3.0 WPF apps must update to .NET Core 3.1, otherwise the APIs will be absent.