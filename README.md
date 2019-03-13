[![Build status on GitLab CI][gitlab-ci-master-badge]][gitlab-ci-link]
[![Newest release on crates.io][crate-version-badge]][crate-link]
[![Number of downloads on crates.io][crate-download-badge]][crate-link]
[![Project license][crate-license-badge]](LICENSE)

[crate-download-badge]: https://img.shields.io/crates/d/ffsend.svg
[crate-license-badge]: https://img.shields.io/crates/l/ffsend.svg
[crate-link]: https://crates.io/crates/ffsend
[crate-version-badge]: https://img.shields.io/crates/v/ffsend.svg
[gitlab-ci-link]: https://gitlab.com/timvisee/ffsend/pipelines
[gitlab-ci-master-badge]: https://gitlab.com/timvisee/ffsend/badges/master/pipeline.svg

# ffsend [WIP]
> Easily and securely share files from the command line.
> A fully featured [Firefox Send][send] client.

Easily and securely share files and directories from the command line through a
safe, private and encrypted link using a single simple command.
Files are shared using the [Send][send] service and may be up
to 2GB. Others are able to download these files with this tool, or through
their web browser.

[![ffsend usage demo][usage-demo-svg]][usage-demo-asciinema]  
_No demo visible here? View it on [asciinema][usage-demo-asciinema]._

