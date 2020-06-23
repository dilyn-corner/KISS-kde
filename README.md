# KISS-kde

This is currently a work in progress. Feel free to help out!

Every package required to build `plasma-desktop` builds and
install just fine! But does it work, that's the question...

The answer is no, by the way. I mean, it'll certainly
*launch*. But if you're looking for something that's more
than just a pretty background, you're a bit SOL atm...

It seems to launch well enough. You can add panels, menus
popup, etc. But you can't launch `krunner`, or
`kactivities`. You can't do much of anything really. 

But hey, it builds! And installs! And launches! That's a
success, kids. 


__NOTE__: If you are interested in using this 'environment' without any of your
usual features, there is a way! No menus will open in the normal interactive
way (there might be a way to launch them via commandline; I haven't looked at
`/usr/bin` out of fear), but you should be able to launch applications by merely
doing the following to `.xinitrc`:

```
cat >> $HOME/.xinitrc << EOF
konsole &
dolphin & 
falkon & 
exec dbus-launch --exit-with-session start-plasmax11
EOF
```

This will launch your plasma session along with `konsole`, `dolphin`, and
`falkon`. Replace with your relevant applications as necessary.

If you want a more 'full-featured' KDE environment, you can install `plasma`.
This 'package' will come with 20 additional packages, and it fleshes out our
fledgling project with features one might expect to exist.
It isn't that much more, so there's no real reason not to use it.
This expanded list includes things like `bluez`. It might be worht it for you.

---

## Where we stand

The qt dependency includes packages which overlap with community, like `qt5`.
Additionally, `qt5` requires packages from community at build time. If a package
in community has been changed, it will be forked into this repository. As a
result, you should place this repository in your `$KISS_PATH` __before__
community.

__gettext__: I have opted not to include internationalization. This was done in
two parts:

1) Hobble `ki18n` by removing the gettext dependency

2) Delete all translation files from packages which 'require' them. 

If you would like to have languages besides english available, it should be
quite easy. First, package `gettext`. Second, remove the patch in the `ki18n`
package. Finally, simply remove `rm -rf po` from each build script in which it
appears. This is probably trivial (cough `sed` cough). 

__dbus__: The dreaded(?) question.

Based on my current knowledge of KDE, `dbus` is required by
a litany of programs. Many which don't explicitly require
`dbus` will nontheless build against it if it is available.
The hard part, however, is not the framework. Instead, it is
for launching KDE itself. 

If you attempt to `startx startplasma-x11`, you will
probably be met with an error message about `dbus`. You see,
KDE requires something like `polkit`, `elogind`, etc. to
start. Indeed even with `wayland` this is the case.

Which would seem to make `dbus` a hard dependency for KDE.

Unless we can find a way around this (and I am certainly not
the one to do it!), `dbus` is required. I have no real
problems with this. If you take umbridge with this hard
fact, perhaps reflect on why you want KDE in the first
place?

#### Prerequisites

1. This all assumes a *working* `xorg-server`. Many
   xorg-related dependencies are left out as explicit deps.
   The minimum xorg requires should be enough - all others
   should be handled by `kiss`. Yes, I know `wayland` is a
   dependency. It is also an option to use `wayland` as a
   result, if you so choose. `xorg` is merely the 'default'.

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
the relevant programs built against `dbus`, `eudev`, etc. 


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
$ exprot KISS_PATH="$KISS_PATH":$HOME/community/community"

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
# Unclear if that's all necessary! We'll see.

# Enjoy! 

$ pkill x
$ echo "exec dbus-launch --exit-with-session startplasma-x11" >> ~/.xinitrc" 
# Ensure you don't have anything else execing before this

$ startx
```

---

# Things to be aware of

1. Currently, no menus operate the way you might like them to. Right-clicking on
   the desktop or the panel open menus, certainly. But they don't actually do
   anything. Windows like `krunner` for instance are just empty. I'm not sure if
   they aren't rendering properly or if we're missing some sort of dependency,
   or maybe `gettext` is actually NOT an optional requirement in order to get
   these things working, but that is currently the #1 bug to solve. 

2. I have not opted to start `sddm` by default. Turning this repository into a
   full-fledged desktop is second on my list of things to do, right after I get
   menus working. I also plan on packaging launcher-alternatives, like
   `lightdm`. User choice, and all. 

3. This is very much in alpha. I will keep this repository up-to-date as best I
   can, testing and building things as frequently as possible - it's a big
   project that requires a fair amount of maintenance. Luckily, I have the hard
   drive space and the free time. But I'm only one person; if you have a
   contribution, feel free to share it. If you package some KDE apps, feel free
   to submit a pull request for inclusion. Requests like this should follow the
   KISS [style guide[(https://k1ss.org/wiki/kiss/style-guide) as closely as 
   possible, but I'm not too much of a stickler.


# My goals

1. Fix menus

2. Package convenient default applications

3. Ensure `bluez` etc. work properly 

4. Look into distributing this as an alternative to the 'default' KISS tar. 

4a. This may include a live USB? Who knows.


## Enjoy!

![alt
text](https://github.com/dilyn-corner/KISS-kde/blob/master/06-23%4001:45:16.jpg)
