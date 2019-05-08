# mconnect
mconnect - KDE Connect protocol implementation in Vala/C, modified from the original for use on Raspbian lite

GLib and Gio should be available even on trimmed down systems. Vala is really
needed only at build time. Json-glib does packet parsing. Libnotify is
responsible for displaying shell popups.

# Building

Build dependencies (using package names as found in Raspbian Stretch Lite):

- valac
- valac-0.34-vapi
- libvala-0.34-0
- libglib2.0-bin
- libglib2.0-devel-dev
- libpcre3-dev
- libpcre32-3
- libcrecpp0v5
- 
- gobject-introspection-devel(maybe not?)
- gobject-introspection(maybe)
- python3-mako(maybe)
- python3-markupsafe(maybe)
- libgee-dev
- gir1.2-gee-1.0
- libgee2
- json-glib-1.0-0
- json-glib-1.0-common
- gnutls-devel(maybenot)
- libghc-gnutls-dev
- ghc
- binfmt-support
- libbsd-dev
- libghc-monads-tf-dev
- libgmp-dev
- libgmpxx4ldbl
- libgnutls-dane0
- libgnutls28-dev
- libgnutlsxx28
- libidn11-dev
- libllvm3.7
- libp11-kit-dev
- libtasn1-6-dev
- libtasn1-doc
- libunbound2
- llvm-3.7
- llvm-3.7-dev
- llvm-3.7-runtime
- nettle-dev
- libnotify-dev
- gir1.2-gdkpixbuf-2.0
- gir1.2-notify-0.7
- libgdk-pixbuf2.0-dev
- libnotify4
- libpthread-stubs0-dev
- libx11-dev
- libx11-doc
- libxau-dev
- libxcb1-dev
- libxdmcp-dev
- x11proto-core-dev
- xllproto-input-dev
- xllproto-kb-dev
- xorg-sgml-doctools
- xtrans-dev
- gtk3-devel(maybe)
- libgtk3.0-cli
- libgtk3.0-cli-dev
- at-spi2-core-devel (and at-spi2-atk)(maybe not)
- at-spi2-core
- libatspi2.0-dev
- gi1.2-atspi-2.0
- libdus-1-dev
- libxext-dev
- libxtst-dev
- x11proto-fixes-dev
- x11proto-record-dev
- x11proto-xext-dev
- meson
- ninja-build
- pkg-config

or see `extra/travis-build` in the source tree for example installation
commands. Once build deps are in place, run:

    mkdir build
    cd build
    meson ..
    ninja
    ninja install
    # to set a custom installation directory run:
    #   DESTDIR=<somedir> ninja install

# Configuration

**NOTE**: manual configuration file is no longer needed

A sample configuration file is provided in source tree, see
`mconnect.conf`. It will get installed to `${datadir}/mconnect/`
(usually corresponding to `/usr/share/mconnect/`) by default. Once
`mconnect` starts it will pick the default file and make a copy of it
in user's config directory, specifically `~/.config/mconnect/`.

A device described in it's own group and listed in `main.devices`, has
to match exactly with incoming identity packets. However, since
`deviceId` is not known beforehand, neither shown in KDE Connect
Android application, only `name` and `type` are used for matching.

# Usage

mconnect comes are 2 separate programs, the daemon - `mconnect` and a D-Bus
client `mconnectctl`.

## The daemon

Start it by running:

```
$ mconnect -d
```

The daemon starts listens on `0.0.0.0:1714` for incoming UDP packets. Once an
identity packet (a sort of a handshake) is received, a connection to the
sender's address will be made. Known devices are cached in
`~/.cache/mconnect/devices`.

File sharing to a device requires TCP ports 9970-9975 to be open. Files shared
from the device are saved to `~/Downloads/mconnect/`.

## The client

List discovered devices:

```
$ mconnectctl list-devices
Devices:
    /org/mconnect/device/0    673ac2db27d2a331 - Motorola Moto G Maciek
```

Accept a device (previously done only through the configuration):

```
$ mconnectctl allow-device /org/mconnect/device/0
```

Show device details:

```
$ mconnectctl show-device /org/mconnect/device/0
Device 
  Name: Motorola Moto G Maciek
  ID: 673ac2db27d2a331
  Address: 192.168.1.103:1716
  Type: phone
  Allowed: true
  Paired: true
  Active: true
  Connected: true
```

Share a file/URL/text:

```
$ mconnectctl share-file /org/mconnect/device/0 <path>
$ mconnectctl share-url /org/mconnect/device/0 www.google.com
$ mconnectctl share-text /org/mconnect/device/0 'battery horse staple'
```


## DBus API

The API is not documented. Use D-Feet or busctl to introspect and invoke methods
manually.

## Gnome Shell

A Gnome Shell extension is available here:
https://github.com/andyholmes/gnome-shell-extension-mconnect or via the GNOME
extensions page: https://extensions.gnome.org/extension/1272/mconnect/


# Firewalls

It may be required to either temporarily disable the firewall or open up UDP
port 1714.

An example service definition for [firewalld](http://www.firewalld.org/) is
provided in `extra/firewalld/mconnect.xml` directory. The file needs to be
copied into `/etc/firewalld/services`.

# Contributing

Please open a Pull Request with your changes. Feel free to ping me (@Vontux)
in description.If you want to do add any features as oppsed to improvements to make this run better under Raspbian, please do a pull request on the original project https://github.com/bboozzoo/mconnect .
