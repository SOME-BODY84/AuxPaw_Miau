
Debian
====================
This directory contains files used to package nyand/nyan-qt
for Debian-based Linux systems. If you compile nyand/nyan-qt yourself, there are some useful files here.

## nyan: URI support ##


nyan-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install nyan-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your nyan-qt binary to `/usr/bin`
and the `../../share/pixmaps/bitcoin128.png` to `/usr/share/pixmaps`

nyan-qt.protocol (KDE)

