## About

These are my systemd user units located in
`$HOME/.config/systemd/user`.  I use systemd not only for running
daemons, but for starting X session, window manager, various GUI utils
and applications.

For information about systemd user session and about managing X
session with user services, you may read:
- [systemd/User - ArchWiki](https://wiki.archlinux.org/index.php/Systemd/User)
- [Automatic X login without a DM - Arch Linux Forums](https://bbs.archlinux.org/viewtopic.php?id=147913)

## Description

Basically the structure of my user units is the following:
- user daemons (like gpg-agent) and `gui@.target` are "attached"
  to (WantedBy) `default.target`;
- `gui@.target` provides a GUI interface -- it starts:
  + `xbase@.target` -- X server with configuration utils (xmodmap,
    xset, ...);
  + GUI applications (emacs, xterm, ...) "attacted" to this target.

For turning X server into a daemon (for `x@.service`), notifying when
it is ready, i use a wonderful tiny bash script `x-daemon` from
[joukewitteveen/xlogin](https://github.com/joukewitteveen/xlogin).
Previously i used
[sofar/xorg-launch-helper](https://github.com/sofar/xorg-launch-helper).

It is possible to organize multiple X sessions with different settings
and starting applications: DISPLAY :0 will be run on vt7, :1 on vt8
and so on.

## Example

Here is my configuration of X session.

Window manager and vital apps:
```shell
systemctl --user enable stumpwm@0
systemctl --user enable emacs@0
systemctl --user enable conkeror@0
```
X settings:
```shell
systemctl --user enable numlock@0
systemctl --user enable setxkbmap@0
systemctl --user enable xmodmap@0
systemctl --user enable xrdb@0
systemctl --user enable xset@0
systemctl --user enable xsetroot@0
```
I want this X session to start on login, so:
```shell
systemctl --user enable gui@0.target
```
(I set up autologin as described at
[Automatic login to virtual console - ArchWiki](https://wiki.archlinux.org/index.php/Automatic_login_to_virtual_console))

Sometimes i need another instance of X server with another
configuration:
```shell
systemctl --user enable numlock@1
systemctl --user enable xmodmap@1
systemctl --user enable xrdb@1
systemctl --user enable xset@1
systemctl --user enable openbox@1
systemctl --user enable xterm@1
```

Actually i have a shell alias `scu` for `systemctl --user` (and `sc`
for `systemctl`), so i can run the second X session with `scu start
gui@1.target` (it will be started on virtual terminal 8,
i.e. Ctrl+Alt+F7/F8 to switch between X sessions) and if i don't need
it anymore, then just `scu stop gui@1.target` and there is no sign of
it.
