# KISS-kde

This is currently a work in progress. Feel free to help out!

I have no interest in using KDE; I just want to see if it can be done on KISS. I plan on maintaining this __indefinitely__, but low use might lead to my missing multiple bugs. Please feel free to submit a pull request or make an issue to fix anything you find, or to include something I have excluded.


There are many, __many__ packages for a Plasma Desktop (because I guess fragmentation is cool?). Ostensibly, anybody who wants a KDE environment wants a KDE environment, so I don't plan on nixing features or removing any packages. That being said, I don't intend to accomodate *every* KDE application. To my mind, there are few reasons to use them. As a result, I will drop a few framework items which aren't required by other framework items and are solely there to make KDE applications work. They can always be readded assuming I ever decide to package KDE applications. I've also opted to drop internationalization support. We can add this again later, if we want - I assume nothing about KISS' userbase, but the lack of support for nonenglish languages by the BDFL makes me inclined to follow suit.

The qt dependency includes packages which overlap with community, like `qt5`. Additionally, `qt5` requires packages from community at build time. If a package in community has been changed, it will be forked into this repository. As a result, you should place this repository in your `$KISS_PATH` __before__ community.

Little did I know `kxmlgui` would require exactly one tiny library to build. This one tiny library requires an entire Qt rebuild; with OpenSSL support (because upstream "will not support LibreSSL). Luckily, the BSDs have our back. They've been maintaining patches for `qt5` to add `libressl` support for quite some time. So we're adding it in here. It might make its way up to Community as well, if the need arises. It's building as I write this, hopefully all goes well. (narrator: it did). 

__DBUS__: The dreaded(?) question.

`kdbusaddons` < `kglobalaccel` < `kxmlgui` < `kbookmarks` < `kio`

`kio` is basically a fundamental program from my vantage point. 

Also note that many other programs will link against dbus if it is present. Presumably, more than just kio relies on dbus. We can check the CMakeFiles.txt for each package to see which ones ask for it (that is to say, we can `grep` the logs kiss generates to see which ones mention dbus. I'm not a lunatic; there's no way I'm reading hundreds of lines from over a hundred packages).

It's probably a hard dependency we can't wriggle out of. I'll look into it for this project, but I make no promises. 

As it stands, KDE requires `dbus`. I have no real problems with this. If you're going to use KDE and you take umbridge with this, perhaps you should reevaluate why you need KDE?

**Again, this is _not_ ready for use. By any stretch of the imagination.**

When it's finally ready and been tested (that means I launched it once and it didn't immediately crash), a metapackage will be made that merely installs all the required packages. 


#### Prerequisites

1. This all assumes a *working* `xorg-server`. Many xorg-related dependencies are left out. The minimum should be enough. "But Dilyn", you may exclaim. "If this requires Xorg, why oh why must we install that darned `wayland`?" Because, my sweet summer child:

KDE devs are dicks.

Basically they build all their stuff off Xorg for years and years. And then they saw `wayland` and thought: oh, yes. New. Please.

So now we have a half-implemented wayland support and the entire business *still* heavily relies on X libraries to function. You're welcome.

2. You will need `dbus`. Because of this, you'll have to rebuild `qt5*`.

3. You will need `eudev`. Because of this, you'll need to rebuild `xorg-server`, any input packages (`libinput`, `xf86-input-libinput`, etc), `dhcpcd`, perhaps others.

These rebuilds are obviously not required if you already had the relevant programs built against `dbus`, `eudev`, etc.

---

# Roadmap

This section will eventually go away. 

## 1. Dependencies for (?)everything.
- [x] cmake
- [x] extra-cmake-modules
- [x] qt5\*

## 2. Qt-5 based dependencies
- [x] phonon
- [x] attica
- [x] kirigami2
- [ ] strigi
- [ ] libdbusmenu-qt
- [ ] polkit-qt-1

## 3. Framework
- [x] kitemmodels 
- [x] kitemviews
- [x] kplotting
- [x] threadweaver
- [x] kcodecs
- [x] kguiaddons
- [x] kidletime
- [x] kwidgetsaddons
- [x] sonnet
- [x] kconfig
- [x] kwindowsystem
- [x] solid
- [x] kcoreaddons
- [x] kcrash
- [x] kdbusaddons
- [x] kglobalaccel
- [x] karchive
- [x] kimageformats
- [x] kauth - requires (optionally) polkitqt-1 <- We're going to need this. Probably.
- [x] kjobwidgets
- [x] kcompletion
- [x] ki18n -> hobbled to remove `gettext`
- [x] kdoctools
- [x] kdnssd
- [x] kconfigwidgets
- [x] kservice -> does not build with (standalone) llvm?
- [x] kiconthemes
- [x] knotifications
- [x] kwallet
- [x] kpty
- [x] kemoticons
- [x] kdesu
- [x] ktextwidgets
- [x] kxmlgui
- [x] kbookmarks
- [x] kio
- [x] kdesignerplugin
- [x] knewstuff
- [x] kparts
- [x] kpackage
- [x] kdeclarative
- [x] kcmutils
- [x] kinit
- [x] kded
- [x] knotifyconfig
- [x] kunitconversion
- [x] kross
- [x] kmediaplayer
- [ ] kdewebkit -> requires qt5-webkit && ruby
- [x] ktexteditor
- [x] kapidox
- [x] frameworkintegration
- [x] kdelibs4support

## 4. Plasma and friends
- [x] kactivities
- [x] plasma-framework
- [x] krunner
- [x] kpeople
- [x] kactivities-stats
- [x] libksysguard
- [x] ksysguard
- [x] kfilemetadata
- [x] baloo
- [x] kdecoration
- [x] kwayland -> Requires wayland{-protocols} (make), qt5-wayland

    For those keeping score, we now require three other KISS repositories.

- [x] breeze{-icons}
- [x] kscreenlocker
- [x] kwin
- [ ] qqc2-desktop-style
- [x] plasma-integration
- [x] libkscreen
- [x] plasma-workspace
- [x] khotkeys
- [ ] kinfocenter
- [x] kmenuedit
- [x] oxygen
- [x] systemsettings
- [x] plasma-desktop



Please don't look at the dependencies files for these packages. Most of them are inaccurate.

The `alpine-version` branch has a more accurate representation of what the actual dependencies are. 
