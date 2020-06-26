# KISS-kde


![alt
text](https://github.com/dilyn-corner/KISS-kde/blob/master/06-23%4021:48:21.jpg)


This is currently a work in progress. Feel free to help out!

KDE is a full-featured desktop environment for Linux! It includes a framework
to allow for integration with the system, utilizing `dbus` and `pam`, along with
`plasma`, a beautiful and modern desktop.

This is very much in alpha. I will keep this repository up-to-date as best I
can, testing and building things as frequently as possible - it's a big
project that requires a fair amount of maintenance. Luckily, I have the hard
drive space and the free time. But I'm only one person; if you have a
contribution, feel free to share it. If you package some KDE apps, feel free
to submit a pull request for inclusion. Requests like this should follow the
KISS [style guide](https://k1ss.org/wiki/kiss/style-guide) as closely as 
possible, but I'm not too much of a stickler.

## Current Milestones

Here are all of the things that can be worked on. 

- [x] Cleanup repository structure

- [ ] Make patches to remove extraneous dependencies
    - [ ] `elogind - /usr/bin/realpath --relative-to`
    - [ ] `libblockdev - /usr/bin/mktemp --tmpdir`
    - [ ] `udisks2 - /usr/bin/ln -r`
    - [ ] `gnugrep - /usr/bin/grep --quiet`

- [ ] Properly configure docbook generation
    * Currently, `docbook-xsl` doesn't actually do anything, I don't think.

- [x] Get the core working
    - [x] Generate build files
    - [x] Make sure the core builds
    - [x] Ensure KDE launches

- [x] Find a *sane* default collection, `plasma-desktop`
    - [x] KDE Framework
    - [x] Plasma
    - [x] `systemsettings`
    - [x] `breeze`
    - [x] `breeze-icons`

- [x] Configure `linux-pam` in a meaningful way

- [x] Enable a login-manager
    - [x] Bundle a default theme (breeze)
    - [ ] Package an alternative

- [ ] Expand `plasma-desktop` for a 'comprehensive' alternative, `plasma`
    - [x] `sddm`
    - [x] `udisks2`
    - [ ] `vaultcrypt`
    - [ ] `networkmanager`
    - [ ] `bluez`
    - [ ] `pulseaudio`

- [ ] Package some KDE apps
    - [ ] `krita`
    - [ ] `kdenlive`
    - [ ] `kwave`
    - [ ] `dragon-player`
    - [ ] `kaffeine`
    - [ ] `elisa`
    - [ ] `calligra`
    - [ ] `kdevelop`
    - [x] `konsole`
    - [ ] `yakuake`
    - [ ] `okular`
    - [ ] `gwenview`
    - [ ] `spectacle`
    - [ ] `kate`
    - [x] `dolphin`
    - [ ] `filelight`
    - [ ] `ark`
    - [x] `falkon`
    - [ ] `konqueror`
    - [ ] `kget`
    - [ ] `kmail`
    - [ ] `kwalletmanager`
    - [ ] `kgpg`
    - [ ] `ktorrent`
    - [ ] `latte`

- [ ] Ensure everything works!
    - [x] Sessions can start from `startx`
    - [x] Sessions can start from `sddm`
    - [x] Applications launch
    - [x] Menus work
    - [x] Themes work
    - [x] Settings work
    - [ ] Users can log back in from a *lock* (super+l)
    - [ ] Bluetooth
    - [ ] Power management
    - [ ] Disk/hardware management 

- [ ] Create an installation tarball similar to how KISS is distributed
    - [ ] Live USB (?)


This list will be expanded, contracted, and refined as necessary. Feel free to
assist. 

---

## KISS-kde with respect to KISS

### gettext

I have opted not to include internationalization. This was done in
two parts:

1) Hobble `ki18n` by removing the `gettext` dependency

2) Delete all translation files located in `po` from packages which 'require' them. 

If you would like to have languages besides english available, it should be
quite easy. First, package `gettext`. Second, remove the patch in the `ki18n`
package. Finally, simply remove `rm -rf po` from each build script in which it
appears. This is probably trivial (cough `sed` cough). 

Additionally, there are packages which I had to go further to remove `gettext`
and `intltool` from. It shouldn't be too hard to find them on your own (the
patches are sanely named, `remove-gettext.patch` and `remove-intltool.patch`).
Fix up the build files as required!


### dbus

The dreaded(?) question.