All files are always encrypted on the client, and secrets are never shared with
the remote host. An optional password may be specified, and a default file
lifetime of 1 (up to 20) download or 24 hours is enforced to ensure your stuff
does not remain online forever.
This provides a secure platform to share your files.
Find out more about security [here](#security).

- [Features](#features)
- [Usage](#usage)
- [Requirements](#requirements)
- [Install](#install) ([GNU/Linux](#linux), [macOS](#macos), [Windows](#windows), [_Other OS/arch_](#other-os-or-architecture))
- [Build](#build)
- [Configuration and environment](#configuration-and-environment)
- [Security](#security)
- [Help](#help)
- [Special thanks](#special-thanks)
- [License](#license)

The public [Send][send] service that is used as default host is provided by
[Mozilla][mozilla].  
This application is not affiliated with [Mozilla][mozilla], [Firefox][firefox]
or [Firefox Send][send] in any way.

_Note: this tool is currently in alpha_

## Features
- Fully featured and friendly command line tool
- Upload and download files and directories securely
- Always encrypted on the client
- Additional password protection, generation and configurable download limits
- Supports old and new Firefox Send server versions
- File and directory archiving and extraction
- History tracking your files for easy management
- Ability to use custom Send hosts
- Inspect or delete shared files
- Accurate error reporting
- Streaming encryption and uploading/downloading, very low memory footprint
- Intended for use in scripts without interaction
- Upcomming: Firefox Account integration (higher download counts, longer expiry times)

For a list of upcoming features and ideas, take a look at the
current [open issues](https://gitlab.com/timvisee/ffsend/issues) over on GitLab.

## Usage
Easily upload and download:

```bash
# Simple upload
$ ffsend upload my-file.txt
Share link: https://send.firefox.com/#sample-share-url

# Advanced upload
# - Specify a download limit of 20
# - Enter a password to encrypt the file
# - Archive the file before uploading
# - Copy the shareable link to your clipboard
# - Open the shareable link in your browser
$ ffsend upload --downloads 20 --password --archive --copy --open my-file.txt
Password: ******
Share link: https://send.firefox.com/#sample-share-url

# Upload to your own host
$ ffsend u -h https://example.com/ my-file.txt
Share link: https://example.com/#sample-share-url

# Simple download
$ ffsend download https://send.firefox.com/#sample-share-url
```

Inspect remote files:

```bash
# Check if a file exists
$ ffsend exists https://send.firefox.com/#sample-share-url
Exists: yes

# Fetch remote file info
$ ffsend info https://send.firefox.com/#sample-share-url
ID:         b087066715
Name:       my-file.txt
Size:       12 KiB
MIME:       text/plain
Downloads:  0 of 10
Expiry:     18h2m (64928s)
```

Other commands include:
```bash
# View your file history
$ ffsend history
#  LINK                                        EXPIRY  OWNER TOKEN
1  https://send.firefox.com/#sample-share-url  23h57m  eea9f544f6d5df8a5afd
2  https://send.firefox.com/#other-sample-url  17h38m  1e9fef63fee3baaf54ce
3  https://example.com/#sample-share-url       37m30s  8eb28bc1bc85cfdab0e4

# Change the password after uploading
$ ffsend password https://send.firefox.com/#sample-share-url
Password: ******

# Delete a file
$ ffsend delete https://send.firefox.com/#sample-share-url
```

Use the `--help` flag, `help` subcommand, or see the [help](#help) section for
all available subcommands.

## Requirements
- GNU/Linux, Windows or macOS
- A terminal :sunglasses:
- Internet connection for uploading and downloading
- GNU/Linux specific:
  - OpenSSL & CA certificates:
    - Ubuntu/Debian: `apt install openssl ca-certificates`
  - Optional: `xclip` for clipboard support
    - Ubuntu/Debian: `apt install xclip`
    - CentOS/Red Hat/openSUSE/Fedora: `yum install xclip`
    - Arch: `pacman -S xclip`
- Windows specific:
  - OpenSSL v1.1.0j: [» Installer][openssl-windows-installer]
- macOS specific:
  - OpenSSL: `brew install openssl@1.1`

## Install
<!-- Before installing, make sure you meet all requirements listed
[here](#requirements) -->

Because `ffsend` is still in alpha, only limited installation options are
available right now.

Make sure you meet and install the [requirements](#requirements).

See the operating system specific instructions below:
* [GNU/Linux](#linux)
* [macOS](#macos)
* [Windows](#windows)
* [_Other OS or architecture_](#other-os-or-architecture)

### GNU/Linux (all distributions)
Using the [snap](#linux-snap-package) package is recommended if supported.  
Alternatively you may install it manually using the
[prebuilt binaries](#linux-prebuilt-binaries).

Only 64-bit (`x86_64`) packages and binaries are provided.
For other architectures and configurations you may [compile from source](#build).

More packages options will be coming soon.

#### GNU/Linux: snap package
[» `ffsend`][snapcraft-ffsend]
```bash
snap install ffsend
ffsend --help
```

#### GNU/Linux: Arch AUR packages
[» `ffsend`][aur-ffsend] (binary package)  
[» `ffsend-git`][aur-ffsend-git] (compile from source package)

```bash
yaourt -S ffsend
# or
yay ffsend
# or
aurget -S ffsend
# or using any other AUR installer

ffsend --help
```

#### GNU/Linux: Prebuilt binaries
Check out the [latest release][github-latest-release] assets for GNU/Linux binaries.  
Use the `ffsend-v*-linux-x64-static` binary, to minimize the chance for issues.

Make sure you meet and install the [requirements](#requirements) before you
continue.

You must make the binary executable, and may want to move it into `/usr/bin` to
make it easily executable:

```bash
# Rename binary to ffsend
mv ./ffsend-* ./ffsend

# Mark binary as executable
chmod a+x ./ffsend

# Move binary into path, to make it easily usable
sudo mv ./ffsend /usr/bin/

ffsend --help
```

### macOS
Currently only a [prebuilt binary](#macos-prebuilt-binaries) is available for macOS.

A `homebrew` package will be coming soon.

#### macOS: Prebuilt binaries
Check out the [latest release][github-latest-release] assets for a macOS binary.

You must install `openssl` through [`homebrew`][homebrew] which `ffsend` depends
on.

Then, mark the downloaded binary as as executable.
You then may want to move it into `/usr/bin/` to make the `ffsend` command
globally available:

```bash
# Install openssl dependency
brew install openssl@1.1

# Rename file to ffsend
mv ./ffsend-* ./ffsend

# Mark binary as executable
chmod a+x ./ffsend

# Move binary into path, to make it easily usable
sudo mv ./ffsend /usr/bin/

ffsend
```

### Windows
Currently only [prebuilt binaries](#windows-prebuilt-binaries) are available for Windows.

If you're using the [Windows Subsystem for Linux](wsl), it's highly recommended
to install the [prebuilt GNU/Linux binary](#prebuilt-binaries-for-linux) instead.

Only 64-bit (`x86_64`) binaries are provided.
For other architectures and configurations you may [compile from source](#build).

A `chocolatey` and a `scoop` package along with an `.msi` installer will be coming soon.

#### Windows: Prebuilt binaries
Check out the [latest release][github-latest-release] assets for Windows binaries.
Use the `ffsend-v*-windows-x64-static` binary, to minimize the chance for issues.

You must install OpenSSL 1.1.0j which `ffsend` depends on. The installer for
this can be found here: [» Installer][openssl-windows-installer]

You can use `ffsend` from the command line in the same directory:
```cmd
.\ffsend.exe --help
```

To make it globally invokable as `ffsend`, you must make the binary available in
your systems `PATH`. The easiest solution is to move it into `System32`:
```cmd
move .\ffsend.exe C:\Windows\System32\ffsend.exe
```

### Other OS or architecture
There are currently no other binaries or packages available.

You can [build the project from source](#build) instead.

## Build
To build and install `ffsend` yourself, you meet the following requirements
before proceeding:

### Build requirements
- Regular [requirements](#requirements)
- [`git`][git]
- [`rust`][rust] `v1.32` or higher (install using [`rustup`][rustup])
- [OpenSSL][openssl] or [LibreSSL][libressl] libraries and headers must be available
	- GNU/Linux:
		- Debian, Ubuntu and derivatives: `apt install build-essential cmake pkg-config libssl-dev`
		- CentOS/Red Hat/openSUSE: `yum install gcc gcc-c++ make cmake openssl-devel`
		- Arch: `pacman -S openssl base-devel`
    - Gentoo: `emerge -a dev-util/pkgconfig dev-util/cmake dev-libs/openssl`
		- Fedora: `dnf install gcc gcc-c++ make cmake openssl-devel`
		- Or see instructions [here](https://github.com/sfackler/rust-openssl#linux)
	- Windows:
		- See instructions here [here](https://github.com/sfackler/rust-openssl#windows-msvc)
	- macOS:
		- Using `brew`: `brew install cmake pkg-config openssl`
		- Or see instructions [here](https://github.com/sfackler/rust-openssl#osx)

### Compile and install
Then, walk through one of the following steps to compile and install `ffsend`:

- Compile and install it directly from cargo:

	```bash
	# Compile and install from cargo
	cargo install ffsend -f

	# Start using ffsend
	ffsend --help
	```

- Or clone the repository and install it with `cargo`:

	```bash
	# Clone the project
	git clone https://github.com/timvisee/ffsend.git
	cd ffsend

	# Compile and install
	cargo install -f

	# Start using ffsend
	ffsend --help

	# or run it directly from cargo
	cargo run --release -- --help 
	```

- Or clone the repository and invoke the binary directly (GNU/Linux, macOS):

	```bash
	# Clone the project
	git clone https://github.com/timvisee/ffsend.git
	cd ffsend

	# Build the project (release version)
	cargo build --release

	# Start using ffsend
	./target/release/ffsend --help
	``` 

### Compile features / use flags
Different use flags are available for `ffsend` to toggle whether to include
various features.
The following features are available, some of which are enabled by default:

| Feature     | Enabled | Description                                                |
| :---------: | :-----: | :--------------------------------------------------------- |
| `send2`     | Default | Support for Firefox Send v2 servers                        |
| `send3`     | Default | Support for Firefox Send v3 servers                        |
| `clipboard` | Default | Support for copying links to the clipboard                 |
| `history`   | Default | Support for tracking files in history                      |
| `archive`   | Default | Support for archiving and extracting uploads and downloads |
| `no-color`  |         | Compile without color support in error and help messages   |

To enable features during building or installation, specify them with
`--features <features...>` when using `cargo`.
You may want to disable default features first using
`--no-default-features`.
Here are some examples:

```bash
# Defaults set of features with no-color, one of
cargo install --features no-color
cargo build --release --features no-color

# None of the features
cargo install --no-default-features

# Only history and clipboard support
cargo install --no-default--features --features history,clipboard
```

For Windows systems it is recommended to provide the `no-color` flag, as color
support in Windows terminals is flaky.

## Configuration and environment
The following environment variables may be used to configure the following
defaults. The CLI flag is shown along with it, to better describe the relation
to command line arguments:

| Variable                  | CLI flag                       | Description                       |
| :------------------------ | :----------------------------: | :-------------------------------- |
| `FFSEND_HISTORY`          | `--history <FILE>`             | History file path                 |
| `FFSEND_HOST`             | `--host <URL>`                 | Upload host                       |
| `FFSEND_TIMEOUT`          | `--timeout <SECONDS>`          | Request timeout (0 to disable)    |
| `FFSEND_TRANSFER_TIMEOUT` | `--transfer-timeout <SECONDS>` | Transfer timeout (0 to disable)   |
| `FFSEND_API`              | `--api <VERSION>`              | Server API version, `-` to lookup |

These environment variables may be used to toggle a flag, simply by making them
available. The actual value of these variables is ignored, and variables may be
empty.

| Variable             | CLI flag        | Description                        |
| :------------------- | :-------------: | :--------------------------------- |
| `FFSEND_FORCE`       | `--force`       | Force operations                   |
| `FFSEND_NO_INTERACT` | `--no-interact` | No interaction for prompts         |
| `FFSEND_YES`         | `--yes`         | Assume yes for prompts             |
| `FFSEND_INCOGNITO`   | `--incognito`   | Incognito mode, don't use history  |
| `FFSEND_OPEN`        | `--open`        | Open share link of uploaded file   |
| `FFSEND_ARCHIVE`     | `--archive`     | Archive files uploaded             |
| `FFSEND_EXTRACT`     | `--extract`     | Extract files downloaded           |
| `FFSEND_COPY`        | `--copy`        | Copy share link to clipboard       |
| `FFSEND_COPY_CMD`    | `--copy-cmd`    | Copy download command to clipboard |
| `FFSEND_QUIET`       | `--quiet`       | Log quiet information              |
| `FFSEND_VERBOSE`     | `--verbose`     | Log verbose information            |

At this time, no configuration or _dotfile_ file support is available.
This will be something added in a later release.

## Security
In short; the `ffsend` tool and the [Send][send] service can be considered
secure, and may be used to share sensitive files. Note though that the
created share link for an upload will allow anyone to download the file. 
Make sure you don't share this link with unauthorized people.

For more detailed information on encryption, please read the rest of the
paragraphs in this security section.

_Note: even though the encryption method is considered secure, this `ffsend`
tool does not provide any warranty in any way, shape or form for files that
somehow got decrypted without proper authorization._

#### Client side encryption
`ffsend` uses client side encryption, to ensure your files are securely
encrypted before they are uploaded to the remote host. This makes it impossible
for third parties to decrypt your file without having the secret (encryption
key). The file and its metadata are encrypted using `128-bit AES-GCM`, and a
`HMAC SHA-256` signing key is used for request authentication.
This is consistent with the encryption documentation provided by the
[Send][send] service, `ffsend` is a tool for.

A detailed list on the encryption/decryption steps, and on what encryption is
exactly used can be found [here][send-encryption] in the official service
documentation.

#### Note on share link security
The encryption secret, that is used to decrypt the file when downloading,
is included in the share URL behind the `#` (hash). This secret is never sent
the remote server directly when using the share link in your browser.
It would be possible however for a webpage to load some malicious JavaScript
snippet that eventually steals the secret from the link once the page is loaded.
Although this scenario is extremely unlikely, there are some options to prevent
this from happening:

- Only use this `ffsend` tool, do not use the share link in your browser.
- Add additional protection by specifying a password using `--password` while
  uploading, or using the `password` subcommand afterwards.
- Host a secure [Send][send] service instance yourself.

A complete overview on encryption can be found in the official service
documentation [here][send-encryption].

## Help
```
$ ffsend help

ffsend 0.2.29
Tim Visee <timvisee.com>
Easily and securely share files from the command line.
A fully featured Firefox Send client.

USAGE:
    ffsend [FLAGS] [OPTIONS] [SUBCOMMAND]

FLAGS:
    -f, --force          Force the action, ignore warnings
    -h, --help           Prints help information
    -i, --incognito      Don't update local history for actions
    -I, --no-interact    Not interactive, do not prompt
    -q, --quiet          Produce output suitable for logging and automation
    -V, --version        Prints version information
    -v, --verbose        Enable verbose information and logging
    -y, --yes            Assume yes for prompts

OPTIONS:
    -H, --history <FILE>                Use the specified history file [env: FFSEND_HISTORY]
    -t, --timeout <SECONDS>             Request timeout (0 to disable) [env: FFSEND_TIMEOUT]
    -T, --transfer-timeout <SECONDS>    Transfer timeout (0 to disable) [env: FFSEND_TRANSFER_TIMEOUT]

SUBCOMMANDS:
    upload        Upload files [aliases: u, up]
    download      Download files [aliases: d, down]
    debug         View debug information [aliases: dbg]
    delete        Delete a shared file [aliases: del]
    exists        Check whether a remote file exists [aliases: e]
    help          Prints this message or the help of the given subcommand(s)
    history       View file history [aliases: h]
    info          Fetch info about a shared file [aliases: i]
    parameters    Change parameters of a shared file [aliases: params]
    password      Change the password of a shared file [aliases: pass, p]
    version       Determine the Send server version [aliases: v]

The public Send service that is used as default host is provided by Mozilla.
This application is not affiliated with Mozilla, Firefox or Firefox Send.
```

## Special thanks
- to [Mozilla][mozilla] for building and hosting the amazing
  [Firefox Send][send] service
- to everyone involved with [asciinema][asciinema] and [svg-term][svg-term] for
  providing tools to make great visual demos
- to everyone involved in all crate dependencies used

## License
This project is released under the GNU GPL-3.0 license.
Check out the [LICENSE](LICENSE) file for more information. 

[usage-demo-asciinema]: https://asciinema.org/a/182225
[usage-demo-svg]: https://cdn.rawgit.com/timvisee/ffsend/6e8ef55b/res/demo.svg
[firefox]: https://firefox.com/
[git]: https://git-scm.com/
[libressl]: https://libressl.org/
[mozilla]: https://mozilla.org/
[openssl]: https://www.openssl.org/
[openssl-windows-installer]: https://slproweb.com/download/Win64OpenSSL_Light-1_1_0j.exe
[rust]: https://rust-lang.org/
[rustup]: https://rustup.rs/
[send]: https://send.firefox.com/
[send-encryption]: https://github.com/mozilla/send/blob/master/docs/encryption.md
[asciinema]: https://asciinema.org/
[svg-term]: https://github.com/marionebl/svg-term-cli
[github-latest-release]: https://github.com/timvisee/ffsend/releases/latest
[aur-ffsend]: https://aur.archlinux.org/packages/ffsend/
[aur-ffsend-git]: https://aur.archlinux.org/packages/ffsend-git/
[snapcraft-ffsend]: https://snapcraft.io/ffsend
[homebrew]: https://brew.sh/
[wsl]: https://docs.microsoft.com/en-us/windows/wsl/install-win10
