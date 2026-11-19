# Running the 6502 Construction Set emulator on a Raspberry Pi

This guide puts the emulator from *6502 Construction Set* (Book 1) on a
Raspberry Pi. It covers the code in the book's [reference
implementation](https://github.com/Silicon-Arcade/reference_implementation),
or your own version of it if you have been building along:

| Program | What it is | Book |
|---|---|---|
| `chip6502_core` | Chapter 4's scaffold: prints a single `H` and stops | Chapter 4 |
| `chip6502_core_app` | The 6502 core in a desktop window, running a small monitor program | Build 29 |
| `chip6502_harness` | A terminal program that runs a raw 6502 image, used to play Microchess | Build 30 |

Part 2 also shows how to run the companion
[debugger](https://github.com/Silicon-Arcade/debugger) on the Pi.

> **Looking for the BBC Micro?** The emulator of a machine compatible
> with the BBC Model B is the subject of Book 2, *BBC Micro Expansion
> Set*, coming soon. It will have its own Raspberry Pi guide. Nothing in
> this guide depends on it: everything here uses Book 1's code only.

## Which approach suits your Pi

There are two ways to get the programs onto a Pi, and the right one
depends mostly on how big your Pi is:

- **Approach A, build on the Pi**, turns the Pi into your development
  machine: Rust, VS Code and the source code all live on it. Compiling
  the window library takes memory and processor time, so this approach
  is for the larger models: a **Pi 5, Pi 500, Pi 4 or Pi 400 with at
  least 4 GB** of memory.
- **Approach B, build on a PC and copy to the Pi**, does the heavy work
  on a PC. The Pi only runs the finished programs, which need tens of
  megabytes of disk and little memory. This approach suits the
  **smaller models, such as a Pi 3 or a Zero 2 W**, and any Pi you
  would rather not fill with a development setup. Instructions are
  provided for **both Windows and Linux PCs**.

| | Approach A: build on the Pi | Approach B: build on a PC, copy to the Pi |
|---|---|---|
| Suits | Pi 5, Pi 500, Pi 4 and Pi 400 with 4 GB or more | Any 64-bit Pi, including the Pi 3 and Zero 2 W |
| Build machine | The Pi itself | A Windows or Linux PC |
| What lands on the Pi | Rust, VS Code, the source code, build output | One folder holding the programs |
| Disk used on the Pi (approx.) | A few gigabytes | Tens of megabytes |
| Time to first run | Up to half an hour on a Pi 5, most of it the first compile; longer on a Pi 4 | Minutes on the Pi, after the build on the PC |
| You can change the code on the Pi | Yes | No: you change it on the PC and copy it again |
| Best for | Following the book on the Pi itself | Smaller Pis, or keeping your development setup on your PC |

Every Pi in this guide runs the **64-bit** Raspberry Pi OS. The
original Pi 1, Pi 2 and Pi Zero (and Zero W) can only run the 32-bit
system, so this guide does not cover them. On the smaller models, the
terminal programs (`chip6502_core` and `chip6502_harness`) are the
quickest check that everything works; then try the monitor's window.

Both approaches start with **Part 1**. Then follow **Part 2** to build on
the Pi, or **Part 3** to build on a Windows or Linux PC and copy the
result across.

---

## Part 1 — Prepare the Pi

You need:

- A Raspberry Pi that runs the 64-bit Raspberry Pi OS (see
  [Which approach suits your Pi](#which-approach-suits-your-pi)).
- A microSD card of 16 GB or more (32 GB if you choose Approach A).
- The official power supply for your model, or one of equal rating.
  An underpowered Pi slows down, or restarts during a long compile.
- A monitor, keyboard and mouse.

### 1.1 Write the operating system

1. On another computer, install **Raspberry Pi Imager** from
   <https://www.raspberrypi.com/software/>.
2. Choose your Pi model as the device.
3. Choose OS **Raspberry Pi OS (64-bit)**, the version *with desktop*.
   Do not choose *Lite*. The monitor and the debugger open a window,
   and need the desktop.
4. Choose your microSD card.
5. When Imager offers to apply OS customization, set a username and
   password, your Wi-Fi network and your locale. Turn on SSH if you
   want to copy files over the network (Approach B uses it).
6. Write the card, put it in the Pi and power on.

### 1.2 Bring the system up to date

When the desktop appears, open a **Terminal** (the black icon in the
top bar) and run:

```sh
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

### 1.3 Confirm you are on 64-bit

After the reboot, open a terminal again:

```sh
uname -m
```

The answer must be `aarch64`. If it says `armv7l`, the 32-bit OS was
written. This guide is for 64-bit only, so go back to step 1.1.

---

## Part 2 — Approach A: build on the Pi

This approach puts a full development setup on the Pi: the same tools
the book uses on a PC. It is the way to go if you want to work through
the book on the Pi, or change the emulator there and see the result.

### 2.1 Install the build tools

```sh
sudo apt install -y git build-essential
```

- `git` fetches the source code.
- `build-essential` provides the C linker, which Rust uses to produce
  the final program.

Nothing else is needed. The window library loads the Pi's graphics
and windowing libraries when the program starts, and Raspberry Pi OS
with desktop already has them.

### 2.2 Install Rust

Rust is installed with `rustup`, which manages compiler versions:

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Accept the default installation (press `Enter` at the prompt). Then
load Rust into the current terminal and check the version:

```sh
source "$HOME/.cargo/env"
rustc --version
```

The version must be **1.85 or newer**. That is the minimum for the
Rust 2024 edition the code uses.

### 2.3 Install VS Code

VS Code is in Raspberry Pi OS's own package list:

```sh
sudo apt install -y code
```

Then add the Rust language extension, which gives you code
completion, inline errors and go-to-definition:

```sh
code --install-extension rust-lang.rust-analyzer
```

### 2.4 Get the source

```sh
mkdir -p ~/Code
cd ~/Code
git clone https://github.com/Silicon-Arcade/reference_implementation.git
code reference_implementation
```

If you have been building your own emulator and keep it in a git
repository, clone that instead. The commands below work the same way
in any workspace laid out as the book describes.

VS Code opens the project. If it asks whether you trust the authors
of the folder, choose **Yes**. Otherwise rust-analyzer will not run.

rust-analyzer now indexes the project in the background. You can see
its progress in the bottom status bar. On a 4 GB Pi, let it finish
before starting your first build, so the two do not compete for
memory.

### 2.5 Build and run

Open VS Code's terminal (**Terminal → New Terminal**). Start with
Chapter 4's scaffold, which needs no window:

```sh
cargo run --release -p chip6502_core
```

It prints a single `H`, which the emulated processor stored to its
console port. Then the monitor:

```sh
cargo run --release -p chip6502_core_app
```

The first build downloads the window library and its dependencies and
compiles them. On a Pi 5 this takes several minutes, and longer on a
Pi 4. Later builds only
recompile what changed and are much faster.

A window titled **6502 Processor** opens. Click **Run / Pause** and
the monitor prints its `MON v1` banner. Type `?` for help; `Q` ends the
program.

**Use `--release` on the Pi.** Without it you get a debug build, which
compiles faster but runs unoptimized. The book's programs are small
enough that a debug build copes, but the conformance suites and
Microchess are much slower without `--release`.

If the build fails with the compiler being killed, the Pi ran out of
memory. Limit the number of parallel compile jobs and try again:

```sh
cargo build --release -j 2
```

### 2.6 Run the tests

```sh
cargo test --release
```

All the tests should pass, with six ignored. The ignored six need
files the repository cannot ship: Klaus Dormann's conformance suites
and a Microchess image. Build 26 explains how to download the suites
and point the tests at them, and that works the same on the Pi.

### 2.7 Optional: play Microchess

Build 30 runs Peter Jennings's Microchess on the emulator, in a
terminal. The assembler it uses, 64tass, is in Raspberry Pi OS's
package list:

```sh
sudo apt install -y 64tass
```

Fetch `uchess.asm` and make the changes Build 30 lists (the reference
implementation's README summarizes where to get it). Then assemble
and play:

```sh
64tass --nostart -o microchess.bin uchess.asm
cargo run --release -p chip6502_harness -- microchess.bin
```

Type `C` and Enter to set up the board, `P` for the program to move,
and moves as four digits (from-square, to-square).

Microchess is Peter Jennings's program, and the repository's
`.gitignore` already keeps your copy and the assembled image out of
git. Keep them out of anything you share.

### 2.8 Optional: the debugger

The [debugger](https://github.com/Silicon-Arcade/debugger) steps
through a 6502 program one instruction at a time. It finds the core by
a relative path, so clone it next to the reference implementation:

```sh
cd ~/Code
git clone https://github.com/Silicon-Arcade/debugger.git
cd debugger
cargo run --release
```

A window titled **chip6502 debugger** opens with a small program
loaded. Press **Run** and `0123456789` appears in the console panel.
The debugger's README explains the rest, including how to point it at
your own core.

### 2.9 Stay up to date

```sh
cd ~/Code/reference_implementation
git pull
cargo build --release
```

### 2.10 Reclaim space

Build output collects in the `target` folder and can grow to a few
gigabytes. To clear it (the next build starts from scratch):

```sh
cargo clean
```

---

## Part 3 — Approach B: build on a PC, copy to the Pi

In this approach the Pi only runs the programs. You build them on a
Windows or Linux PC, put them in one folder, and copy that folder
across.

### 3.1 Why a PC build will not run on the Pi

The output of `cargo build --release` is a single executable file per
program. Rust links its own libraries into that file, so the Pi does
not need Rust installed to run it. Two things stop you from copying a
PC's build straight across, though:

1. **Processor.** Every Pi this guide covers has a 64-bit ARM CPU. A
   program compiled for a typical PC (x86-64) will not run on it. The
   compiler must be told to produce ARM code for Linux, a *target*
   called `aarch64-unknown-linux-gnu`. Building for a machine other
   than the one you are on is called *cross-compiling*.
2. **Operating system.** A Windows `.exe` expects Windows. The Pi needs
   a Linux program, which expects the Pi to provide the C library
   (*glibc*) and, for the window, the graphics and windowing
   libraries. Raspberry Pi OS with desktop already has all of them.

So the build has to happen in Linux, for ARM. A Linux PC already has
Linux. On Windows, Linux comes from WSL, the Windows Subsystem for
Linux.

### 3.2 Choose where to build

| Build machine | What you need | Notes |
|---|---|---|
| **Windows PC with an Intel or AMD processor** | WSL 2, Docker Desktop, `cross` | Cross-compiles: the PC produces ARM code it cannot run itself |
| **Windows PC with an ARM processor** (Snapdragon, for example) | WSL 2 | No cross-compiling: WSL on an ARM PC is already 64-bit ARM Linux, the same kind of system as the Pi |
| **Linux PC with an Intel or AMD processor** | Docker Engine (or Podman), `cross` | Cross-compiles, as on Windows, without WSL |
| **Another Pi, or any 64-bit ARM Linux** | Part 2's setup | No cross-compiling at all. Build as in Part 2, then go to step 3.7 using `target/release` |

Setup is the one step that differs between Windows and Linux: Windows
PCs follow 3.3a, Linux PCs follow 3.3b. From there the steps are the
same on both: Intel and AMD PCs follow 3.4, ARM machines follow 3.5.
On Windows, every command after WSL is installed runs in WSL's Ubuntu
terminal; on Linux, in an ordinary terminal.

### 3.3a Windows: set up WSL (once)

1. **Install WSL 2 with Ubuntu 24.04.** In an administrator PowerShell:

   ```powershell
   wsl --install -d Ubuntu-24.04
   ```

   Restart when asked, then open **Ubuntu 24.04** from the Start menu
   and create a Linux username. All later commands in Part 3 run in
   this Ubuntu terminal, not in PowerShell, until step 3.8 moves to the
   Pi.

   Working inside Linux matters for one detail in particular. Linux
   marks programs as *executable* with a file permission that Windows
   folders do not store. If the folder for the Pi is assembled on a
   Windows drive, the Pi receives programs it refuses to run.

   The Ubuntu version is named deliberately; step 3.5 explains why.

2. **Find out which kind of processor you have:**

   ```sh
   uname -m
   ```

   `x86_64` means Intel or AMD: you will follow 3.4. `aarch64` means
   ARM: you will follow 3.5.

3. **Install Rust inside Ubuntu** (WSL is a separate machine from
   Windows' point of view, so it needs its own copy):

   ```sh
   sudo apt update && sudo apt install -y build-essential git
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source "$HOME/.cargo/env"
   ```

   Then check that Ubuntu is using its *own* copy:

   ```sh
   which cargo            # must print /home/<you>/.cargo/bin/cargo
   rustup show | head -3  # host must match step 2: x86_64-… or aarch64-…
   ```

   WSL adds the Windows `PATH` to Ubuntu's. If Rust is also installed
   on Windows and the Ubuntu copy is missing, the Windows `cargo.exe`
   runs instead, and builds fail in confusing ways.

4. **Clone the repository into the Linux file system**, not under
   `/mnt/c`. Builds there are much faster and the permission problem
   above does not arise:

   ```sh
   mkdir -p ~/code && cd ~/code
   git clone https://github.com/Silicon-Arcade/reference_implementation.git
   cd reference_implementation
   ```

   To build your own emulator instead, clone your repository here.

Now go to 3.4 or 3.5.

### 3.3b Linux: install Rust and get the source (once)

These commands are for Debian, Ubuntu and their relatives. On other
distributions, install a C compiler and git with your own package
manager in step 1; the rest is the same.

1. **Install the build tools.** Rust uses the C compiler as its
   linker:

   ```sh
   sudo apt update && sudo apt install -y build-essential git
   ```

2. **Find out which kind of processor you have:**

   ```sh
   uname -m
   ```

   `x86_64` means Intel or AMD: you will follow 3.4. `aarch64` means
   ARM: you will follow 3.5.

3. **Install Rust**, if you have not already:

   ```sh
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   source "$HOME/.cargo/env"
   rustc --version        # must be 1.85 or newer
   ```

   If Rust came from your distribution's package manager instead, check
   the version: some distributions ship one older than 1.85. `cross`
   (step 3.4) also expects `rustup`.

4. **Clone the repository:**

   ```sh
   mkdir -p ~/code && cd ~/code
   git clone https://github.com/Silicon-Arcade/reference_implementation.git
   cd reference_implementation
   ```

   To build your own emulator instead, use your own repository.

### 3.4 Build on an Intel or AMD PC: cross-compile with `cross`

1. **Install a container engine** for `cross` to run its build
   container in.

   - **Windows:** install Docker Desktop from <https://www.docker.com/>
     and, in its settings, enable **Use the WSL 2 based engine** and
     turn on integration with your Ubuntu distribution.
   - **Linux:** install Docker Engine by following
     <https://docs.docker.com/engine/install/> for your distribution,
     then let your user run it without `sudo` (log out and back in
     afterwards):

     ```sh
     sudo usermod -aG docker "$USER"
     ```

     Podman works too, if you already use it: `cross` finds it when
     Docker is not installed.

   Check it from your Linux terminal (WSL's Ubuntu on Windows):

   ```sh
   docker run --rm hello-world
   ```

2. **Install `cross`.** `cross` runs the compiler inside a Docker
   container that already has an ARM linker and an ARM C library.
   That spares you installing and configuring those by hand:

   ```sh
   cargo install cross --locked --version 0.2.5
   which cross   # must print /home/<you>/.cargo/bin/cross
   ```

   (`cross` is dual-licensed MIT / Apache-2.0.)

   `cross` is for x86-64 PCs only. On an ARM PC it fails with
   *toolchain 'stable-x86_64-unknown-linux-gnu' may not be able to run
   on this system*, because its containers are x86-64 programs. If you
   see that message, you are on an ARM PC: follow 3.5 instead.

3. **Cross-compile** from the workspace root:

   ```sh
   cross build --release --target aarch64-unknown-linux-gnu \
       -p chip6502_core_app -p chip6502_harness
   ```

   The executables appear in
   `target/aarch64-unknown-linux-gnu/release/`. That is the build
   folder for step 3.7.

### 3.5 Build on an ARM PC: an ordinary build

On an ARM machine, your Linux (WSL's Ubuntu on Windows) and the Pi's
Raspberry Pi OS are both 64-bit ARM Linux. A normal `cargo build` already produces programs
the Pi can run, so there is no Docker, no `cross` and no `--target`.

1. **Build** from the workspace root:

   ```sh
   cargo build --release -p chip6502_core_app -p chip6502_harness
   ```

   The executables appear in `target/release/`. That is the build
   folder for step 3.7.

2. **Check the C library version.** A Linux program needs a C library
   (*glibc*) at least as new as the one it was built against. `cross`
   avoids the issue by building against a deliberately old one. A
   native build uses whatever your Linux has. Compare the two
   machines:

   ```sh
   ldd --version | head -1    # run on the PC, then again on the Pi
   ```

   The Pi's number must be the same or higher. Ubuntu 24.04 has glibc
   2.39 and the current Raspberry Pi OS has 2.41, so that pairing
   works, which is why step 3.3a names Ubuntu 24.04. A newer Linux on
   the PC, or the older *Legacy* Raspberry Pi OS, can put the PC ahead
   of the Pi. If so, the Pi refuses to start the program with an error
   mentioning `GLIBC_2.xx not found`.

### 3.6 Check the result

Make sure the executables really are ARM programs. Run the line that
matches your build folder:

```sh
file target/aarch64-unknown-linux-gnu/release/chip6502_core_app   # Intel/AMD PC (3.4)
file target/release/chip6502_core_app                             # ARM machine (3.5)
```

The answer should include `ARM aarch64`. If it says `x86-64`, an
Intel or AMD build missed the `--target` flag.

### 3.7 Bundle the programs

Gather the two programs into one folder and pack it into a single
file. Set `BUILD` to your build folder from 3.4 or 3.5:

```sh
BUILD=target/aarch64-unknown-linux-gnu/release   # Intel/AMD PC (3.4)
# BUILD=target/release                           # ARM PC (3.5)

rm -rf ~/pi-bundle && mkdir -p ~/pi-bundle/chip6502
install -m 755 "$BUILD/chip6502_core_app" "$BUILD/chip6502_harness" ~/pi-bundle/chip6502/
tar -czf ~/chip6502-pi.tar.gz -C ~/pi-bundle chip6502
tar -tvzf ~/chip6502-pi.tar.gz
```

How it works:

- `install -m 755` copies a file and makes it executable. This is the
  step that lets the Pi run the programs.
- `tar -czf` creates (`c`) a gzip-compressed (`z`) archive file (`f`).
  `-C ~/pi-bundle chip6502` means "go into `~/pi-bundle` and archive
  `chip6502` from there", so the archive unpacks as one `chip6502`
  folder. Unlike a plain copy, the archive carries the executable
  permission with it.
- `tar -tvzf` lists what is in the archive. Both programs should start
  `-rwxr-xr-x`; the `x`s mean executable.

If you have assembled `microchess.bin` (step 2.7 explains how; the
same `64tass` command works on the PC after `sudo apt install 64tass`),
copy it into `~/pi-bundle/chip6502/` before the `tar -czf` line. It is
for your own Pi only: don't share a bundle that contains it.

### 3.8 Copy to the Pi and run

Copy the archive across over the network. This needs SSH turned on on
the Pi: either in Raspberry Pi Imager (step 1.1), or afterwards in the
Pi's **Control Centre → Interfaces → SSH**. From your Linux terminal
(WSL's Ubuntu on Windows):

```sh
scp ~/chip6502-pi.tar.gz <user>@<pi-name>.local:~/Downloads/
```

Replace `<user>` with your username on the Pi and `<pi-name>` with its
hostname (`raspberrypi` unless you changed it in Imager). `scp` copies
files over SSH and asks for the Pi's password the first time. A USB
stick works too: copy the file onto it (on Windows, WSL's home folder
is visible in File Explorer under **Linux**), then plug it into the Pi.

On the Pi, open a terminal and unpack:

```sh
mkdir -p ~/Apps
tar -xzf ~/Downloads/chip6502-pi.tar.gz -C ~/Apps
cd ~/Apps/chip6502
./chip6502_core_app
```

The **6502 Processor** window opens; click **Run / Pause** to start the
monitor. If you copied `microchess.bin` across too:

```sh
./chip6502_harness microchess.bin
```

To update, build again on the PC, repeat 3.7 and 3.8, and unpack over
the old folder.

### 3.9 Optional: add the monitor to the desktop menu

A menu entry is a small text file that names the program to run, by
its full path. This writes one on the Pi:

```sh
mkdir -p ~/.local/share/applications
cat > ~/.local/share/applications/chip6502-monitor.desktop <<EOF
[Desktop Entry]
Type=Application
Name=6502 Monitor
Exec=$HOME/Apps/chip6502/chip6502_core_app
Path=$HOME/Apps/chip6502
Terminal=false
Categories=Game;Emulator;
EOF
```

The monitor now appears in the main menu under **Games**. `$HOME` is
filled in as the file is written, so the entry holds your real path.
If you move the folder, run the command again. To remove the entry,
delete the file:

```sh
rm -f ~/.local/share/applications/chip6502-monitor.desktop
```

The same entry works for a program built on the Pi in Part 2: point
`Exec` and `Path` at `~/Code/reference_implementation/target/release`
instead.
