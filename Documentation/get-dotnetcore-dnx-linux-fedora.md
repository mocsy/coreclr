Get the .NET Core DNX SDK on Fedora Linux
=========================================

These instructions will lead you through acquiring the .NET Core DNX SDK via the [.NET Version Manager (DNVM)](https://github.com/aspnet/dnvm)  and running a "Hello World" demo on Linux. The instructions use a particular set of paths. You'll need to adjust if you want to use a different set.

These instructions are for .NET Core console apps. If you want to try out ASP.NET 5 on top of .NET Core - which is a great idea - check out the [ASP.NET 5 instructions](https://github.com/aspnet/home).

.NET Core NuGet packages and the .NET Core DNX SDKs are available on the [ASP.NET 'vnext' myget feed](https://www.myget.org/F/aspnetvnext), which you can more easily view on [gallery](https://www.myget.org/gallery/aspnetvnext) for the feed.

You can also [build from source](linux-instructions.md). 

Environment
===========

These instructions are written assuming Fedora 21.

Packages
--------

Install in a rool terminal the `libunwind`, `openssl-devel` and `unzip` packages: 

	yum install libunwind openssl-devel unzip

You also need a latest version of Mono, which is required for DNU. This is a temporary requirement. 

    rpm --import "http://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF"
	yum-config-manager --add-repo http://download.mono-project.com/repo/centos/
	yum upgrade
	yum install mono-devel

Alternative approach to install to /opts:

http://software.opensuse.org/download/package?project=home:tpokorra:mono&package=mono-opt
	
Alternatively you can try the one click install:

http://download.mono-project.com/repo/mono-complete.ymp
	
Certificates
------------

You need to import trusted root certificates in order to restore NuGet packages. You can do that with the `mozroots` tool.

    yum provides */mozroots
	mozroots --import --sync

Installing DNVM
===============

You need DNVM to acquire a (or multiple) .NET Execution Environment (DNX) SDKs. DNVM is simply a script, which doesn't depend on .NET.

	curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh && source ~/.dnx/dnvm/dnvm.sh
	 
You can see the currently installed DNX versions with `dnvm list`, which will display an empty set of installed runtimes.

	dnvm list

Installing the .NET Core DNX SDK
================================

You first need to acquire the Mono DNX. It doesn't include Mono, but is needed to use the DNX tools on top of Mono. In particular, the DNU command is not yet supported on .NET Core, requiring us to use Mono for this purpose (until DNU runs on .NET Core). Mono is the default DNX, do you can acquire it via `dnvnm upgrade`.

	dnvm upgrade -u

Next, acquire the .NET Core DNX SDK.

	dnvm install latest -r coreclr -u

You can see the currently installed DNX versions with `dnvm list`.

	dnvm list

```
Active Version              Runtime Arch Location             Alias
------ -------              ------- ---- --------             -----
       1.0.0-beta5-11855    mono         ~/.dnx/runtimes      default
  *    1.0.0-beta5-11855    coreclr x64  ~/.dnx/runtimes      
```

Write your App
==============

You need a Hello World application to run. You can write your own, if you'd like. Here's a very simple one:

```csharp
using System;

public class Program
{
    public static void Main (string[] args)
    {
        Console.WriteLine("Hello, Linux");
        Console.WriteLine("Love from CoreCLR.");
    }   
} 
```

Some people on the .NET Core team are partial to a demo console app on corefxlab repo which will print a picture for you. Download the [corefxlab demo](https://raw.githubusercontent.com/dotnet/corefxlab/master/demos/CoreClrConsoleApplications/HelloWorld/HelloWorld.cs) to the demo directory.

You need a `project.json` that matches your app. Use this one. It will work for both of the apps provided/referenced above. Save the project.json beside your app.

```
{
    "version": "1.0.0-*",
    "dependencies": {
    },
    "frameworks" : {
        "dnx451" : { },
        "dnxcore50" : {
            "dependencies": {
                "System.Console": "4.0.0-beta-*"
            }
        }
    }
}
```

Run your App
============

You need to restore packages for your app, based on your project.json, with `dnu restore`. You will need to run this command under the Mono DNX. Make sure that you are using that one.

	dnvm use 1.0.0-beta5-11855 -r mono
	dnu restore

You can run your app with .NET Core, although make sure to switch to that DNX.

    dnvm use 1.0.0-beta5-11855 -r coreclr
	dnx . run
	
	Hello, Linux
	Love from CoreCLR.
