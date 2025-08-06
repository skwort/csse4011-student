# CSSE4011 Student Workspace

This repository acts as the workspace for studentss developing application
throughout CSSE4011. It uses the [West T2 topology][zephyr-west-t2] and pulls
in the [CSSE4011 Zephyr SDK][csse4011-sdk].

## Setup

The first step is setup a local folder for development. Assuming you're using
either WSL, Linux or MacOS, create a root folder as follows:

```sh
sam@raskolnikov:~ 
$ mkdir csse4011
```

Enter the directory, then create and source a virtual environment.

```sh
sam@raskolnikov:~ 
$ mkdir csse4011
sam@raskolnikov:~ 
$ cd csse4011/
sam@raskolnikov:~/csse4011 
$ python -m venv .venv
sam@raskolnikov:~/csse4011 
$ source .venv/bin/activate
(.venv) sam@raskolnikov:~/csse4011 
$ 
```

If the `(.venv)` has been appended to your prompt as above, then you've probably
got this working.

The next step is to install `west`. `west` is Zephyr's meta-tool for managing
projects. It manages dependencies and wraps the building and flashing process,
among other things.

`west` is a Python application (hence the virtual environment). You'll find that
Python is a very common tool in the embedded space. Its high-level nature makes
building scripts and tooling easier.

To install west we use `pip`. The traditional way to do this is with `pip`
directly, but I've been preferring `uv` lately for its impressive *speed*. You
can research and install `uv` if you want to go fast.

```sh
$ source .venv/bin/activate
(.venv) sam@raskolnikov:~/csse4011 
$ pip install west
Collecting west
...
```

With `west` installed we can now initialise the *west workspace*.

```shell
(.venv) sam@raskolnikov:~/csse4011 
$ west init -m https://github.com/skwort/csse4011-student --mr main
=== Initializing in /home/sam/csse4011
--- Cloning manifest repository from https://github.com/skwort/csse4011-student, rev. main
Cloning into '/home/sam/csse4011/.west/manifest-tmp'...
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 1), reused 16 (delta 1), pack-reused 0 (from 0)
Receiving objects: 100% (16/16), done.
Resolving deltas: 100% (1/1), done.
--- setting manifest.path to firmware
=== Initialized. Now run "west update" inside /home/sam/csse4011.
```

This command will clone the repo and setup some basic west metadata. Let's poke
around a little bit.

```sh
(.venv) sam@raskolnikov:~/csse4011 
$ ls
firmware
```

Looking at that output, you'll notice that our repo has been clone to a folder
called `firmware`. This is deliberate. You will write all your firmware inside
this folder. More on that in a little bit.

To finalise the setup, we need to pull in all the dependencies.

```sh
(.venv) sam@raskolnikov:~/csse4011 
$ west update
...
```

This may take a few minutes. Listing the directory, we can see that `west` has
pull in quite a few dependencies.

```sh
(.venv) sam@raskolnikov:~/csse4011 
$ ls
csse4011-sdk  firmware  modules  tools  zephyr
```

Of particular note is the `csse4011-sdk` folder which contains some extra
examples for the specific boards we are using this semester.

Next, export the Zephyr CMake package:

```sh
(.venv) sam@raskolnikov:~/csse4011 
$ west zephyr-export
```

Then install Zephyr's python dependencies:

```sh
(.venv) sam@raskolnikov:~/csse4011 
$ west packages pip --install
```

Lastly, source the Zephyr environment:
```sh
(.venv) sam@raskolnikov:~/csse4011 
$ source zephyr/zephyr-env.sh 
```

Now that you have a workspace setup you can build a test application.

## Usage

This repo,  avaliable locally as `firmware` when setup using `west` as above,
will be your working directory as you develop code throughout the semester. 

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

When starting on new pracs, I suggest copying the prac0 folder and using
it as a template. The setup flow for a new application would look something like
this:

```sh
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ cp -r prac0/ prac1
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ ls
boards  CMakeLists.txt  Kconfig  prac0  prac1  west.yml  zephyr
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ west build
```

If you want to save yourself some effort when repeatedly running commands,
I suggest using either `make` or `just` to write some simple recipes, for example
`just build-prac0` could map to `west build -p -b board -d prac0/build prac0`.


[zephyr-west-t2]:https://docs.zephyrproject.org/latest/develop/west/workspaces.html#west-t2
[csse4011-sdk]:https://github.com/skwort/csse4011-sdk