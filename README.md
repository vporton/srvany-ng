**This is `srvany-next`, a modified `srvany-ng` that uses the command line instead of the registry to
read the command and parameters. It is invoked like `srvany-ng.exe command.exe arg1 ... argN`.
It seems that `CreateProcess` function does not return, but during testing, it behaved as if everything would be good.**

This modified `srvany-ng` is better than the original (but incompatible with it), because it allows to (un)install
services entirely by actions of installers without using external commands as custom actions.

Use it with [WiX](https://wixtoolset.org) like:

```xml
<Component Id="ServiceComponent" Guid="XXX" Directory="SrvAnyDIR">
    <File Id="SrvAnyFile" Source="srvany-next\src\srvany-next.exe" KeyPath="yes" />
    <ServiceInstall
        Id="MyServiceInstall"
        Type="ownProcess"
        Vital="yes"
        Name="XXX"
        DisplayName="XXX"
        Description="XXX"
        Start="demand"
        Account="LocalSystem"
        ErrorControl="normal"
        Arguments='"[NETHERMIND_DIR]Nethermind.Runner.exe" --config [ConfigDir]config.cfg' />
    <ServiceControl
        Id="MyServiceControl"
        Name="XXX"
        Stop="both"
        Remove="uninstall"
        Wait="yes" />
</Component>
```

# srvany-ng: Run any Windows application as a Service
A drop in, compatible, replacement for the useful "srvany.exe", found in the Windows Server Resource Kit.<br />
Written in C, for Windows XP and newer.


## Support Me
[![Donate Via Paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CALMNQUWLZNYL)
<br /><br />
## Differences to the original
* Open Source
* 32 and 64 bit binary available
* No OS/2 application support
* Service will stop if the launched application exits

## Building
Solution is known to build with Visual Studio 2013, no additional dependencies are needed. The project uses the "v120_xp" toolset to ensure XP / 2003 support, and links to the MSVCRT statically.
<br /><br />

## Installing
Place srvany-ng in an accessible folder on your system.
Install it as a service from an Elevated (Administrator) Command Prompt:
```winbatch
sc create "MyServiceName" start= auto binPath= "C:\Path\To\srvany-ng.exe"
sc description MyServiceName "My services description"
```
Note the spaces between `start=`, `binPath=` and their parameters. This is intended.

Now, open the Registry editor (`regedit.exe`), and browse to:
`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MyServiceName`

Create a new Key named "Parameters".  
In the parameters key, create a new String value named "Application". The value should be the file path to the application you wish to run as a service.

#### Optional Parameters
| Value name     | Value type         | Description                                                                                       |
| ---------------| ------------------ | ------------------------------------------------------------------------------------------------- |
| AppDirectory   | String value       | The starting directory for your application. Usually the same as the folder its executable is in. |
| AppParameters  | String value       | Command line arguments to pass to your application on startup.                                    |
| AppEnvironment | Multi-String value | Environment variables to set for your application.                                                |

## Further Reading
Microsoft support article describing the use of the original srvany.exe: https://support.microsoft.com/en-us/kb/137890
<br />
