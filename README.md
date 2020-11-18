|/
|\ISS                                                           https://k1ss.org
________________________________________________________________________________


KISS-kde - A KISS repository for the Plasma Desktop
________________________________________________________________________________

# KISS-kde


![Plasma 5.20.1
Clean](https://raw.githubusercontent.com/dilyn-corner/KISS-kde/master/10-26%2522%3A22%3A22.png)


This is currently a work in progress. Feel free to help out!

KDE is a full-featured desktop environment for Linux! It uses the Qt framework
to generate a simple, featureful, and gorgeous desktop.

This is very much in alpha. If you have a contribution, feel free to share it.
Requests like this should follow the KISS [style
guide](https://k1ss.org/wiki/kiss/style-guide) as closely as possible, but I'm
not too much of a stickler.

## Current Milestones

Here are all of the things that can be worked on. 

- [x] Cleanup repository structure

- [ ] Make patches to remove extraneous dependencies
    - [ ] `elogind - /usr/bin/realpath --relative-to`
    - [ ] `libblockdev - /usr/bin/mktemp --tmpdir`
    - [ ] `udisks2 - /usr/bin/ln -r`
    - [x] `breeze-icons - /usr/bin/grep --quiet`

- [x] ~~Properly configure docbook generation~~
    * ~~Currently, `docbook-xsl` doesn't actually do anything, I don't think.~~
    * This has been avoided because we just dropped the requirement.

- [x] Get the core working
    - [x] Generate build files
    - [x] Make sure the core builds
    - [x] Ensure KDE launches

- [x] Find a *sane* default collection, `plasma-desktop`
    - [x] KDE Framework
    - [x] Plasma

- [x] Configure `linux-pam` in a meaningful way

- [x] Remove JS backend from polkit
    * This project will rely on forward-porting the work done
      [here](https://dev.getsol.us/T4824)
    * This allows us to drop `mozjs`
    * The currently milestone is to get rid of innetgr from our patches. 
    * Once innetgr is no longer required, we can drop our `musl` fork.

- [x] Enable a login-manager and greeter
    - [x] `elogind`
    - [x] `sddm`
    - [ ] `seatd` to replace `elogind`
    - [ ] `greetd` + greeter to replace `sddm`
    - [x] Bundle a default theme (breeze)

- [ ] Expand `plasma-desktop` for a 'comprehensive' extension
    - [x] `breeze`
    - [x] `breeze-icons`
    - [x] `systemsettings`
    - [x] `kinfocenter`
    - [x] `bluez`
    - [x] `networkmanager`
    - [ ] `pulseaudio`
    - [x] `udisks2`
    - [ ] `vaultcrypt`

- [ ] Package some KDE apps
    - [x] `ark`
    - [x] `calligra`
    - [x] `dolphin`
    - [x] `dragon`
    - [ ] `elisa` - immediately crashes
    - [x] `falkon`
    - [ ] `filelight` - segfault on launch
    - [x] `gwenview`
    - [x] `kaffeine`
    - [x] `kate`
    - [x] `kdenlive`
    - [x] `kdevelop`
    - [x] `kget`
    - [ ] `kgpg`
    - [ ] `kmail`
    - [ ] `konqueror`
    - [x] `konversation`
    - [x] `konsole`
    - [x] `krita`
    - [x] `ktorrent`
    - [ ] `kwalletmanager`
    - [ ] `kwave`
    - [x] `latte-dock`
    - [ ] `okular`
    - [ ] `spectacle`
    - [ ] `yakuake`

- [ ] Test `wayland` support
    - [x] `plasma 5.20.0`
    - [x] fork `xorg-server` to add `xwayland` support
    - [ ] sucessfully launch a `kwin_wayland` session

- [ ] Ensure everything works!
    - [x] Sessions can start from `startx`
    - [x] Sessions can start from `sddm`
    - [x] Applications launch
    - [x] Menus work
    - [x] Themes work
    - [x] Settings work
    - [~] Users can log back in from a *lock* (super+l) - works without
      `linux-pam`
    - [ ] Bluetooth - can't test
    - [ ] Power management -untested
    - [ ] Disk/hardware management -untested

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

### Documentation

I have opted not to include internationalization or docs. This was done in three
parts:

1) Hobble `ki18n` by removing the `gettext` dependency
    * Without learning C, I will not be able to drop the `ki18n` dependency.

2) Delete all translation files located in `po` from packages which 'require'
them. 

3) Remove the `kdoctools` dependency from packages which 'require' it.

