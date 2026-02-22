# CSSE4011 Student Workspace

This repository acts as the workspace for studentss developing application
throughout CSSE4011. It uses the [West T2 topology][zephyr-west-t2] and pulls
in the [CSSE4011 Zephyr SDK][csse4011-sdk].

## Setup
For full setup instructions, see the CSSE4011 Workspace Setup guide on Ed
Discussion.

## Usage
This repo, available locally as `firmware` when set up using `west`, will be
your working directory as you develop code throughout the semester.

The repo contains stub folders for each prac (prac0 through prac5). Each
is pre-configured as a blinky application that you can build immediately to
verify your setup.

Important: You must develop your code for each prac inside its corresponding
`pracX` folder. Do not rename, move, or restructure these folders. The teaching
staff use automated testing that expects this exact layout -- if your code is
not in the correct folder, it may not be assessed.

Assuming you've got the a working Zephyr installation, you can build the test
blinky application, available within `prac0`.

```sh
(.venv) sam@raskolnikov:~/csse4011 (main)
$ cd firmware
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ west build -p -b nrf52840dk/nrf52840 -d prac0/build prac0
...
[144/144] Linking C executable zephyr/zephyr.elf
Memory region         Used Size  Region Size  %age Used
           FLASH:       18652 B         1 MB      1.78%
             RAM:        4480 B       256 KB      1.71%
        IDT_LIST:          0 GB        32 KB      0.00%
Generating files from /home/sam/csse4011/firmware/prac0/build/zephyr/zephyr.elf for board: nrf52840dk
```

Note that we are using `-p` for pristine and `-b` to select our board. I
strongly recommend using `pristine` builds to avoid a family of troublesome
errors. Additonally we use the `-d` switch to specify our build directory.
I strongly recommend using this to keep your prac build artifacts adjacent to
the prac source. You'll find yourself regularly digging through build artifacts
to debug devicetree and Kconfig errors.

You can flash the application to the board as follows:

```sh
west flash -d prac0/build
```

Again we use the `-d` switch to specify the location of our build artifacts;
when using a mono-repo for multiple applications, you can end up flashing the
wrong application if you're not careful.

When working on subsequent pracs, just build from the corresponding folder:

```sh
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ west build -p -b nrf52840dk/nrf52840 -d prac1/build prac1
```

## Development

This section describes some additional tools that may be useful during
development.

### Just

If you want to save yourself some effort when repeatedly running commands,
I suggest using either `make` or `just` to write some simple recipes. I find
`just` more forgiving.

A simple `Justfile` is included in this repo and contains a parameterised
recipe for building arbitrary targets (assuming you follow the flat project
structure described above). Assuming you've installed `just`, you can run:

```
just build prac0
```

You can configure the `default_board` in the `Justfile`.

### Clangd

Clangd is a language server for C/C++. It gives your editor the ability to do
things like jump to definitions (Ctrl+Click), show type info on hover, and
provide autocomplete. This makes it much easier to navigate through code,
especially Zephyr’s source, and understand how things are connected.

The .clangd file in this repo is set up to enable that for `prac0` through
`prac5`. Note that this requires the build directory be located at
`pracX/build`; clangd will fail to find the compilation database otherwise.

If you want to get the same functionality for other targets (like the project),
copy one of the existing blocks in .clangd and update the paths and match
patterns.

For example:

```
If:
  PathMatch: ^prac0.*

CompileFlags:
  CompilationDatabase: ./prac0/build/
---
If:
  PathMatch: ^project.*

CompileFlags:
  CompilationDatabase: ./project/build/
```

You need to include the `---` to separate `If` blocks.

I won't go into detail on how this works. Nor will I explain how to install
clangd. You should do your own research.

Clangd can also be used to automatically format your code using `clang-format`.
This repository contains a simple `.clang-format` file this purpose. The
provided file adapts Zephyr's `.clang-format` file to use spaces instead of
tabs with an indent-width of four.

If you're using VSCode or similar, I suggest enabling `formatOnSave` or the
equivalent feature in the editor of your choice so that your code is always
formatted nicely. If you've never used automated formatting it will rock your
world.


[zephyr-west-t2]:https://docs.zephyrproject.org/latest/develop/west/workspaces.html#west-t2
[csse4011-sdk]:https://github.com/skwort/csse4011-sdk
