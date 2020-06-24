# KISS-kde


![alt
text](https://github.com/dilyn-corner/KISS-kde/blob/master/06-23%4021:48:21.jpg)


This is currently a work in progress. Feel free to help out!

Every package required to build `plasma-desktop` builds and
install just fine! But does it work, that's the question...

It builds! It installs! It launches! That's a _success_, kids. 


## Current Milestones

Here are all of the things that can be worked on. 

- [ ] Enable `linux-pam` in a meaningful way
    * This would allow things like *logging back in from the lockscreen*
    * This might fix the `udisks2` problem I have (very poorly) worked around.

- [ ] Enable a login-manager

- [ ] Ensure everythingn works!

- [ ] Make patches to remove coreutils dependency
    * Currently, we need `coreutils` for:
    - [ ] `elogind - /usr/bin/realpath --relative-to`
    - [ ] `libblockdev - /usr/bin/mktemp --tmpdir`
    - [ ] `udisks2 - /usr/bin/ln -r`

- [ ] Properly configure docbook generation
    * Currently, `docbook-xsl` doesn't actually do anything, I don't think.
    * You can see this in `udisks2`: all docs are disabled!


- [ ] Package some KDE apps
    - [ ] `krita`
    - [ ] `okular`
    - [ ] ???

- [ ] Cleanup repository structure
    * This one is on me. There seems to be a 'natural' grouping for these.

- [x] Generate build files!

- [x] Satisfy dependencies

- [x] Make sure the core builds

- [x] Does KDE launch?

- [x] Fix menus not working

- [x] Find a *sane* default collection

- [ ] Expand the default for a 'comprehensive' alternative
    - [x] `udisks2`
    - [ ] `vaultcrypt`
    - [ ] `networkmanager`
    - [ ] `bluez`
    - [ ] `pulseaudio` - will probably remain optional
    - [ ] ???


- [ ] Create an installation tarball similar to how KISS is distributed
    - [ ] Live USB (?)


This list will be expanded, contracted, and refined as necessary. Feel free to
assist. 

---

## KISS-kde with respect to KISS

__gettext__: I have opted not to include internationalization. This was done in
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


__dbus__: The dreaded(?) question.

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

__qt__: The engine that make it go!

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


#### Prerequisites

1. This all assumes a *working* `xorg-server`. Many
   xorg-related dependencies are left out as explicit deps.
   The minimum xorg requires should be enough - all others
   should be handled by `kiss`. Yes, I know `wayland` is a
   dependency. It is also an option to use `wayland` as a
   result, if you so choose. `xorg` is merely the 'default'. Note that `wayland`
   support is still not fully implemented in KDE. You might have better mileage
   using `kwinft` instead of `kwin`, for instance. This option is also included
   in this repository. FWIW, I am using `kwinft` with `xorg` and these windows
   wobble just fine.

2. [cgroups](http://www.linuxfromscratch.org/blfs/view/svn/general/elogind.html) may be required for `elogind`. I leave it up to
   you to test your own kernel configs. 

3. You will require exactly one program from `coreutils` to
   build a single package. 

4. You will need `gnugrep` to build at least one package.

5. You will need `dbus`. Because of this, you'll have to rebuild `qt5`.

6. You will need `eudev`. Because of this, you'll need to
   rebuild `xorg-server`, any input packages (`libinput`,
   `xf86-input-libinput`, etc.), `dhcpcd`, perhaps others. 

These rebuilds are obviously not required if you already had
the relevant programs built against `dbus`, `eudev`, etc. To determine which
packages are built against `dbus` or `eudev` merely run `kiss-revdepends eudev`.
If you see `xorg-server`, you're probably fine.


---


### Getting Started

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
$ export KISS_PATH="$HOME/KISS-kde/KISS-kde:$KISS_PATH"
$ export KISS_PATH="$HOME/mywaland/wayland:$KISS_PATH"
$ exprot KISS_PATH="$KISS_PATH:$HOME/community/community"

# If you don't already have it,

$ kiss b dbus eudev && kiss i dbus eudev

# If you just did the previous,

$ kiss b xorg-server libinput xf86-input-libinput 
# Add others as necessary

# Thanks to the alternatives system, we can just pluck out
# the one binary we need from `coreutils`, along with the grep utility.

$ kiss b coreutils gnugrep && kiss i coreutils gnugrep
$ kiss a coreutils /usr/bin/realpath
$ kiss a | grep ^gnugrep | kiss a -

# We're finally ready!

$ kiss b plasma-desktop

~~~ WAIT TWELVE HOURS ~~~

$ kiss i plasma-desktop

# There are some circular dependencies we should work around

$ kiss b elogind && kiss i elogind

# Perhaps we start the dbus, elogind, polkit, and eudev services?
# At least the dbus service seems necessary, so we might as well...
$ ln -sv /etc/sv/dbus  /var/service
$ ln -sv /etc/sv/udevd /var/service

# Enjoy!

$ pkill x
$ echo "exec dbus-launch --exit-with-session startplasma-x11" >> ~/.xinitrc" 
# Ensure you don't have anything else execing before this

$ startx
```

---

# Things to be aware of

1. I have not opted to start `sddm` by default. Turning this repository into a
   full-fledged desktop is first on my list of things to do. I will be looking
   into this after I get a few more packages built to flesh out KDE. I also plan on 
   packaging launcher-alternatives, like `lightdm`. User choice, and all. 

2. This is very much in alpha. I will keep this repository up-to-date as best I
   can, testing and building things as frequently as possible - it's a big
   project that requires a fair amount of maintenance. Luckily, I have the hard
   drive space and the free time. But I'm only one person; if you have a
   contribution, feel free to share it. If you package some KDE apps, feel free
   to submit a pull request for inclusion. Requests like this should follow the
   KISS [style guide](https://k1ss.org/wiki/kiss/style-guide) as closely as 
   possible, but I'm not too much of a stickler.

3. You have two window manager options: `kwin` or `kwinft`. `kwin` was recently
   forked! It's all very exciting. `kwinft` is a promising, development-heavy
   branch. As a a result, there are bound to be bugs that crop up. Presumably,
   `kwinft` is strictly better than `kwin`, because something something bleeding
   edge. It's your choice which you choose! If you opt to use `kwinft`, 
   simply comment `kwin` from `plasma-desktop/depends` and
   `plasma-workspace/depends` and uncomment `kwinft`, and then simply
   install `plasma-desktop` or `plasma`. If you have already installed `kwin`
   fear not! Do the comment switcheroo as before, reinstall *those* packages
   (which should pull-in everything required for `kwinft`), and then simply kill
   your KDE session if you're in one, uninstall `kwin`, and restart the session!
   If you run into bugs, please make sure they're not `kwinft` related - burn down
   the [developer's door](https://gitlab.com/kwinft/kwinft) for those. 

4. `kscreenlocker` works, but it seems you cannot log in! This probably has
   something to do with `linux-pam` not being setup properly. To be frank, the
   entire `elogind` stack will take a bit of learning on my part to understand and
   get working. If you happen to know how to ensure things like pam are running
   so that users don't get locked out of their systems, I would love assistance
   on this.


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

