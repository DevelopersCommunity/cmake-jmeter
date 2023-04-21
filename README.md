# Apache JMeter installer

This is a CMake project to build a Windows installer for [Apache
JMeter](https://jmeter.apache.org/). The installer is used by the
`DEVCOM.JMeter` [winget](https://learn.microsoft.com/windows/package-manager/)
package. You can install it with the following command:

```powershell
winget install --id DEVCOM.JMeter

```

## Build prerequisites

- [CMake](https://cmake.org/)
- [Ninja](https://ninja-build.org/)
- [WiX v3](https://wixtoolset.org/docs/wix3/) (if packaging on Windows)

You can install the first two components with the following
[winget](https://learn.microsoft.com/windows/package-manager/winget/) commands:

```powershell
winget install --id Kitware.CMake
winget install --id Ninja-build.Ninja
```

WiX v3 is available at <https://github.com/wixtoolset/wix3/releases/>. It
requires [.NET Framework 3.5](https://learn.microsoft.com/dotnet/framework/install/dotnet-35-windows),
which can be installed with the following command:

```powershell
Start-Process `
    -FilePath pwsh `
    -ArgumentList "-Command `"& {Enable-WindowsOptionalFeature -Online -FeatureName NetFx3}`"" `
    -Wait `
    -Verb RunAs
```

Update your path environment with `setx` (supposing you installed the tools in
their default directories):

```powershell
setx PATH ($(Get-ItemProperty -Path HKCU:\Environment -Name Path).Path + "${env:ProgramFiles(x86)}\WiX Toolset v3.11\bin")
```

Check if `PATH` was set correctly:

```
Get-Command cmake
Get-Command ninja
Get-Command candle
```

You may need to restart your Windows session if you can't find `ninja` even
after restarting your terminal.

## Build

Use the following commands to generate the installation package:

```powershell
cmake -B build -G Ninja
cmake --build build --target package
```

You need Internet access to execute the build process to download the Apache
JMeter binaries.

If successful, the MSI file will be available in the `build` directory.
