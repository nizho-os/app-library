# App Library

Nizho's app store. It lists the apps on Flathub, shows what each one can reach before you
install it, and installs it for your account only, so no password is needed and the system
image doesn't change. It replaces Capsule.

- **Real data only.** Names, publishers, summaries, icons, versions, licences and release
  dates come from the Flathub catalogue Flatpak already keeps on disk
  (`~/.local/share/flatpak/appstream/flathub/<arch>/active/appstream.xml`, or the system
  copy in `/var/lib/flatpak`). Sizes and permissions come from `flatpak remote-info`.
- **What it can reach** uses the same words as `nizho install`, so the window and the
  terminal never disagree. Apps that reach well beyond themselves (all your files, every
  device, X11, your keys) are listed first, as broad access.
- **Picked by Nizho** is the short list in `/usr/share/nizho/apps.json`, the same one
  `nizho search` reads. It is a selection, not an audit: nobody at Nizho has reviewed these
  apps' code.
- **Installed & updates** lists your apps, checks Flathub for updates and updates one or all.
- **System apps** (Cargo feature `archon`, on by default) is the page for apps installed for
  every account, run by Nizho's Archon package service over D-Bus (`org.nizho.Archon1`).

## Build

Needs Rust 1.79 or newer, GTK 4.14 or newer and libadwaita 1.5 or newer.

```
cargo build --release                         # with the System apps page
cargo build --release --no-default-features   # without Archon
cargo test
```

## Install

| File | Goes to |
|---|---|
| `target/release/nizho-capsule` | `/usr/bin/nizho-capsule` |
| `icons/` | `/usr/share/nizho-capsule/icons/` |
| `data/org.nizho.capsule.desktop` | `/usr/share/applications/` |
| `data/nizho-capsule.svg` | `/usr/share/icons/hicolor/scalable/apps/` |

The binary and app id keep Capsule's names (`nizho-capsule`, `org.nizho.capsule`), so docks
and menus that pinned Capsule keep working. At run time it needs `flatpak`, the `nizho`
command from [nizho-os/nizho](https://github.com/nizho-os/nizho) (`apps/nizho-install`) and
the picks list. `data/apps.json.example` is a copy of that list.

## Tested so far

- The same code, under ArkaOS's names, ran in an ArkaOS virtual machine on 21 November 2026
  with the real `arka` command and Flathub: installing, removing and updating LocalSend
  worked, and a failed update showed its error and left the app as it was.
- This Nizho build compiles with and without `archon`, its unit tests pass, and its pages
  render in a headless preview. It has not run on a Nizho image yet, and the System apps
  page has not been tried against a running Archon service from inside App Library.

## Previews and tests

| Variable | Does |
|---|---|
| `NIZHO_LOOK=light\|dark` | Force a look |
| `NIZHO_APPLIB_PAGE=app:ID\|section:N\|installed\|system\|search:WORDS` | Open that page |
| `NIZHO_APPLIB_OFFLINE=1` | Don't refresh the catalogue from the network |
| `NIZHO_APPLIB_ICONS=DIR` | Read the interface icons from DIR |
| `NIZHO_APPS_CATALOG=FILE` | Read the picks from FILE |
| `NIZHO_SHOT=FILE.png` | Save a picture of the window, then quit |
| `NIZHO_APPLIB_ACT=install,remove` or `update-all` | Press those buttons, for tests |
| `NIZHO_APPLIB_SHOTS=DIR` | Save a picture of each test step |

## Licence

GPL-3.0-or-later, see `LICENSE`. The interface icons are from
[Phosphor Icons](https://phosphoricons.com) (MIT, see `icons/PHOSPHOR-LICENSE.txt`). App
names, icons and descriptions shown in App Library come from Flathub and belong to each app's
authors. `src/packages.rs`, `src/archon.rs` and `src/packages_tests.rs` come from Capsule's
Archon integration in nizho-os/nizho.
