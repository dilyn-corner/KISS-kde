# KISS-kde

I have no interest in using KDE; I just want to see if it can be done on KISS. I plan on maintaining this __indefinitely__, but low use might lead to my missing multiple bugs. Please feel free to submit a pull request or make an issue to fix anything you find, or to include something I have excluded.

This is currently a work in progress. Feel free to help out!

---

## Where we stand

As of right [now](https://github.com/dilyn-corner/KISS-kde/commit/8847aa1f15dbbe72eb7465730323b2c91d8bb768), we have successfully built the entire framework and the required plasma bits, bobs, and dodads to have what one might call and recognize as a KDE desktop. 

Currently, no KDE applications have been packaged. This is always a possibility to be added. I plan on adding a few, like `falkon` (becuase it is known working). Maybe `calligra`, so KISS can have a (sane?) `libre office`. `krita` also looks nice. 

There is no internationalization support built-in. If you would like to have it, it's quite easy to add. This will be explained in the 'geting started' section. 

The qt dependency includes packages which overlap with community, like `qt5`. Additionally, `qt5` requires packages from community at build time. If a package in community has been changed, it will be forked into this repository. As a result, you should place this repository in your `$KISS_PATH` __before__ community.

Little did I know `kxmlgui` would require exactly one tiny library to build. This one tiny library requires an entire Qt rebuild; with OpenSSL support (because upstream "will not support LibreSSL). Luckily, the BSDs have our back. They've been maintaining patches for `qt5` to add `libressl` support for quite some time. So we're adding it in here. It might make its way up to Community as well, if the need arises. It's building as I write this, hopefully all goes well. (narrator: it did). 

__gettext__: I have opted not to include internationalization. This was done in two parts:

1) Hobble `ki18n` by removing the gettext dependency

2) Delete all translation files from packages which 'require' them. 

If you would like to have languages besides english available, it should be quite easy. First, package `gettext`. Second, remove the patch in the `ki18n` package. Finally, simple remove `rm -rf po` from each build script in which it appears. This is probably trivial (cough sed cough). 

__dbus__: The dreaded(?) question.

`kdbusaddons` < `kglobalaccel` < `kxmlgui` < `kbookmarks` < `kio`

`kio` is basically a fundamental program from my vantage point. 

Also note that many other programs will link against dbus if it is present. Presumably, more than just kio relies on dbus. We can check the CMakeFiles.txt for each package to see which ones ask for it (that is to say, we can `grep` the logs kiss generates to see which ones mention dbus. I'm not a lunatic; there's no way I'm reading hundreds of lines from over a hundred packages).

It's probably a hard dependency we can't wriggle out of. I'll look into it for this project, but I make no promises. 

As it stands, KDE requires `dbus`. I have no real problems with this. If you're going to use KDE and you take umbridge with this, perhaps you should reevaluate why you need KDE?

#### Prerequisites

1. This all assumes a *working* `xorg-server`. Many xorg-related dependencies are left out. The minimum should be enough. Yes, I know `wayland` is a dependency. It is also an option to use `wayland`, if you so choose. `xorg` is merely the 'default'.

2. cgroups may be required for `elogind`. I leave it up to you to test your own kernel configs.

3. You will require exactly one program from `coreutils` to build a single package. 

4. You will need `dbus`. Because of this, you'll have to rebuild `qt5*`.

5. You will need `eudev`. Because of this, you'll need to rebuild `xorg-server`, any input packages (`libinput`, `xf86-input-libinput`, etc), `dhcpcd`, perhaps others.

These rebuilds are obviously not required if you already had the relevant programs built against `dbus`, `eudev`, etc.

---

### Getting Started

Now that we have all of that nonsense out of the way, let's get to it!

I will assume you have just installed KISS and have a working Xorg. Maybe you played around with `sowm` or `xfce`. Either way, ensure you don't have any `qt5*` packages installed.

To get a 'minimal' KDE, simply install `plasma-desktop`. How convenient. Unless your system is perfect, you will have to follow the following. It doesn't matter where you keep any of these repos. Assume I kept them in `$HOME`. 

1. Clone the relevant repos, build a sane `$KISS_PATH`.
`git clone https://github.com/kisslinux/community` 

`git clone https://github.com/periish/kiss-dbus`

`git clone https://github.com/sdsddsd1/mywayland`

`git clone https://github.com/dilyn-corner/KISS-kde`

`. /etc/profile.d/kiss_path.sh`

`export KISS_PATH="$HOME/KISS-kde/KISS-kde:$HOME/kiss-dbus/kiss-dbus:$HOME/mywaland/wayland:$KISS_PATH:$HOME/community/community"`

2. Install relevant prerequisites.

`kiss b dbus eudev`

`kiss i dbus eudev`

`kiss b xorg-server libinput xf86-input-libinput` # Add others as necessary

Thanks to the alternatives system, we can just pluck out the one binary we need from `coreutils`.

`kiss b coreutils`

`kiss i coreutils`

`kiss a coreutils /usr/bin/realpath`

3. `elogind` requires `getent`. core's `musl` does not (yet) include this, so we build our own. 

`kiss b musl`

`kiss i musl`

4. That should be basically everything.
`kiss b plasma-desktop`

Wait ten hours.

`kiss i plasma-desktop`

5. Enjoy!
`pkill x`

`echo "exec dbus-launch --exit-with-session statplasma-x11" >> ~/.xinitrc"` # Ensure you don't have anything else execing before this

`startx`
