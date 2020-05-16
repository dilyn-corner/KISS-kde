# KISS-kde

This is currently a work in progress. Feel free to help out!

I have no interest in using KDE; I just want to see if it can be done on KISS. I plan on maintaining this __indefinitely__, but low use might lead to my missing multiple bugs. Please feel free to submit a pull request or make an issue to fix anything you find, or to include something I have excluded.


There are many, __many__ packages for a Plasma Desktop (because I guess fragmentation is cool?). Ostensibly, anybody who wants a KDE environment wants a KDE environment, so I don't plan on nixing features or removing any packages. That being said, I don't intend to accomodate *every* KDE application. To my mind, there are few reasons to use them. As a result, I will drop a few framework items which aren't required by other framework items and are solely there to make KDE applications work. I've also opted to drop internationalization support. We can add this again later, if we want - I assume nothing about KISS' userbase, but the lack of support for nonenglish languages by the BDFL makes me inclined to follow suit.

The qt dependency includes packages which overlap with community, like `qt5`. Additionally, `qt5` requires packages from community at build time. If a package in community has been changed, it will be forked into this repository. As a result, you should place this repository in your `$KISS_PATH` __before__ community.

Little did I know `kxmlgui` would require exactly one tiny library to build. This one tiny library requires an entire Qt rebuild; with OpenSSL support (because upstream "will not support LibreSSL). Luckily, the BSDs have our back. They've been maintaining patches for `qt5` to add `libressl` support for quite some time. So we're adding it in here. It might make its way up to Community as well, if the need arises. It's building as I write this, hopefully all goes well. 

__DBUS__: The dreaded(?) question. `kdbusaddons` is required by `kglobalaccel`. This in turn is required solely by `kxmlgui`. This, unforunately, is required for `kbookmarks` (every other requirement is a KDE app), which is, for some reason, required by `kio`. `kio` is basically a fundamental program from my vantage point. It's *possible* that this dependency chain could be broken, but I only say that it's possible because I have not seen anything saying otherwise. It's probably a hard dependency we can't wriggle out of. I'll look into it for this project, but I make no promises. 

As it stands, KDE requires `dbus`. I have no real problems with this. If you're going to use KDE and you take umbridge with this, perhaps you should reevaluate why you need KDE?

**Again, this is _not_ ready for use. By any stretch of the imagination.**

When it's finally ready and been tested (that means I launched it once and it didn't immediately crash), a metapackage will be made that merely installs all the required packages. 


#### Prerequisites

1. This all assumes a *working* `xorg-server`. Many xorg-related dependencies are left out. The minimum should be enough.

2. You will need `dbus`. Because of this, you'll have to rebuild `qt5*`.

3. You will need `eudev`. Because of this, you'll need to rebuild `xorg-server`, any input packages (`libinput`, `xf86-input-libinput`, etc), `dhcpcd`, perhaps others.

These rebuilds are obviously not required if you already had the relevant programs built against `dbus`, `eudev`, etc.

---

# Roadmap

## What is left

1. plasma-framework

the framework requires:

`kauth` < `polkit-qt-1`

`kconfigwidgets` < `ktextwidgets`

`ki18n` < `kservice` & `kiconthemes` & `kio` & `kdeclarative` & `kpackage`

`kdoctools`

`kirigami`

`kxmlgui`

So This is basically our "bare-minimum". 

2. kauth

A lot of the core bits install with very few problems. However, `kauth` seems pretty integral to a working KDE environment, as this whole stack directly requires it:

`kconfigwidgets` < `ktextwidgets` < `kcmutils`

`frameworkintegration`

`kde4libssupport`

3. ki18n

This internationalization framework has its hands in *everything*. 

`kservice` < `kemoticons` < `kinit` < `kded`

`kiconthemes`

`kwallet`

`kpty`

`kdesu`

`kio`

`knewstuff`

`kparts`

`kpackage`

`kdeclarative`

`knotifyconfig`

`kunitconversion`

`kjsembed`

`kross`

`ktexteditor`

`khtml`

The only applications here on this list I think are worth keeping are `kservice`, `kiconthemes`, `kwallet`, `kpty`, `kdesu`, `kio`. The rest are just sort of... extra? And (so far) don't seem to be required by anything (at least nothing which I have successfully built. It could end up being that these emoticons are super important). 
Taking a look at how we get around a `ki18n` dependency in `falkon`, it seems simple enough; delete the `po` folder. Unforunately, this doesn't really do the trick. We'd have to mangle up at least a couple files in (each?) package we want to keep. I'm not sure whether `ki18n` is inextricable or not. But if it's possible, getting it out of any of the aforementioned apps is basically mandatory. 
Otherwise, we install `gettext`. Which is fine by me. 

4. Others

`kdoctools` requires a perl module that is easy to package. `kdoctools` is probably required by many other things I haven't had the option of building yet, so we might need this. Easy fix. 

`kdewebkit` requires `qt-webkit`. This wouldn't be so terrible if `qt-webkit` didn't require `ruby`. I'm not sure how important this Qt webkit implenentation is, but it certainly doesn't bring joy to me.

---

These are the requirements for the KDE framework as given by the ['from source' guide](https://community.kde.org/Guidelines_and_HOWTOs/Build_from_source/Details).  

## 1. Dependencies for (?)everything.
- [x] cmake
- [x] extra-cmake-modules
- [x] qt5

## 2. Qt-5 based dependencies
- [x] phonon
- [x] attica
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
- [x] kauth - requires (optionally) polkitqt-1 <- We're going to need this.
- [x] kjobwidgets
- [x] kcompletion
- [x] kdnssd
- [x] kconfigwidgets
- [x] kservice -> does not build with (standalone) llvm?
- [x] kiconthemes
- [x] knotifications
- [x] kwallet -> requires libgcrypt
- [x] kpty
- [x] kemoticons
- [x] kdesu
- [x] ktextwidgets
- [ ] kxmlgui
- [ ] kbookmarks
- [ ] kio
- [x] kdesignerplugin
- [ ] knewstuff
- [ ] kparts
- [ ] kpackage -> Will need to remove `ki18n`
- [ ] kdeclarative
- [ ] kcmutils
- [ ] kinit
- [ ] kded
- [ ] knotifyconfig
- [ ] kross
- [ ] kmediaplayer
- [ ] kdewebkit
- [ ] ktexteditor
- [x] kapidox
- [ ] frameworkintegration

## 4. Plasma and friends
- [x] kactivities
- [ ] plasma-framework
- [ ] kde-workspace


## 5. Exclusions

kjs - It's required by two framework items (following) and two applications. The applications are simple things which do not need to be applications (a batch file renamer and a document viewer).

khtml - Requires `kjs`. The only real appeal for this seems to be `calligra`, but there are (potentially?) alternatives. We can look into adding this later, when we consider packaging KDE applications.

kdoctools - It generates documentation. Who needs documentation?

ki18n - We patch this out of `falkon`. It's an internationalization framework. KISS does not support nonenglish languages. That's reason enough to drop it. It's mainly required by a variety of KDE applications. I already hate it.

kunitconversion - It converts units. No. (It's also required by a single KDE application related to the periodic table. No.)

kjsembed - 'Embedded JS' I'm sorry? It provides bindings from JS objects to Qt objects. So... Unnecessary.

kdelibs4support - Are we in the business of supporting legacy stuff? No. Devs, stop using old libraries.
