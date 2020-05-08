# KISS-kde

This is currently a work in progress. Feel free to help out!

I have no interest in using KDE; I just want to see if it can be done on KISS. I plan on maintaining this __indefinitely__, but low use might lead to my missing multiple bugs. Please feel free to submit a pull request or make an issue to fix anything you find. 


There are many, __many__ packages for a Plasma Desktop (because I guess fragmentation is cool?). Ostensibly, anybody who wants a KDE environment wants a KDE environment, so I don't plan on nixing features or removing any packages. As a result, when this has been tested as being 'buildable' and 'runable', I will then create a meta-package that simply requires all of these as dependencies. THAT is the package which should be installed. To reiterate: you should install (pkg name forthcoming), NOT these packages individually. 



**Again, this is _not_ ready for use. By any stretch of the imagination.**



# Roadmap

These are the requirements for KDE in the order they've been given on the KDE ['from source' guide](https://community.kde.org/Guidelines_and_HOWTOs/Build_from_source/Details).  

1. Dependencies for (?)everything.
- [x] cmake
- [x] extra-cmake-modules
- [ ] qt\*
    This probably includes everything currently in Community (qt5{-declarative,svg,webchannel,webengine,x11extras}), and others. For instance, Qt5UiPlugins are required, which is provided by qt5-tools.

2. Qt-5 dependencies
    Note that it is unclear to me if these are *all* required; phonon seems the most necessary.
- [x] phonon
- [ ] attica
- [ ] strigi
- [ ] libdbusmenu-qt
- [ ] polkit-qt-1

3. Framework
- [x] kitemmodels 
- [x] kitemviews
- [x] kplotting
- [x] threadweaver
- [ ] kcodecs
- [ ] kguiaddons
- [ ] kidletime
- [ ] kwidgetsaddons
- [ ] sonnet
- [ ] kconfig
- [ ] kwindowsystem
- [ ] solid
- [ ] kglobalaccel
- [ ] karchive
- [ ] kdbusaddons
- [ ] kcoreaddons
- [ ] kjs
- [ ] kimageformats
- [ ] kauth
- [ ] kcrash
- [ ] kjobwidgets
- [ ] kcompletion
- [ ] ki18n
- [ ] kdoctools
- [ ] kdnssd
- [ ] kconfigwidgets
- [ ] kservice
- [ ] kiconthemes
- [ ] knotifications
- [ ] kwallet
- [ ] kpty
- [ ] kemoticons
- [ ] kdesu
- [ ] ktextwidgets
- [ ] kxmlgui
- [ ] kbookmarks
- [ ] kio
- [ ] kdesignerplugin
- [ ] knewstuff
- [ ] kparts
- [ ] kpackage
- [ ] kdeclarative
- [ ] kcmutils
- [ ] kinit
- [ ] kded
- [ ] knotifyconfig
- [ ] kunitconversion
- [ ] kjsembed
- [ ] kross
- [ ] kmediaplayer
- [ ] kdewebkit
- [ ] ktexteditor
- [ ] kapidox
- [ ] frameworkintegration
- [ ] kdelibs4support
- [ ] khtml

4. Plasma and friends
- [ ] kactivities
- [ ] pasma-framework
- [ ] kde-workspace
