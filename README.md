# liquidctl – liquid cooler control

_Cross-platform tool and drivers for liquid coolers and other devices_

[![Status of the tests](https://github.com/jonasmalacofilho/liquidctl/workflows/tests/badge.svg)](https://github.com/jonasmalacofilho/liquidctl/commits/master)
[![Status of the build for Windows](https://ci.appveyor.com/api/projects/status/n5lgebd5m8iomx42/branch/master?svg=true&passingText=windows%20exe&failingText=failed&pendingText=building)](https://ci.appveyor.com/project/jonasmalacofilho/liquidctl/branch/master)


```
# liquidctl list
Device ID 0: NZXT Smart Device (V1)
Device ID 1: NZXT Kraken X (X42, X52, X62 or X72)

# liquidctl initialize all
# liquidctl --match smart set fan1 speed 50
# liquidctl --match smart set led color fading 350017 ff2608
# liquidctl --match kraken set fan speed  20 30  30 50  34 80  40 90  50 100
# liquidctl --match kraken set sync color spectrum-wave

# liquidctl status
NZXT Smart Device (V1)
├── Fan 1                        PWM  
├── Fan 1 current               0.04  A
├── Fan 1 speed                 1035  rpm
├── Fan 1 voltage              11.91  V
├── Fan 2                          —  
├── Fan 3                          —  
├── Firmware version           1.0.7  
├── LED accessories                2  
├── LED accessory type    HUE+ Strip  
├── LED count (total)             20  
└── Noise level                   60  dB

NZXT Kraken X (X42, X52, X62 or X72)
├── Liquid temperature     28.1  °C
├── Fan speed               851  rpm
├── Pump speed             1953  rpm
└── Firmware version      6.0.2  
```

<!-- stop here for PyPI -->


## Table of contents

1. [Supported devices](#supported-devices)
2. [Installing on Linux](#installing-on-linux)
3. [Installing on Windows](#installing-on-windows)
4. [Installing on macOS](#installing-on-macos)
5. [The command-line interface](#introducing-the-command-line-interface)
6. [Automation and running at boot](#automation-and-running-at-boot)
    1. [Set up Linux using systemd](#set-up-linux-using-systemd)
    2. [Set up Windows using Task Scheduler](#set-up-windows-using-task-scheduler)
    3. [Set up macOS using launchd](#set-up-macos-using-launchd)
7. [License](#license)
8. [Related projects](#related-projects)


## Supported devices

### All-in-one liquid coolers

| Family | Documentation | Notes |
| --- | --- | --- |
| Corsair H80i GT, H100i GTX, H110i GTX | [documentation](docs/asetek-690lc.md) | <sup>_E_</sup> |
| Corsair H80i v2, H100i v2, H115i | [documentation](docs/asetek-690lc.md) | |
| EVGA CLC 120 (CL12), 240, 280, 360 | [documentation](docs/asetek-690lc.md) | |
| NZXT Kraken M22 | [documentation](docs/nzxt-kraken-x-3rd-generation.md) | |
| NZXT Kraken X40, X60 | [documentation](docs/asetek-690lc.md) | <sup>_E, L_</sup> |
| NZXT Kraken X31, X41, X61 | [documentation](docs/asetek-690lc.md) | <sup>_E, L_</sup> |
| NZXT Kraken X42, X52, X62, X72 | [documentation](docs/nzxt-kraken-x-3rd-generation.md) | |

### Other parts

| Family | Documentation | Notes |
| --- | --- | --- |
| Corsair HX750i, HX850i, HX1000i, HX1200i | [documentation](docs/corsair-hxi-rmi.md) | <sup>_E_</sup> |
| Corsair RM650i, RM750i, RM850i, RM1000i | [documentation](docs/corsair-hxi-rmi.md) | <sup>_E_</sup> |
| NZXT E500, E650, E850 | [documentation](docs/seasonic-e-series.md) | <sup>_E_</sup> |
| NZXT Grid+ V3 | [documentation](docs/nzxt-smart-device.md) | |
| NZXT HUE 2, HUE 2 Ambient | [documentation](docs/nzxt-smart-device-v2.md) | <sup>_E_</sup> |
| NZXT Smart Device | [documentation](docs/nzxt-smart-device.md) | |
| NZXT Smart Device V2 | [documentation](docs/nzxt-smart-device-v2.md) | <sup>E</sup> |

<sup>_E_</sup> _Experimental._  
<sup>_L_</sup> _Requires the `--legacy-690lc` flag._  


## Installing on Linux

Packages are available for certain Linux distributions and package managers:

 - ArchLinux/Manjaro: [python-liquidctl<sup>AUR</sup>](https://aur.archlinux.org/packages/python-liquidctl/), [python-liquidctl-git<sup>AUR</sup>](https://aur.archlinux.org/packages/python-liquidctl-git/)
 - Fedora: [liquidctl](https://apps.fedoraproject.org/packages/liquidctl)
 - Debian/Ubuntu: work in progress (issue #62), continue reading for manual installation instructions
 - Linuxbrew: tap [jonasmalacofilho/homebrew-liquidctl](https://github.com/jonasmalacofilho/homebrew-liquidctl)

Alternatively, it is possible to install liquidctl from PyPI or directly from the source code repository.  In these cases the following dependencies are necessary:

| Dependency | Arch Linux | Fedora | Ubuntu |
| --- | --- | --- | --- |
| Python 3.6+ | python | python3 | python3 |
| libusb-1.0 | libusb-1.0 | libusbx | libusb-1.0-0 |
| pkg_resources | python-setuptools | python3-setuptools | python3-pkg-resources |
| docopt | python-docopt | python3-docopt | python3-docopt |
| PyUSB | python-pyusb | python3-pyusb | python3-usb |
| cython-hidapi | python-hidapi | python3-hidapi | python3-hid |

If cython-hidapi is to be installed from sources or directly from PyPI, then build tools and development headers for Python, libusb-1.0 and libudev are also needed.

To install any release from PyPI, *pip* should be used:

```
# pip install liquidctl
# pip install liquidctl==<version>
```

For the latest changes and to contribute back to the project, it is best to clone the source code repository and install liquidctl from your local copy:

```
$ git clone https://github.com/jonasmalacofilho/liquidctl
$ cd liquidctl
# python setup.py install
```

_Note: in systems that default to Python 2, replace `pip` and `python` by `pip3` and `python3`._  


## Installing on Windows

A pre-built executable for the last stable version is available in [liquidctl-1.3.2-bin-windows-x86_64.zip](https://github.com/jonasmalacofilho/liquidctl/releases/download/v1.3.2/liquidctl-1.3.2-bin-windows-x86_64.zip).

Executables for previous releases can be found in the assets of the [Releases](https://github.com/jonasmalacofilho/liquidctl/releases) tab, and development builds can be found in the artifacts on the [AppVeyor runs](https://ci.appveyor.com/project/jonasmalacofilho/liquidctl/history).

Products that cannot use the generic Microsoft HID Driver require another driver that is compatible with libusb.  In most cases Microsoft WinUSB is recommended, which can be easily set up for a device with [Zadig](https://zadig.akeo.ie/).¹

Alternatively, it is possible to install liquidctl from PyPI or directly from the source code repository.  Pre-build liquidctl executables for Windows already include libusb, but when installing from PyPI or the sources this will need to be manually set up.

The libusb DLLs can be found in [libusb/releases](https://github.com/libusb/libusb/releases) (part of the `libusb-<version>.7z` files) and the appropriate (e.g. MS64) `.dll` and `.lib` files should be extracted to the system or python installation directory (e.g. `C:\Windows\System32` or `C:\Python36`).  Note that there is a [known issue in PyUSB](https://github.com/pyusb/pyusb/pull/227) that causes errors when the devices are released; the solution is to either manually patch PyUSB or stick to libusb 1.0.21.

To install any release from PyPI, *pip* should be used:

```
> pip install liquidctl
> pip install liquidctl==<version>
```

For the latest changes and to contribute back to the project, it is best to clone the source code repository and install liquidctl from your local copy:

```
> git clone https://github.com/jonasmalacofilho/liquidctl
> cd liquidctl
> python setup.py install
```

_¹ See [How to use libusb under Windows](https://github.com/libusb/libusb/wiki/FAQ#how-to-use-libusb-under-windows) for more information._


## Installing on macOS

liquidctl is available on Homebrew, and installing liquidctl using it is straightforward.

```
$ brew install liquidctl
$ brew install liquidctl --HEAD
```

By default the last stable version will be installed, but by passing `--HEAD` this can be changed to the last snapshot from this repository.  All dependencies are automatically resolved.

A [custom tap](https://github.com/jonasmalacofilho/homebrew-liquidctl) is also available, which can sometimes be slightly ahead of the official formula, as well as allows testing release candidates (i.e. devel versions).

It is also possible to install liquidctl from PyPI or directly from the source code repository.  In these cases, Python 3 and libsub must be installed first; the recommended way is with `brew install libusb`.  To install any release from PyPI, *pip* should be used:

```
$ pip3 install liquidctl
$ pip3 install liquidctl==<version>
```

To contribute back to the project, it is best to clone the source code repository and install liquidctl from your local copy:

```
$ git clone https://github.com/jonasmalacofilho/liquidctl
$ cd liquidctl
$ python3 setup.py install
```

_Note: installation into a virtual environment is recommended to avoid conflicts with Python modules instalelled with Homebrew.  The use of virtual environments is outside the scope of this document.  Their use will also restrict the availability of the liquidctl command to that virtual environment._


## Introducing the command-line interface

The complete list of commands and options can be seen with `liquidctl --help`, but a good place to start is to ask liquidctl to list all recognized devices.

```
# liquidctl list
```

In case more than one supported device is found, the desired one can be selected with `--match <substring>`, where `<substring>` matches part of the desired device's description using a case insensitive comparison.

More device properties can be show by passing `--verbose` to `liquidctl list`.  Any of these can also be used to select a particular product.  See `liquidctl --help` or the man page for more information.

Finally, devices can also be selected with `--device <ID>`, but these are not guaranteed to remain stable and will vary with hardware changes, liquidctl updates or simply normal enumeration order variance.

Devices will usually need to be initialized before they can be used, though each device has its own requirements and limitations.  This and other information specific to a particular device will appear on the documentation linked in the [supported devices](#supported-devices) section.

```
# liquidctl initialize
```

Most devices provide some status information, like fan speeds and liquid temperatures.  This can be queried for all devices or using the filtering methods mentioned before.

```
# liquidctl [options] status
```

Fan and pump speeds can be set to fixed values or, if the device supports them, custom profiles.

```
# liquidctl [options] set <channel> speed (<temperature> <percentage>) ...
# liquidctl [options] set <channel> speed <percentage>
```

Lighting is controlled in a similar fashion and, again, the specific documentation for each device will list the available channels, modes and additional options.

```
# liquidctl [options] set <channel> color <mode> [<color>] ...
```

Finally, the `--verbose` option will print some extra information, like automatically made adjustments to the user provided settings.  And if there is a problem, the `--debug` flag will make liquidctl output more information to help identify its cause; be sure to include this when opening a new issue.

_Note: when debugging issues with PyUSB or libusb it can be useful to set the `PYUSB_DEBUG` (`=debug`) or/and `LIBUSB_DEBUG` (`=4`) environment variables._


## Automation and running at boot

In most cases you will want to automatically apply your settings when the system boots.  Generally a simple script or a basic service is enough, and some specifics about this are given in the following sections.

If you need more control for some really ambitious automation, you can also write a Python program that calls the driver APIs directly.

### Set up Linux using systemd

On systems running Linux and Systemd a service unit can be used to configure liquidctl devices.  A simple example is provided bellow, which you can edit to match your preferences and save the result to `/etc/system.d/system/liquidcfg.service`.

```
[Unit]
Description=AIO startup service

[Service]
Type=oneshot
ExecStart=liquidctl set pump speed 90
ExecStart=liquidctl set fan speed  20 30  30 50  34 80  40 90  50 100
ExecStart=liquidctl set ring color fading 350017 ff2608
ExecStart=liquidctl set logo color spectrum-wave

[Install]
WantedBy=default.target
```

The unit can be started manually or set to automatically run during boot using standard Systemd tools:

```
# systemctl start liquidcfg
# systemctl enable liquidcfg
```

A slightly more complex example can be seen at [jonasmalacofilho/dotfiles](https://github.com/jonasmalacofilho/dotfiles/tree/master/liquidctl), which handles multiple devices and uses the LEDs to convey progress and alert of errors.

### Set up Windows using Task Scheduler

The configuration of devices can be automated by writing a batch file and setting up a new scheduled task for (every) log on.  The batch file can be really simple and consist of the invocations of liquidctl that would otherwise be done manually.

```batchfile
liquidctl set pump speed 90
liquidctl set fan speed  20 30  30 50  34 80  40 90  50 100
liquidctl set ring color fading 350017 ff2608
liquidctl set logo color spectrum-wave
```

Make sure that liquidctl is available in the context where the batch file will run: in short, `liquidctl --version` should work within a _normal_ Command Prompt window.

When not using a pre-built liquidctl executable, try installing Python with the option to set the PATH variable enabled, or manually add the necessary folders to the PATH. Alternatively, if you're using Anaconda, try adding the following line to the beginning of the file:

```batchfile
call %homepath%\Anaconda3\Scripts\activate.bat
```

A slightly more complex example can be seen in [issue #14](https://github.com/jonasmalacofilho/liquidctl/issues/14#issuecomment-456519098) ("Can I autostart liquidctl on Windows?"), that uses the LEDs to convey progress or eventual errors.

Chris' guide on [Replacing NZXT’s CAM software on Windows for Kraken](https://codecalamity.com/replacing-nzxts-cam-software-on-windows-for-kraken/) goes into a lot more detail and is a good read.

### Set up macOS using launchd

You can use a shell script and launchd to automatically configure your devices upon logging in.

Create a script in `/usr/local/bin/liquidcfg.sh` and make it executable; it should contain the calls to liquidctl necessary to initialize and configure your devices.

```bash
#!/bin/bash -xe
liquidctl set pump speed 90
liquidctl set fan speed  20 30  30 50  34 80  40 90  50 100
liquidctl set ring color fading 350017 ff2608
liquidctl set logo color spectrum-wave
```

Afterwards, create a new global daemon in `/Library/LaunchDaemons/local.liquidcfg.plist` that executes the previous script.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>ProgramArguments</key>
	<array>
		<string>/usr/local/bin/liquidcfg.sh</string>
	</array>
	<key>Label</key>
	<string>local.liquidcfg</string>
	<key>RunAtLoad</key>
	<true/>
	<key>KeepAlive</key>
	<false/>
	<key>EnvironmentVariables</key>
	<dict>
		<key>PATH</key>
		<string>/Library/Frameworks/Python.framework/Versions/?.?/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
	</dict>
</dict>
</plist>
```

It is important to adjust the location of Python 3 framework in the PATH environment variable above.  launchd agents use the system profile and thus will by default only find the Apple-provided Python 2.7 and its packages.

You can enable and disable the agent with `launchctl load|unload ~/Library/LaunchAgents/local.liquidcfg.plist`.  Errors can be found in `system.log` using Console; search for `liquidcfg` or `liquidctl`.

A real world example can be seen in [icedterminal/ga-z270x-ug](https://github.com/icedterminal/ga-z270x-ug/tree/master/post_install/pump_control).


## License

liquidctl – monitor and control liquid coolers and other devices.  
Copyright (C) 2018–2019  Jonas Malaco  
Copyright (C) 2018–2019  each contribution's author  

liquidctl includes contributions by CaseySJ and other authors.

liquidctl incorporates work by leaty, KsenijaS, Alexander Tong, Jens
Neumaier, Kristóf Jakab, Sean Nelson, Chris Griffith, notaz, realies
and Thomas Pircher.

Depending on how it is packaged, it might also bundle copies of
python, hidapi, libusb, cython-hidapi, pyusb and docopt.

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but without any warranty; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.


## Related projects

### [jonasmalacofilho/liquidctl-device-data](https://github.com/jonasmalacofilho/liquidctl-device-data)

Device information for developing and maintaining liquidctl, including USB descriptions, traffic captures and protocol analyses.

### [jonasmalacofilho/liquidtux](https://github.com/jonasmalacofilho/liquidtux)

Ongoing work on Linux kernel _hwmon_ drivers for some of the devices supported by liquidctl.  This allows standard monitoring tools (e.g. lm-sensors or tools built on top of it) to read the sensors in these devices.

### [audiohacked/OpenCorsairLink](https://github.com/audiohacked/OpenCorsairLink)

Command-line tool to control Corsair all-in-one liquid coolers and other devices.

### [jaksi/leviathan](https://github.com/jaksi/leviathan) and [brkalmar/leviathan](https://github.com/brkalmar/leviathan)

Linux kernel device drivers for second and third generation NZXT Kraken X coolers.

### [KsenijaS/krakenx](https://github.com/KsenijaS/krakenx)

A related cross-plataform interface for controlling third generation NZXT Kraken X coolers.

_A special thanks to all krakenx contributors; this project would not exist were not for it._
