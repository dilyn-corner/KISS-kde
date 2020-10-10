|/  
|\ISS                                                           https://k1ss.org
________________________________________________________________________________


KISS-kde - A KISS repository for the Plasma Desktop
________________________________________________________________________________

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
contribution, feel free to share it. Requests like this should follow the
KISS [style guide](https://k1ss.org/wiki/kiss/style-guide) as closely as 
possible, but I'm not too much of a stickler.

## Current Milestones

Here are all of the things that can be worked on. 

- [x] Cleanup repository structure

- [ ] Make patches to remove extraneous dependencies
    - [ ] `elogind - /usr/bin/realpath --relative-to`
    - [ ] `libblockdev - /usr/bin/mktemp --tmpdir`
    - [ ] `udisks2 - /usr/bin/ln -r`
    - [x] `gnugrep - /usr/bin/grep --quiet`

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

- [x] Remove JS backend from polkit
    * This project will rely on forward-porting the work done [here](https://dev.getsol.us/T4824)
    *  The goal is that we can drop `mozjs` - it's the only package which
       requires `mozjs` and it means we can also drop `nspr`, which I have been
       to lazy to make coincide 'cleanly' with `community/nss`. 
    *  The currently milestone is now to fix one of the patches we're using in
       `polkit` to  drop `mozjs`; we basically need to get rid of innetgr from
        the code so we drop our `musl` patch (which Rich Felker does not approve of).

- [x] Enable a login-manager
    - [x] Bundle a default theme (breeze)
    - [ ] Package an alternative
        - [ ] `seatd` to replace `elogind`
        - [ ] `greetd` + greeter to replace `sddm`
        This change would mean that `rust` is a build-time requirement. Gross.
        However, ideally this can be the default, and people can seek out the
        `elogind` stack themselves. If they... want... that...

- [ ] Expand `plasma-desktop` for a 'comprehensive' alternative, `plasma`
    - [x] login manager - `sddm` for now!
    - [x] `udisks2`
    - [ ] `vaultcrypt`
    - [ ] `networkmanager`
    - [ ] `bluez`
    - [ ] `pulseaudio`

- [ ] Package some KDE apps
    - [ ] `krita`
    - [ ] `kdenlive` - halts at splash
    - [ ] `kwave`
    - [ ] `dragon-player` - doesn't play tracks
    - [ ] `kaffeine`
    - [ ] `elisa` - immediately crashes
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
    - [x] `latte-dock`

- [ ] Test `wayland` support
    - [ ] `plasma 5.20.0` - IT WILL BE THE DEFAULT.

- [ ] Ensure everything works!
    - [x] Sessions can start from `startx`
    - [x] Sessions can start from `sddm`
    - [x] Applications launch
    - [x] Menus work
    - [x] Themes work - bugged in 5.19.2?
    - [x] Settings work
    - [ ] Users can log back in from a *lock* (super+l)
    - [ ] Bluetooth
    - [ ] Power management
    - [ ] Disk/hardware management 

- [x] Create tarball

- [ ] Live USB
    - [ ] initramfs
        - [ ] `tinyramfs`
        - [ ] `dracut`
        - [ ] `allyesconfig` kernel /s
    - [ ] `ncurses`-style installer?

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
the 5 to a @ so `kiss` doesn't complain.


### xorg vs wayland 

The eternal debate.

Starting with the release of `plasma 5.20.0`, `wayland` will be the presumptive
default for KDE. I'm unsure to what extent they plan to leave `xorg` available,
but as it stands many parts of KDE require xorg pieces to function. Wayland is
an exciting project, and having been using it on my own personal KISS box, I can
recommend it heartily over `xorg` in general - it seems to have matured as a
protocol, there is still incredibly active development work (as opposed to
critical-bug-fixing-only for `xorg`), and the performance is quite stellar.
Additionally, `kwinft` is a fork of `kwin` which purports to better support
wayland. It is a more bleading-edge, development focused 'branch' of `kwin`. 
Bound to be bugs that crop up. Presumably, `kwinft` is strictly better than `kwin`. 
Currently, it's your choice which you choose! If you opt to use `kwinft`, simply 
comment `kwin` from `plasma-desktop/depends` and `plasma-workspace/depends` 
and uncomment `kwinft`, and then simply install `plasma-desktop` or `plasma`. 
If you have already installed `kwin` fear not! Do the comment switcheroo as before,
reinstall *those* packages (which should pull-in everything required for `kwinft`),
and then simply kill your KDE session if you're in one, uninstall `kwin`, and 
restart the session! If you run into bugs, please make sure they're not `kwinft` 
related - burn down the [developer's door](https://gitlab.com/kwinft/kwinft) for those. 


## Prerequisites

1. This all (currently) assumes a *working* `xorg-server`.

2. You will need `dbus`. Because of this, you'll have to rebuild `qt5`.

3. You will need `eudev` or `libudev-zero`. Because of this, you'll need to
   rebuild `xorg-server`, any input packages (`libinput`,
   `xf86-input-libinput`, etc.), `dhcpcd`, perhaps others.

These rebuilds are obviously not required if you already had
the relevant programs built against `dbus`, `eudev`, etc. To determine which
packages are built against `dbus` or `eudev` merely run `kiss-revdepends eudev`.
If you see `xorg-server`, you're probably fine.

If you opt to use `elogind` (required for `sddm`), you will need the following:

4. [cgroups](http://www.linuxfromscratch.org/blfs/view/svn/general/elogind.html). I leave it up to
   you to test your own kernel configs.

5. You will require exactly `realpath` from `coreutils` to
   build `elogind`.

`coreutils` is a build time requirement, so you are free to remove
it with no ill-effects afterwards.


## Getting Started

Now that we have all of that nonsense out of the way, let's
get to it!

We start with the assumption that you just installed KISS,
following the [installation guide](https://k1ss.org/install)
exactly, which means you have `libudev-zero` or `eudev`, a working `xorg` that
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
$ git clone https://github.com/dilyn-corner/KISS-kde # pls

# Add relevant repository paths

$ export KISS_PATH="$HOME/KISS-kde/extra:$KISS_PATH"
$ export KISS_PATH="$HOME/KISS-kde/plasma:$KISS_PATH"
$ export KISS_PATH="$HOME/KISS-kde/frameworks:$KISS_PATH"
$ export KISS_PATH="$KISS_PATH:$HOME/community/community"

# If you don't already have it,

# replace `libudev-zero` with `eudev` if you prefer

$ kiss b dbus libudev-zero && kiss i dbus libudev-zero

# If you just did the previous,

$ kiss b xorg-server libinput xf86-input-libinput 
# Add others as necessary

# Thanks to the alternatives system, we can just pluck out
# the few binaries we need from `coreutils`, along with the grep utility.
# You only require coreutils if you opt to use `elogind` or install `plasma`. 

$ kiss b coreutils && kiss i coreutils
$ kiss a coreutils /usr/bin/realpath
$ kiss a coreutils /usr/bin/mktemp
$ kiss a coreutils /usr/bin/ln

# We're finally ready!

$ kiss b plasma-desktop # or plasma

~~~ WAIT TWELVE HOURS ~~~

$ kiss i plasma-desktop # or plasma

# Start the eudev and dbus services for convenience
# This is ultimately not necessary
$ ln -sv /etc/sv/dbus  /var/service
$ ln -sv /etc/sv/udevd /var/service

$ sv up dbus
$ sv up udevd

# Two options: 
# 1. Use 'startx' to launch KDE
# 2. Use a login manager

# For startx:

$ pkill X
$ echo "exec dbus-launch --exit-with-session startplasma-x11" >> ~/.xinitrc
# Replace 'x11' with 'wayland' in the previous command to launch a wayland session

$ startx

# For sddm:

$ kiss b sddm && kiss i sddm

# Enable the required services. FoR sEcUrItY

$ ln -sv /etc/sv/polkitd /var/service
$ ln -sv /etc/sv/elogind /var/service
$ ln -sv /etc/sv/sddm    /ver/service

$ sv up polkitd
$ sv up elogind
$ sv up sddm    # should launch sddm
```

__ALTERNATIVELY__ you can install KISS with KDE already built and ready to go!
It's basically just a KISS tarball but twenty times the size (because KDE). It
is a build of `plasma-desktop` with:

`C(XX)FLAGS=-march=x86-64 -mtune=generic -pipe -Os`

The first release is built from a fresh KISS tarball. All future releases will
merely be published after performing updates on this original tarball.
Installing works almost identically to the [usual
instructions](https://k1ss.org/install). Just download the `kiss-kde-$VER.tar.xz`
from the releases tab and:

```
# Mount your relevant root partition to wherever. In this case, I choose /mnt
$ tar xf kiss-kde-$VER.tar.xz -C /mnt
$ ./mnt/bin/kiss-chroot /mnt

# Follow the KISS install guide - after the 'Rebuild KISS' section

$ exit
$ reboot

# Boot into your freshly installed KISS
# Login (user: root; pass: toor)
$ startx

# You should be greeted with KDE.
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