Based on my current knowledge of KDE, `dbus` is required by
a litany of programs. Many which don't explicitly require
`dbus` will nontheless build against it if it is available.
The hard part, however, is not the framework. Instead, it is
for launching KDE itself. 

If you attempt to `startx startplasma-x11`, you will
probably be met with an error message about `dbus`. You see,
KDE requires something like `polkit`, `elogind`, etc. to
start. Indeed even with `wayland` this is the case. Additionally, `dbus` is used
by integral parts of the system to do... Most things. Which is why KDE tries so
hard to ensure it is running before it is launched.

Which would seem to make `dbus` a hard dependency for KDE.

Unless we can find a way around this (and I am certainly not
the one to do it!), `dbus` is required. I have no real
problems with this. If you take umbridge with this hard
fact, perhaps reflect on why you want KDE in the first
place?

### qt

The engine that make it go!

The qt dependency includes packages which overlap with community, like `qt5`.
Additionally, `qt5` requires packages from community at build time. If a package
in community has been changed, it will be forked into this repository. As a
result, you should place this repository in your `$KISS_PATH` __before__
community. 

`qt5*`, specifically `qt5-webengine`, take a very long time to build (thanks,
chromium). As a result, I have included along with this repository a KISS
tarball of the fully built packages for the three parts of the `qt5` suite that
take more than twenty minutes to build. These are compiled assuming you've
satisfied the dependencies of *this* repository. Built with everyone's favorite
GNU C compiler, with `CFLAGS=-march=x86-64 -mtune=generic -Os -pipe`. If you
want a 'seamless' build, merely plop these archives in
`${XDG_CACHE_DIR:-$HOME/.cache}/kiss/bin`, and make sure you change the . before
the 5 to a # so `kiss` doesn't complain.


### xorg vs wayland 

The eternal debate.

You can use either `xorg` or `wayland` as your backend. I have only ever used
`xorg`; as such, I'm not certain what `wayland` may or may not require. It's
certainly an option you can explore though! It's quite simple. If you opt for
`xorg`, you get `wayland` for free. And you don't have a choice in the matter. 
`wayland` support has 'a long way to go' in KDE yet. But `kwin` was forked, so
we will presumably see better `wayland` support in the future. This fork,
`kwinft`, is included in this repository and *is* an option you can take! I'm
using `kwinft` with `xorg` and these windows wobble just fine.

