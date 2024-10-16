# Failure to build when Microsoft.WindowsAppSDK 1.6 referenced through secondary nuget package

This is a reproduction example of a WinUI 3 bug introduced in Microsoft.WindowsAppSDK 1.6.240923002.

The bug makes it impossible for a class library to consume a nuget package that references the WindowsAppSDK 1.6.240923002 nuget package.

The example consists of three projects:

* WebView2LoaderBug.Lib -
  A class library that uses the WindowsAppSDK 1.6.240923002 as a nuget reference and in itself builds a nuget package named WebView2LoaderBugLibrary.
* WebView2LoaderBug.LibWrapper -
  A class library that references the WebView2LoaderBugLibrary nuget package built by the WebView2LoaderBug.Lib project.
* WebView2LoaderBug.App -
  A WinUI 3 desktop application that references the WebView2LoaderBug.LibWrapper project.

The project fails to build the WebView2LoaderBug.Lib project with the following compiler error output:

```
1>------ Rebuild All started: Project: WebView2LoaderBug.Lib, Configuration: Debug Any CPU ------
Restored C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.LibWrapper\WebView2LoaderBug.LibWrapper.csproj (in 51 ms).
Restored C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.App\WebView2LoaderBug.App.csproj (in 70 ms).
Restored C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.Lib\WebView2LoaderBug.Lib.csproj (in 88 ms).
1>WebView2LoaderBug.Lib -> C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.Lib\bin\Debug\net8.0-windows10.0.22621\WebView2LoaderBug.Lib.dll
1>The package WebView2LoaderBugLibrary.1.0.0 is missing a readme. Go to https://aka.ms/nuget/authoring-best-practices/readme to learn why package readmes are important.
1>Successfully created package 'C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.Lib\bin\WebView2LoaderBugLibrary.1.0.0.nupkg'.
2>------ Rebuild All started: Project: WebView2LoaderBug.LibWrapper, Configuration: Debug Any CPU ------
2>C:\Program Files\Microsoft Visual Studio\2022\Professional\MSBuild\Current\Bin\amd64\Microsoft.Common.CurrentVersion.targets(5321,5): error MSB3030: Could not copy the file "C:\Users\my-user\.nuget\packages\webview2loaderbuglibrary\1.0.0\lib\net8.0-windows10.0.22621\WebView2LoaderBug.Lib\runtimes\win-arm64\native\WebView2Loader.dll" because it was not found.
2>C:\Program Files\Microsoft Visual Studio\2022\Professional\MSBuild\Current\Bin\amd64\Microsoft.Common.CurrentVersion.targets(5321,5): error MSB3030: Could not copy the file "C:\Users\my-user\.nuget\packages\webview2loaderbuglibrary\1.0.0\lib\net8.0-windows10.0.22621\WebView2LoaderBug.Lib\runtimes\win-x64\native\WebView2Loader.dll" because it was not found.
2>C:\Program Files\Microsoft Visual Studio\2022\Professional\MSBuild\Current\Bin\amd64\Microsoft.Common.CurrentVersion.targets(5321,5): error MSB3030: Could not copy the file "C:\Users\my-user\.nuget\packages\webview2loaderbuglibrary\1.0.0\lib\net8.0-windows10.0.22621\WebView2LoaderBug.Lib\runtimes\win-x86\native\WebView2Loader.dll" because it was not found.
2>Done building project "WebView2LoaderBug.LibWrapper.csproj" -- FAILED.
3>------ Rebuild All started: Project: WebView2LoaderBug.App, Configuration: Debug x86 ------
3>C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.App\WebView2LoaderBug.App.csproj : XamlCompiler error WMC1006: Cannot resolve Assembly or Windows Metadata file 'C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.LibWrapper\bin\Debug\net8.0-windows10.0.22621\WebView2LoaderBug.LibWrapper.dll'
3>CSC : error CS0006: Metadata file 'C:\Users\my-user\Documents\Develop\lab\WebView2LoaderBug\WebView2LoaderBug.LibWrapper\bin\Debug\net8.0-windows10.0.22621\WebView2LoaderBug.LibWrapper.dll' could not be found
3>Done building project "WebView2LoaderBug.App.csproj" -- FAILED.
========== Rebuild All: 1 succeeded, 2 failed, 0 skipped ==========
```

The reason for using a class library wrapper to consume the nuget package is because of bug https://github.com/microsoft/microsoft-ui-xaml/issues/6429.
