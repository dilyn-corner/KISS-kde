# KISS-kde

This is currently a work in progress. Feel free to help out!

I have no interest in using KDE; I just want to see if it can be done on KISS. I plan on maintaining this __indefinitely__, but low use might lead to my missing multiple bugs. Please feel free to submit a pull request or make an issue to fix anything you find, or to include something I have excluded.


There are many, __many__ packages for a Plasma Desktop (because I guess fragmentation is cool?). Ostensibly, anybody who wants a KDE environment wants a KDE environment, so I don't plan on nixing features or removing any packages. That being said, I don't intend to accomodate *every* KDE application. To my mind, there are few reasons to use them. As a result, I will drop a few framework items which aren't required by other framework items and are solely there to make KDE applications work. I've also opted to drop internationalization support. We can add this again later, if we want - I assume nothing about KISS' userbase, but the lack of support for nonenglish languages by the BDFL makes me inclined to follow suit.

The qt dependency includes packages which overlap with community, like `qt5`. Additionally, `qt5` requires packages from community at build time. If a package in community has been changed, it will be forked into this repository. As a result, you should place this repository in your `$KISS_PATH` __before__ community.

__DBUS__: The dreaded(?) question. `kdbusaddons` is required by `kglobalaccel`. This in turn is required solely by `kxmlgui`. This, unforunately, is required for `kbookmarks` (every other requirement is a KDE app), which is, for some reason, required by `kio`. `kio` is basically a fundamnetal program from my vantage point. It's *possible* that this dependency chain could be broken, but I only say that it's possible because I have not seen anything saying otherwise. It's probably a hard dependency we can't wriggle out of. I'll look into it for this project, but I make no promises. 

As it stands, KDE requires `dbus`. I have no real problems with this. If you're going to use KDE and you take umbridge with this, perhaps you should reevaluate why you need KDE?

**Again, this is _not_ ready for use. By any stretch of the imagination.**

When it's finally ready and been tested (that means I launched it once and it didn't immediately crash), a metapackage will be made that merely installs all the required packages. 

---

# Roadmap

These are the requirements for the KDE framework as given by the ['from source' guide](https://community.kde.org/Guidelines_and_HOWTOs/Build_from_source/Details).  

##1. Dependencies for (?)everything.
- [x] cmake
- [x] extra-cmake-modules
- [x] qt5

##2. Qt-5 based dependencies
- [x] phonon
- [ ] attica
- [ ] strigi
- [ ] libdbusmenu-qt
- [ ] polkit-qt-1

##3. Framework
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
- [x] kauth - requires (optionally) polkitqt-1
- [x] kjobwidgets
- [ ] kcompletion
- [ ] kdnssd
- [ ] kconfigwidgets -> Will need to remove `ki18n`
- [ ] kservice -> Will need to remove `ki18n`
- [ ] kiconthemes
- [ ] knotifications
- [ ] kwallet
- [ ] kpty -> Will need to remove `ki18n`
- [ ] kemoticons
- [ ] kdesu
- [ ] ktextwidgets
- [ ] kxmlgui
- [ ] kbookmarks
- [ ] kio
- [ ] kdesignerplugin
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
- [ ] kapidox
- [ ] frameworkintegration

##4. Plasma and friends
- [ ] kactivities
- [ ] plasma-framework
- [ ] kde-workspace


##5. Exclusions

kjs - It's required by two framework items (following) and two applications. The applications are simple things which do not need to be applications (a batch file renamer and a document viewer).

khtml - Requires `kjs`. The only real appeal for this seems to be `calligra`, but there are (potentially?) alternatives. We can look into adding this later, when we consider packaging KDE applications.

kdoctools - It generates documentation. Who needs documentation?

ki18n - We patch this out of `falkon`. It's an internationalization framework. KISS does not support nonenglish languages. That's reason enough to drop it. It's mainly required by a variety of KDE applications. I already hate it.

kunitconversion - It converts units. No. (It's also required by a single KDE application related to the periodic table. No.)

kjsembed - 'Embedded JS' I'm sorry? It provides bindings from JS objects to Qt objects. So... Unnecessary.

kdelibs4support - Are we in the business of supporting legacy stuff? No. Devs, stop using old libraries.