`kwinft` is a promising, development-heavy project. As a result, there are 
bound to be bugs that crop up. Presumably, `kwinft` is strictly better than `kwin`, 
because something something bleeding edge. It's your choice which you choose! If 
you opt to use `kwinft`, simply comment `kwin` from `plasma-desktop/depends` and
`plasma-workspace/depends` and uncomment `kwinft`, and then simply
install `plasma-desktop` or `plasma`. If you have already installed `kwin`
fear not! Do the comment switcheroo as before, reinstall *those* packages
(which should pull-in everything required for `kwinft`), and then simply kill
your KDE session if you're in one, uninstall `kwin`, and restart the session!
If you run into bugs, please make sure they're not `kwinft` related - burn down
the [developer's door](https://gitlab.com/kwinft/kwinft) for those. 


## Prerequisites

1. This all assumes a *working* `xorg-server`.

2. [cgroups](http://www.linuxfromscratch.org/blfs/view/svn/general/elogind.html) may be required for `elogind`. I leave it up to
   you to test your own kernel configs.

3. You will require exactly `realpath` from `coreutils` to
   build `elogind`. The others are for extras in KISS-kde/extra.

4. You will need `gnugrep` to build `breeze-icons`.

5. You will need `dbus`. Because of this, you'll have to rebuild `qt5`.

6. You will need `eudev`. Because of this, you'll need to
   rebuild `xorg-server`, any input packages (`libinput`,
   `xf86-input-libinput`, etc.), `dhcpcd`, perhaps others.

These rebuilds are obviously not required if you already had
the relevant programs built against `dbus`, `eudev`, etc. To determine which
packages are built against `dbus` or `eudev` merely run `kiss-revdepends eudev`.
If you see `xorg-server`, you're probably fine.


## Getting Started

Now that we have all of that nonsense out of the way, let's
get to it!

We start with the assumption that you just installed KISS,
following the [installation guide](https://k1ss.org/install)
exactly, which means you have `eudev`, a working `xorg` that
knows about `eudev`, some input drivers, and fonts. Although
this repo does include some nice fonts, so either way you're
covered. 

You might have a few extra programs, like `pcre2` and some
`xcb` already installed. Great! It might save you time. Not
really. You shouldn't have too many conflicts to deal with,
if any. Just make sure you've uninstalled `qt5` and
friends.

__NOTE__: I have taken the liberty of uploading a KISS package for `qt5`,
`qt5-webengine`, and `qt5-declarative`. Assuming your system is roughly similar
to mine, and you've installed all of their dependencies, you can simply install
these xz archives instead of wasting ten hours building them. Trust me.

You have two primary choices on what to install to get a working desktop. 
Either you can install `plasma-desktop`, or you can install `plasma`. 

`plasma-desktop` includes basically every single item in the framework you would
need in order to have a recognizable KDE experience. It has icons, fonts, some
default things like an application launcher, a system monitor, a settings
manager.

If you want a more 'full-featured' KDE environment, you can install `plasma`.
This 'package' will come with 20 additional packages, and it fleshes out our
fledgling project with features one might expect to exist.
It isn't that much more, so there's no real reason not to use it.
This expanded list includes things like `bluez`. It might be worth it for you.

To get a 'minimal' KDE (it's over a hundred packages),
simply install `plasma-desktop`. How convenient! Here's how
you do it:

__NOTE__: It doesn't matter where you keep these repos (or
any KISS repos, for that matter). I keep mine in
`$HOME/git`. For now, assume I've done `git clone` in `$HOME`.

```
$ git clone https://github.com/kisslinux/community   # Clone
$ git clone https://github.com/sdsddsd1/mywayland    # Repos
$ git clone https://github.com/dilyn-corner/KISS-kde # pls

# Start with a clean path, get a new one.

$ . /etc/profile.d/kiss_path.sh
$ exprot KISS_PATH="$KISS_PATH:$HOME/community/community"
$ export KISS_PATH="$HOME/mywaland/wayland:$KISS_PATH"
$ export KISS_PATH="$HOME/KISS-kde/extra:$KISS_PATH"
$ export KISS_PATH="$HOME/KISS-kde/plasma:$KISS_PATH"
$ export KISS_PATH="$HOME/KISS-kde/frameworks:$KISS_PATH"

# If you don't already have it,

$ kiss b dbus eudev && kiss i dbus eudev

# If you just did the previous,

$ kiss b xorg-server libinput xf86-input-libinput 
# Add others as necessary

# Thanks to the alternatives system, we can just pluck out
# the few binaries we need from `coreutils`, along with the grep utility.

$ kiss b coreutils gnugrep && kiss i coreutils gnugrep
$ kiss a coreutils /usr/bin/realpath
$ kiss a coreutils /usr/bin/mktemp
$ kiss a coreutils /usr/bin/ln
$ kiss a gnugrep   /usr/bin/grep

# We're finally ready!

$ kiss b plasma-desktop # or plasma

~~~ WAIT TWELVE HOURS ~~~

$ kiss i plasma-desktop # or plasma

# There are some circular dependencies we should work around

$ kiss b elogind && kiss i elogind

# Perhaps we start the dbus and udev services?
$ ln -sv /etc/sv/dbus  /var/service
$ ln -sv /etc/sv/udevd /var/service

$ sv up dbus
$ sv up udevd

# Two options: 
# 1. Use 'startx' to launch KDE
# 2. Use a login manager

# For startx:

$ pkill x
$ echo "exec dbus-launch --exit-with-session startplasma-x11" >> ~/.xinitrc
# Ensure you don't have anything else execing before this

$ startx

# For a login manager:

$ kiss b sddm && kiss i sddm

# Enable the required services. FoR sEcUrItY

$ ln -sv /etc/sv/polkitd /var/service
$ ln -sv /etc/sv/elogind /var/service
$ ln -sv /etc/sv/sddm    /ver/service

$ sv up polkitd
$ sv up elogind
$ sv up sddm    # should launch sddm
```

--- 

## How you can help

I need testers! People who just use the desktop to navigate through digital
space and time. Does an application segfault? Does something simply fail to
launch? I need to know! Do you run into build-time errors? Run-time? What are
they! I have already run into a few, I've identified fixes for many and am
working on solutions to others. But in the end, I am a simple person with simple
needs. I cannot guarantee that everything works because I don't plan on using
everything available. So jump in! Make a package! Fix bugs! Be my fren! But most
importantly...


## Enjoy!

![alt
text](https://github.com/dilyn-corner/KISS-kde/blob/master/06-23%4001:45:16.jpg)

