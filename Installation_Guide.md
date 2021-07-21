<img alt="OpenSK logo" src="docs/img/OpenSK.svg" width="200px">

# Installation guide

This document describes in details how to turn a Feitian nRF52840 board into a
working FIDO2 security key.

## Pre-requisite

### Hardware

You will need the following supported board:

*   [Feitian nRF52840 USB dongle v2](https://feitiantech.github.io/OpenSK_USB/).


### Software

In order to compile and flash a working OpenSK firmware, you will need the
following:

*   [Ubuntu latest version](https://ubuntu.com/download/desktop/thank-you?version=20.04.2.0&architecture=amd64#download) (Could be installed on VMware Workstation)
*   Install git (can be installed by sudo apt install git)
*   rustup (can be installed with [Rustup](https://rustup.rs/))
*   python3 and pip (can be installed with the `python3-pip` package on Debian)
*   The OpenSSL command line tool (can be installed with the `libssl-dev`
    package on Debian)
*   [nrfutil](https://pypi.org/project/nrfutil/) (can be installed by `sudo pip3 install nrfutil` or `sudo pip3 install nrfutil --user` )


The scripts provided in this project have been tested under Ubuntu 20.04 LTS on VMware Workstation v15.5.2. 

## Compiling the firmware

### Initial setup

1.  Clone git repository using
```shell
$ git clone https://github.com/google/OpenSK.git
Cloning into 'OpenSK'...
remote: Enumerating objects: 4984, done.
remote: Counting objects: 100% (300/300), done.
remote: Compressing objects: 100% (168/168), done.
remote: Total 4984 (delta 158), reused 238 (delta 127), pack-reused 4684
Receiving objects: 100% (4984/4984), 5.38 MiB | 1.58 MiB/s, done.
Resolving deltas: 100% (3071/3071), done.
```
2.  Installing Rust 
```shell
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
info: downloading installer
Welcome to Rust!
This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.
Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:
/home/epitacs2/.rustup
This can be modified with the RUSTUP_HOME environment variable.
The Cargo home directory located at:
/home/epitacs2/.cargo
This can be modified with the CARGO_HOME environment variable.
The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:
/home/epitacs2/.cargo/bin
This path will then be added to your PATH environment variable by
modifying the profile files located at:
/home/epitacs2/. profile
/home/epitacs2/.bashrc
You can uninstall at any time with rustup self uninstall and
these changes will be reverted.
Current installation options:
default host triple: x86_64-unknown-linux-gnu
default toolchain: stable (default)
profile: default
modify PATH variable: yes
1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1

info: profile set to 'default'
info: default host triple is x86_64 - unknown-linux-gnu
warning: Updating existing toolchain, profile choice will be ignored
info: syncing channel updates for 'stable-x86_64-unknown-linux-gnu'
info: default toolchain set to 'stable-x86_64 - unknown-linux-gnu'
stable-x86_64-unknown-linux-gnu unchanged - rustc 1.53.0 (53cb7b09b 2021-06-17)
Rust is installed now. Great!
To get started you may need to restart your current shell.
This would reload your PATH environment variable to include
Cargo's bin directory ($HOME/.cargo/bin).
To configure your current shell, run:
source $HOME/.cargo/env
$ source $HOME/ .cargo/env
```
3.  Access OpenSK directory
```shell
$ cd OpenSK
```
4.  Running the setup script
```shell
$./setup.sh
Submodule 'third_party/libtock-rs' (https://github.com/tock/libtock-rs) registered for path 'third_party/libtock-rs'
Submodule 'third party/tock' (https://github.com/tock/tock) registered for path 'third_party/tock'
Cloning into '/home/epitacs2/OpenSK/third_party/libtock-rs'...
Cloning into '/home/epitacs2/OpenSK/third_party/tock'...
Submodule path 'third_party/libtock-rs': checked out '828c19de9292ddbca0e2da6a161c0c38124c5053'
Submodule path 'third party/tock': checked out 'c5b7a4f2c89a8c067f0f5786788f4037b32329fd'
[-] Copying additional boards to Tock... DONE.
[-] Applying patch "01-persistent-storage.patch". DONE.
[-] Applying patch "O2-usb.patch"... DONE.
[-] Applying patch "03-usb-debugging.patch"... DONE.
[-] Applying patch "04-additional-boards. patch"... DONE.
[-] Applying patch "05-mpu-fix.patch"... DONE.
[-] Applying patch "06-update-uicr.patch"... DONE.
[-] Applying patch "07-firmware-protect.patch"... DONE.
Signature ok
subject=CN = OpenSK CA
Getting Private key
Signature ok
subject=CN = OpenSK Hacker Edition
Getting CA Private key
info: syncing channel updates for nightly-2020-06-10-x86_64 - unknown-linux-gnu'
nightly-2020-06-10-x86_64-unknown-linux-gnu unchanged rustc 1.46.0-nightly (feb3536eb 2020-06-09)
info: checking for self-updates
/usr/lib/python3/dist-packages/secretstorage/dhcrypto.py:15: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
/usr/lib/python3/dist-packages/secretstorage/util.py:19: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
Requirement already up-to-date: tockloader==1.5 in /home/epitacs2/.local/lib/python3.8/site-packages (1.5.0)
Requirement already up-to-date: six in /home/epitacs2/.local/lib/python3.8/site-packages (1.16.0)
Requirement already up-to-date: intelhex in /home/epitacs2/.local/lib/python3.8/site-packages (2.3.0)
Requirement already satisfied, skipping upgrade: argcomplete>=1.8.2 in /home/epitacs2/.local/lib/python3.8/site-packages (from tockloader==1.5) (1.12.3)
Requirement already satisfied, skipping upgrade: crcmod>=1.7 in /home/epitacs2/.local/lib/python3.8/site-packages (from tockloader==1.5) (1.7)
Requirement already satisfied, skipping upgrade: colorama>=0.3.7 in /home/epitacs2/.local/lib/python3.8/site-packages (from tockloader==1.5) (0.4.4)
Requirement already satisfied, skipping upgrade: pytoml>=0.1.11 in /home/epitacs2/.local/lib/python3.8/site-packages (from tockloader==1.5) (0.1.21)
Requirement already satisfied, skipping upgrade: pyserial>=3.0.1 in /home/epitacs2/.local/lib/python3.8/site-packages (from tockloader==1.5) (3.5)
info: component 'rust-std' for target 'thumbv7em-none-eabi' is up to date
Updating crates.io index
Installing elftab v0.6.0
Compiling libc v0.2.97
Compiling version_check v0.9.3
Compiling proc-macro2 v1.0.27
Compiling unicode-xid vo.2.2
Compiling autocfg v1.0.1
Compiling syn v1.0.73
Compiling bitflags v1.2.1
Compiling unicode-width vo.1.8
Compiling unicode-segmentation v1.7.1
Compiling strsim v0.8.0
Compiling ansi_term vo.11.0
Compiling cfg-if v1.0.0
Compiling vec_map vo.8.2
Compiling byteorder v0.5.3
Compiling lazy_static v1.4.0
Compiling proc-macro-error-attr v1.0.4
Compiling proc-macro-error v1.0.4
Compiling num-traits vo.2.14
Compiling num-integer vo.1.44
Compiling textwrap vo.11.0
Compiling heck v0.3.3
Compiling elf vo.0.10
Compiling atty vo.2.14
Compiling time vo.1.44
Compiling xattr vo.2.2
Compiling filetime vo.2.14
Compiling quote v1.0.9
Compiling clap v2.33.3
Compiling tar v0.4.35
Compiling chrono v0.4.19
Compiling structopt-derive vo.4.14
Compiling structopt vo.3.21
Compiling elf2tab vo.6.0
Finished release [optimized] target(s) in 3m 14s
Installing elftab/bin/elf2tab
Installed package 'elf2tab v0.6.0' (executable 'elfztab“)
warning: be sure to add 'elfztab/bin' to your PATH to be able to run the installed binaries
/usr/lib/python3/dist-packages/secretstorage/dhcrypto.py:15: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
/usr/lib/python3/dist-packages/secretstorage/util.py:19: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
Requirement already up-to-date: colorama in /home/epitacs2/.local/lib/python3.8/site-packages (0.4.4)
Requirement already up-to-date: tqdm in /home/epitacs2/.local/lib/python3.8/site-packages (4.61.1)
Requirement already up-to-date: cryptography in /home/epitacs2/.local/lib/python3.8/site-packages (3.4.7)
Requirement already up-to-date: fido2>=0.9.1 in /home/epitacs2/.local/lib/python3.8/site-packages (0.9.1)
Requirement already satisfied, skipping upgrade: cffi>=1.12 in /home/epitacs2/.local/lib/python3.8/site-packages (from cryptography) (1.14.5)
Requirement already satisfied, skipping upgrade: six in /home/epitacs2/.local/lib/python3.8/site-packages (from fido2>=0.9.1) (1.16.0)
Requirement already satisfied, skipping upgrade: pycparser in /home/epitacs2/.local/lib/python3.8/site-packages (from cffi>=1.12->cryptography) (2.20)
```

The setup.sh script performs the following steps:

1.  Make sure that the git submodules are checked out

2.  Apply our patches that haven't yet been merged upstream to both
    [Tock](https://github.com/tock/tock) and
    [libtock-rs](https://github.com/tock/libtock-rs)

3.  Generate a self-signed certificate authority as well as a private key and a
    corresponding certificate for your OpenSK key signed by this CA. You will be
    able to replace them with your own certificate and private key.

4.  Ensure that your Rust toolchain is using the same version that we tested
    OpenSK with.

5.  Install [tockloader](https://github.com/tock/tockloader).

6.  Ensure that the Rust toolchain can compile code for ARM devices.


### Fix for a potential error
```shell
copying additional boards to Tock... DONE.
[-] Applying patch "01-persistent-storage.patch" ... DONE.
[-] Applying patch "02-usb.patch" ... DONE.
[-] Applying patch "03-usb-debugging.patch" ... DONE.
[-] Applying patch "04-additional-boards.patch" ... DONE.
[-] Applying patch "05-mpu-fix.patch" DONE.
[ - ] Applying patch "06-update-uicr.patch" ... DONE.
[-] Applying patch "07-firmware-protect.patch" DONE.
Signature ok
subject=CN OpenSK CA
Getting Private key
Signature ok
subject=CN = OpenSK Hacker Edition
Getting CA Private Key
tools/gen_key_materials.sh: line 93: uuidgen: command not found
```

```shell
$ sudo apt install uuid-runtime
Reading package lists ... Done
Building dependency tree ... Done
Reading state information ... Done
The following New packages will be installed:
uuid-runtime
0 upgraded, 1 newly installed, 0 to remove and 16 not upgraded.
Need to get 101 kB of archives.
After this operation, 224 kB of additional disk space will be used.
Get:1 http://cz.archive.ubuntu.com/ubuntu focal main (101 kB]
Fetched 101 kB in 1s (167 kB/s)
Selecting previously unselected package uuid-runtime.
(Reading database 272193 files and directories currently installed.)
Preparing to unpack ... /uuid-runtime_2.36.1-7_amd64.deb ...
Unpacking uuid-runtime (2.36.1-7) ...
Setting up uuid-runtime (2.36.1-7) ...
Adding group uuidd' (GID 143) ...
Done.
Warning: The home dir /run/uuidd you specified can't be accessed: No such file or directory
Adding system user "uuidd' (UID 134) ...
Adding new user "uuidd' (UID 134) with group "uuidd'
Not creating home directory /run/uuidd'.
update-rc.d: We have no instructions for the uuidd init script.
update-rc.d: It looks like a non-network service, we enable it.
Created symlink /etc/systemd/system/sockets.target.wants/uuidd.socket -> /lib/system/system/uuidd.socket.
uuidd.service is disabled or a static unit, not starting it.
Processing triggers for man-db (2.9.4-2) ...
Processing triggers for kali-menu (2021.2.3) ...
```
### Installing nrfutil
If you face an error while using `pip3 install nrfutil`, proceed by the following command: 
```shell 
pip3 install nrfutil --user 
```

```shell
$ pip3 install nrfutil
/usr/lib/python3/dist-packages/secretstorage/dhcrypto.py:15: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
/usr/lib/python3/dist-packages/secretstorage/util.py:19: CryptographyDeprecationWarning: int_from_bytes is deprecated, use int.from_bytes instead
from cryptography.utils import int_from_bytes
Collecting nrfutil
Downloading nrfutil-6.1.0.tar.gz (842 kB)
842 kB 4.5 MB/s
Requirement already satisfied: click in /usr/lib/python3/dist-packages (from nofutil) (7.0)
Requirement already satisfied: crcmod in /home/epitacs2/.local/lib/python3.8/site-packages (from nrfutil) (1.7)
Collecting ecdsa
Downloading ecdsa -0.17.0-py2.py3-none-any.whl (119 kB)
119 kB 3.5 MB/s
Requirement already satisfied: intelhex in /home/epitacs2/.local/lib/python3.8/site-packages (from nrfutil) (2.3.0)
Collecting libusb1
Downloading libusb1-1.9.2-py3-none-any.whl (58 kB)
58 kB 3.0 MB/s
Collecting pc_ble_driver_py>=0.14.2
Downloading pc_ble driver py-0.15.0-cp38-cp38-manylinux2010_x86_64.whl (2.4 MB)
2.4 MB 3.1 MB/s
Collecting piccata
Downloading piccata-2.0.1-py3-none-any.whl (21 kB)
Requirement already satisfied: protobuf in /usr/lib/python3/dist-packages (from nofutil) (3.6.1)
Requirement already satisfied: pyserial in /home/epitacs2/.local/lib/python3.8/site-packages (from nrfutil) (3.5)
Collecting pyspinel>=1.0.023
Downloading pyspinel-1.0.3.tar.gz (58 kB)
|| 58 KB 3.0 MB/s
Requirement already satisfied: pyyaml in /usr/lib/python3/dist-packages (from nrfutil) (5.3.1)
Requirement already satisfied: tqdm in /home/epitacs2/.local/lib/python3.8/site-packages (from nrfutil) (4.61.1)
Requirement already satisfied: six>=1.9.0 in /home/epitacs2/local/lib/python3.8/site-packages (from ecdsa->ncfutil) (1.16.0)
Collecting wrapt
Downloading wrapt-1.12.1.tar.gz (27 kB)
Building wheels for collected packages: nrfutil, pyspinel, wrapt
Building wheel for nrfutil (setup.py) ... done
Created wheel for nofutil: filename=nrfutil-6.1.0-py3-none-any.whl size=897463 sha256=fcd161350bdb458dbaaf7c8668ec51c9ac5d9f3483fe2a7d16d906c0c767d176
Stored in directory: /home/epitacs2/.cache/pip/wheels/f4/04/d2/ccb2f5afdfd03523f2769ce9c15984a96315a085b8c3be901a
Building wheel for pyspinel (setup.py) ... done
Created wheel for pyspinel: filename=pyspinel-1.0.3-py3-none-any.whl size=65036 sha256=0f789343091cba6637cb1525434c2c3972bee6119020330b7d5f68de9c3813cc
Stored in directory: /home/epitacs2/.cache/pip/wheels/01/a4/e6/d5556boca15a3edo 200ff7906a098ad1161e05ed801bbccf64
Building wheel for wrapt (setup.py) ... done
Created wheel for wrapt: filename=wrapt-1.12.1-cp38-cp38-linux_x86_64.whl size=78508 sha256=C46977093cf4c79ebd6ebfe0b749b3ee68c5a47ccc6cc25db79ec7df3f3b5011
Stored in directory: /home/epitacs2/.cache/pip/wheels/5f/fd/9e/b6cf5890494cb8efob5eaff72e5d55a70fb56316007d6dfe73
Successfully built nrfutil pyspinel wrapt
Installing collected packages: ecdsa, libusbi, wrapt, pc-ble-driver-py, piccata, pyspinel, nrfutil
Successfully installed ecdsa -0.17.0 libusb1-1.9.2 nrfutil-6.1.0 pc-ble-driver-py-0.15.o piccata-2.0.1 pyspinel-1.0.3 wrapt-1.12.1
```

### Replacing the certificates

All the generated certificates and private keys are stored in the directory
`crypto_data/`.

This is the expected content after running our `setup.sh` script:

File              | Purpose
----------------- | --------------------------------------------------------
`aaguid.txt`      | Text file containaing the AAGUID value
`opensk_ca.csr`   | Certificate sign request for the Root CA
`opensk_ca.key`   | ECC secp256r1 private key used for the Root CA
`opensk_ca.pem`   | PEM encoded certificate of the Root CA
`opensk_ca.srl`   | File generated by OpenSSL
`opensk_cert.csr` | Certificate sign request for the attestation certificate
`opensk_cert.pem` | PEM encoded certificate used for the authenticator
`opensk.key`      | ECC secp256r1 private key used for the autenticator

If you want to use your own attestation certificate and private key, simply
replace `opensk_cert.pem` and `opensk.key` files.

Our build script `build.rs` is responsible for converting the `aaguid.txt` file
into raw data that is then used by the Rust file `src/ctap/key_material.rs`.

Our configuration script `tools/configure.py` is responsible for configuring
an OpenSK device with the correct certificate and private key.



### Flashing a firmware

#### Feitian nRF52840 board

1.  Run our script for compiling/flashing Tock OS and OpenSK on your device
    (_output may differ_):
    
    During this deployment you don't have to enter to DFU (Device Firmware Upgrade) (Bootloader)
    ```shell
    $ ./deploy.py --board=nrf52840_dongle_dfu --opensk --programmer=nordicdfu
    info: Updating rust toolchain to nightly-2020-06-10
    info: syncing channel updates for 'nightly-2020-06-10-x86_64-unknown-linux-gnu'
    info: checking for elf-updates
    info: component 'rust-std' for target 'thumbv7em-none-eabi' is up to date
    info: Rust toolchain up-to-date
    info: Building Tock os for board nrf52840_dongle_dfu
    info: This is the version for the rustup toolchain manager, not the rustc compiler.
    info: syncing channel updates for 'nightly-2020-06-03-x86_64 - unknown-linux-gnu'
    info: latest update on 2020-06-03, rust version 1.45.0-nightly (fe10f1a49 2020-06-02)
    info: downloading component 'cargo'
    info: downloading component 'clippy'
    info: downloading component 'rust-docs'
    info: downloading component 'rust-std'
    info: downloading component 'rustc'
    info: downloading component 'rustfmt'
    info: installing component 'cargo'
    info: installing component 'clippy
    info: installing component 'rust-docs'
    info: installing component 'rust-std'
    Compiling syn v1.0.73
    Compiling libc vo.2.97
    Compiling libtock_core vo.1.0 (/home/epitacs2/Desktop/OpenSK/third_party/libtock-rs/core)
    Compiling cfg-if v1.0.0
    Compiling arrayref vo.3.6
    Compiling cbor v0.1.0 (/home/epitacs2/Desktop/OpenSK/libraries/cbor)
    Compiling by teorder v1.4.3
    Compiling subtle v2.4.0
    Compiling linked_list_allocator v0.8.11
    Compiling persistent_store vo.1.0 (/home/epitacs2/Desktop/OpenSK/libraries/persistent_store)
    Compiling quote v1.0.9
    Compiling getrandom vo.2.3
    Compiling uuid v0.8.2
    Compiling libtock_codegen vo.1.0 (/home/epitacs2/Desktop/OpenSK/third_party/libtock-rs/codegen)
    Compiling ctap2 v1.0.0 (/home/epitacs2/Desktop/OpenSK/)
    Compiling libtock_drivers vo.1.0 (/home/epitacs2/Desktop/OpenSK/third_party/libtock-drivers)
    Compiling crypto vo.1.0 (/home/epitacs2/Desktop/OpenSK/libraries/crypto)
    Compiling lang_items vo.1.0 (/home/epitacs2/Desktop/OpenSK/third_party/lang-items)
    Finished release (optimized] target(s) in 2m 14s
    info: Generating Tock TAB file for application/example ctap2
    info: Generating all-merged HEX file: target/nrf52840_dongle_dfu_merged.hex
    info: Creating DFU package
    info: Please insert the dongle and switch it to DFU mode by keeping the button pressed while inserting...
    info: Press [ENTER] when ready.
    info: Flashing device using DFU...
    [####################################] 100%
    Device programmed.


### Custom installation

For more options, we invite you to read the help of our `deploy.py` script by
running `./deploy.py --help`.

### Installing the udev rule (Linux only)

By default on Linux, a USB device will require root privilege in order interact
with it. As it is not recommended to run your web browser with such a high
privileged account, we made a `udev` rule file to allow regular users to
interact with OpenSK authenticators.

To install it, you need to execute the following commands:

```shell
sudo cp rules.d/55-opensk.rules /etc/udev/rules.d/
sudo udevadm control --reload
```

Then, you will need to unplug and replug the key for the rule to trigger.


## Successfull installation

The name of the product changes from Feitian to OpenSK
```shell
$ dmesg 
[ 1526.074997] usb 2-2.2: Product: OpenSK
```

## Reset submodules and git repository
To reset the local directory on your computer, execute the script `reset.sh`.
Access the OpenSK directory. 
```shell
$ ./reset.sh 
```

## Remove pin code and private keys
```shell
$ ./deploy.py --board=nrf52840_dongle_dfu --opensk --programmer=nordicdfu --clear-storage
```
## Troubleshooting

To test whether the installation was successful, visit a
[demo website](https://webauthn.io/), [demo website 2](https://webauthn.me/)  and try to register and login.


### Linux

If you have issues with the demo website, the following commands should help you
understand whether OpenSK was installed properly.

When plugging in the USB key, the following line should appear in `lsusb`.

```shell
$ lsusb
...
Bus XXX Device YYY: ID 1915:521f Nordic Semiconductor ASA OpenSK
```

You should also see lines similar to the following in `dmesg`.

```shell
$ dmesg
...
[XXX] usb A-BB: new full-speed USB device number 00 using xhci_hcd
[XXX] usb A-BB: New USB device found, idVendor=1915, idProduct=521f, bcdDevice= 0.01
[XXX] usb A-BB: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[XXX] usb A-BB: Product: OpenSK
[XXX] usb A-BB: Manufacturer: Nordic Semiconductor ASA
[XXX] usb A-BB: SerialNumber: v0.1
[XXX] hid-generic 0000:0000:0000.0000: hiddev0,hidraw0: USB HID v1.10 Device [Nordic Semiconductor ASA OpenSK] on usb-0000:00:00.0-00/input0
```

When plugging in the USB key in bootloader mode (DFU mode), the following line should appear in `dmesg`.
This USB has an open DFU. 

```shell
$ dmesg
...
[ 1588.909452] usb 2-2.2: Product: Open DFU Bootloader
```


### LED status

Led Behavior | Mode Type | Trigger Method 
--- | --- | --- 
Blue Led is breathing | Bootloader mode | Push the button while USB key is plugged for 5 seconds
Blue Led is flashing | Bootloader mode | Flashing the firmware
No led on | Working mode | Idle
RGB led | Working mode | Wait for the user presence when receiving FIDO command