If you would like to have languages besides english available: 
    * Package `gettext` 

    * Remove the patch for `ki18n`

    * Remove `rm -rf po` from each build script that has it

Additionally, there are packages which I had to go further to remove `gettext`
and `intltool` from. It shouldn't be too hard to find them on your own (the
patches are sanely named, `remove-gettext.patch` and `remove-intltool.patch`).
Fix up the build files as required!

if you would like to have documentation:

    * Package `docbook-xml`, `docbook-xsl`, and `kdoctools`

    * Remove the `seds` removing DocTools (and the `seds` that acommpany them.

Specifically, revert:

    * c1b33ca4d668cb76edad6df6cb2e1617a1b33c07
    * b0d7c17e2a1e38d3f532c9d7c901a0fc45f44391
    * c1b33ca4d668cb76edad6df6cb2e1617a1b33c07


### dbus

The dreaded(?) question.

The trouble with evading `dbus` is not with building KDE. 

It's with launching it.

If you attempt to `startx startplasma-x11`, you will probably be met with an
error message about `dbus`. KDE tries *very* hard to have a running `dbus`
session to latch onto. So much so that it shits itself if one isn't running.
It'll print an error if you try to just launch plasma, it'll hang at a black
screen if you attempt to launch `kwin` via `xinit`, etc.

Which would seem to make `dbus` a hard dependency for KDE.

`plasma-desktop` et al build fine with
[libdbus-stub](https://github.com/dylanaraps/libdbus-stub). The hard part would
probably be getting `dbus-launch` to execute meaningfully with a stub library.
Unless we can find a way around this, `dbus` is required. I have no real
problems with this. If you take umbridge with this hard fact, perhaps reflect on
why you want KDE in the first place?

### polkit, PAM, logind

You'll notice in many distros that there are many versions of, for instance,
polkit swimming around. I don't want to maintain a bunch of polkit versions for
different login managers and greeters. In fact, I want as minimal maintenance as
possible. But I also want to maximize choice. There's no *good* reason to assume
a user wants `linux-pam` over `shadow`. There's no good reason to assume
anything about what the user wants! But this minimalism should decrease friction
as much as possible. I don't want to punish users for wanting to install these
programs. I definitely don't want to force them to do the reading I did either!

For the user who is disinterested or unconcerned with `linux-pam`, `polkit`,
`elogind`, etc., they are required to do nothing. Not only is this in line with
KISS' goals as a project, but it also doesn't require users to do large amounts
of research to do what should be routine maintenance or security management. If
a user wants to install `polkit` or, maximally, `elogind`, only a few packages
must be built prior to building `plasma-desktop`.


### qt

The engine that make it go!

The qt dependency includes packages which overlap with community, like `qt5`.
Additionally, `qt5` requires packages from community at build time. If a package
has been changed, it will be forked into this repository. As a result, you
should place this repository in your `$KISS_PATH` __before__ the official and
the community repositories. 

`qt5*`, specifically `qt5-webengine`, take a very long time to build (thanks,
chromium). As a result, I have included along with this repository a KISS
tarball of the fully built packages for the three parts of the `qt5` suite that
take more than twenty minutes to build. These are compiled assuming you've
satisfied the dependencies of *this* repository. Built with everyone's favorite
GNU C compiler, with `CFLAGS=-march=x86-64 -mtune=generic -Os -pipe`. If you
want a 'seamless' build, merely plop these archives in
`${XDG_CACHE_DIR:-$HOME/.cache}/kiss/bin`.


### xorg vs wayland 

The eternal debate.

Starting with the release of `plasma 5.20.0`, `wayland` will be the presumptive
default for KDE. I'm unsure to what extent they plan to leave `xorg` available,
As it stands, few parts *require* `xorg`. Indeed, you can build almost every
package in this repository without any `xorg` libraries at all! Unfortunately,
EGL is required. EGL is provided by `mesa`, and will only be built if the x11
platform is built. This brings in the largest number of dependencies.  Next to
this, `plasma-desktop` requires `xorg-server`, and there are a few `xorg`
libraries required by a couple of packages. 

Wayland is an exciting project, and having been using it on my own personal KISS
box, I can recommend it heartily over `xorg` in general - it seems to have
matured as a protocol, there is still incredibly active development work (as
opposed to critical-bug-fixing-only for `xorg`), and the performance is quite
stellar.  Additionally, `kwinft` is a fork of `kwin` which purports to better
support wayland. It is a more bleading-edge, development focused 'branch' of
`kwin`.  Bound to be bugs that crop up. Presumably, `kwinft` is strictly better
than `kwin`. 

Currently, it's your choice which you choose! If you opt to use `kwinft`, simply
fork `plasma-desktop` and `plasma-workspace`, and comment `kwin` from their
depends file and uncomment `kwinft`. then simply build `plasma-desktop`.  If you
have already installed `kwin` fear not! Do the comment switcheroo as before,
force-uninstall `kwin`, reinstall `plasma-{desktop,workspace}` (which should
pull-in everything required for `kwinft`), and then simply kill your KDE session
if you're in one, and restart the session! If you run into bugs, please make
sure they're not `kwinft` related - burn down the [developer's
door](https://gitlab.com/kwinft/kwinft) for those. 


## Prerequisites

1. This all (currently) assumes a *working* `xorg-server`.

2. You will need `dbus`. Because of this, you'll have to rebuild `qt5`.

3. You will need `eudev` or `libudev-zero`. Because of this, you'll need to
rebuild `xorg-server`, `{xf86-input-}libinput`, `dhcpcd`, maybe more.

These rebuilds are obviously not required if you already had the relevant
programs built against `dbus`, `eudev`, etc. To determine which packages are
built against `dbus` or `eudev` merely run `kiss-revdepends {dbus,eudev}`.

If you opt to use `elogind` (required for `sddm`), you will need the following:

4. Choose between `shadow` or `linux-pam`, and install your choice.

5. Install `polkit`.

6. [cgroups](http://www.linuxfromscratch.org/blfs/view/svn/general/elogind.html)
    I leave it up to you to test your own kernel configs.

7. You will require exactly `realpath` from `coreutils`.

`coreutils` is a build time requirement, so you are free to remove it with no
ill-effects afterwards.


## Getting Started

Now that we have all of that nonsense out of the way, let's get to it!

We start with the assumption that you just installed KISS, following the
[installation guide](https://k1ss.org/install) exactly, which means you have
`libudev-zero` or `eudev`, a working `xorg` that knows about `eudev`, some input
drivers, and fonts. Although this repo does include some nice fonts, so either
way you're covered. 

Ensure you do not have `qt5*` installed. 

__NOTE__: I have taken the liberty of uploading a KISS package for `qt5`,
`qt5-webengine`, and `qt5-declarative`. Assuming your system is roughly similar
to mine (built from a blank KISS-chroot), you can simply install these xz
archives instead of building them. `qt5-webengine` is NOT required, just
convenient.

`plasma-desktop` includes the basic requirements to launch a KDE session and use
plasma. In order to minimize the system footprint as much as possible, things
like fonts or starting applications are not included. You may wish to install
`hack` or `noto-fonts`, along with `systemsettings` and `kinfocenter`.
Additionally, `breeze-icons` will give you a default set of icons.

If you want a more 'full-featured' KDE environment, you can install  useful
extras. This list includes things such as `bluedevil` and `bluez`. `udisks2` is
also here! Additionally, `plasma-workspace-wallpapers` includes many additional
wallpapers for your pleasure.

__NOTE__: `bluez` and other things are more are less untested; ymmv. If you
discover issues, please raise them! If you discover solutions, please PR them!

To get a 'minimal' KDE (final product on a fresh install will be just north of
150 packages), simply install `plasma-desktop`. How convenient! Here's how you
do it:

__NOTE__: It doesn't matter where you keep these repos (or any KISS repos, for
that matter). I keep mine in `$HOME/git`. For now, assume I've done `git clone`
in `$HOME`.

``` 
$ git clone https://github.com/kisslinux/community   # Clone
$ git clone https://github.com/dilyn-corner/KISS-kde # pls

# Add relevant repository paths

$ export KISS_PATH="$HOME/KISS-kde/frameworks" 
$ export KISS_PATH="$KISS_PATH:$HOME/KISS-kde/plasma" 
$ export KISS_PATH="$KISS_PATH:$HOME/KISS-kde/extra" 
$ export KISS_PATH="$KISS_PATH:$HOME/community/community"

# If you don't already have it,

# replace `libudev-zero` with `eudev` if you prefer

$ kiss b dbus libudev-zero && kiss i dbus libudev-zero

# If you just did the previous or don't have xorg,

$ kiss b xorg-server libinput xf86-input-libinput 
# Add others as necessary

# If you've opted to use e.g. elogind, you'll need 
# to do the following!

# Choose shadow or linux-pam to use with polkit 
$ kiss b shadow && kiss i shadow
$ kiss b polkit && kiss b polkit

# Thanks to the alternatives system, we can just pluck out 
# the few binaries we need from `coreutils`.

$ kiss b coreutils && kiss i coreutils 
# For elogind 
$ kiss a coreutils
/usr/bin/realpath 
# For libblockdev & udisks 
$ kiss a coreutils /usr/bin/mktemp
$ kiss a coreutils /usr/bin/ln

$ kiss b elogind && kiss i elogind

# We're finally ready!

$ kiss b plasma-desktop

~~~ SEVERAL HOURS LATER ~~~

$ kiss i plasma-desktop

# Start the eudev and dbus services for convenience 
# This is ultimately not necessary 
$ ln -sv /etc/sv/dbus  /var/service 
$ ln -sv /etc/sv/udevd /var/service

$ sv up dbus 
$ sv up udevd

# Install a font of your choosing.  
# noto-fonts is in KISS-kde/extra. It's massive.  
# hack is in community. It's very small and good.  
# Any TTF font should work though.

# You might want to make a runtime directory or KDE will 
# make one for you. This is required for a wayland session.  
# Put these lines in somewhere like $HOME/.profile

$ export XDG_RUNTIME_DIR="${XDG_RUNTIME_DIR:-/tmp/$(id -u)-runtime}" 
$ [ -d "$XDG_RUNTIME_DIR" ] || { 
    mkdir -p   "$XDG_RUNTIME_DIR" 
    chmod 0700 "$XDG_RUNTIME_DIR" }

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
It's basically just a KISS tarball. It is built with musl and GCC using:

`C(XX)FLAGS=-march=x86-64 -mtune=generic -pipe -Os`

The first release is built from a fresh KISS tarball. All future releases will
merely be published after performing updates on this original tarball.
Installing works almost identically to the [usual
instructions](https://k1ss.org/install).  To install from the first October 2020
release,

``` 
$ ver=2020.10-1 
$ wget https://github.com/dilyn-corner/KISS-kde/releases/download/$ver.tar.xz 
# Mount your relevant root partition to wherever. In this case, I choose /mnt 
$ tar xf kiss-kde-$ver.tar.xz -C /mnt 
$ ./mnt/bin/kiss-chroot /mnt

# Follow the KISS install guide.

$ exit 
$ reboot

# Boot into your freshly installed KISS # Login (user: root; pass: toor) 
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

![Think
Bigger](https://raw.githubusercontent.com/dilyn-corner/KISS-kde/master/06-23%4021%3A48%3A21.jpg)
