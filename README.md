# pbots_calc

# Texas Hold'em Equity Calculator for hand ranges

## Introduction

As simple as that
```powershell
PS D:\pbots_calc> .\example\calculator.bat 4qo:jts+

After 1000000 iterations of Monte Carlo Simulation, EV:
4qo: 0.361006
jts+: 0.638994
```

pbots_calc is a ranged Equity Calculator. Originally developed for poker bots in the MIT Pokerbots Competition! (mitpokerbots.com)

pbots_calc is a library for calculating the showdown equity for regular texas hold'em hands, with limited support for pineapple variants. It supports common 2-card hand range syntax (see documentation for full details), and handles any number of players. It's built in C, using the [poker-eval](http://pokersource.sourceforge.net/) library for fast hand evaluation, and provides wrappers for python and java. It has reasonable speed, but there are certainly many areas for optimization and improvements.

The pbots_calc library is Free Software and is released under the terms of the GNU General Public License. See the `COPYING` file in the root directory of the distribution.

## Installation

**Make sure python, pbots_calc and poker-eval all are either 32 or 64 bit.**
Tensorflow, for example, only has x64 version on windows + anaconda.

Linux and mac instructions have not been tested after updating this document to x64 and VS Community 2019 from VC++ Express 2010. Check git history in case of issues.

### Linux Installation

First, make sure that you have gcc, python and scons installed. Installation
should be as simple as `sudo apt-get install gcc python scons`

Now you'll need to download, patch, and install the [poker-eval](http://pokersource.sourceforge.net/) library:

1. Download the [poker-eval source code](http://deb.debian.org/debian/pool/main/p/poker-eval/poker-eval_138.0.orig.tar.gz) and extract it.

   a. You need to patch the source code: You can find the patch in this directory, called `poker-eval_patch.diff`. If you're in the poker-eval directory, you can just `patch -p0 < path/to/poker-eval_patch.diff`. This will fix an `#include` typo and remove a bunch of unneeded folders from the build path.

2. If not already installed, you'll need to grab the autoconf and libtool packages: `sudo apt-get install autoconf libtool`

3. Proceed according to the instructions in poker-eval/README. (summarized below for reference.

   a. `autoreconf --install`

   b. `./configure`

   c. `make`

   d. (assuming no errors) `sudo make install`

Finally, to compile the pbots_calc library, just run `scons` from the pbots_calc directory. This will compile the pbots_calc library, the c example code, and the java example code. It will also create calculator.sh scripts for each of the examples (more below).

You will be left with the compiled library in the pbots_calc directory, under `export/*your OS type*/` `lib` and `include` folders.

---
### Mac OSX Installation

1. Follow step 1 from the Linux installation.

2. Continue with

   * Install [homebrew](http://mxcl.github.com/homebrew/).

   * If not already installed, you need to grab the autoconf and libtool
      packages: `brew install autoconf libtool`

3. Follow step 3 and onwards from the Linux installation

You will be left with the compiled library in the pbots_calc directory, under `export/*your OS type*/` `lib` and `include` folders.

---
### Windows Installation

First let's configure local environment.

1. Create a folder on your hard drive that will hold the compiled libraries or use another existing folder. I used `D:\lib`. And you need to add this folder to your [PATH](https://www.computerhope.com/issues/ch000549.htm).

2. Create a `config.json` file in the root folder of repo, with following content (replace the library folder if you prefer). Notice, you need to escape `\` there as `\\`:
```json
{
    "lib_path": "D:\\lib"
}
```

Software you will need to have the following installed:

1. `Python`: [Download](https://www.python.org/downloads/) and install, I prefer [miniconda](https://docs.conda.io/en/latest/miniconda.html) version, though (use only if you are familiar with conda). Remember to pick correct bitness.

2. `Visual Studio`: Needed to compile the projects. After installing, you will need to add path to `vcvars32.bat` (by default for VS 2019 ``C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build``) to your PATH. **Continue in a new terminal after that** to make sure PATH has updated in the terminal's environment.

3. `Scons` - `pip install SCons`

4. (optional) `JDK` - if you want to develop a Java app. Not tested, though.

Next, you'll need to download, modify, compile, and install the [poker-eval (homepage)](http://pokersource.sourceforge.net/) library:

1. Clone the `poker-eval` [repo](https://github.com/VinQbator/poker-eval) with `git clone https://github.com/VinQbator/poker-eval.git` in suitable location.

2. Use Visual Studio to open the ``poker-eval.sln`` found in the source.

   * Choose appropriate solution platform from dropdown x64/x86

   * Ensure you are set to build a "Release" version (and not "Debug") by selecting "Release" from the drop down menu next to the "run" button (should be right below the help menu).

   * Finally, right-click on the poker-eval project and select "build" - the output console should not report any failures!

3. Open a file explorer and navigate to the location of the poker-eval library you downloaded (and just built). *TODO: lib folder setup in poker-eval.*

   * Navigate to the "Release" directory and copy the file `poker-eval.lib` to your libraries folder you set up at first.

   * Also copy the all the contents from the `include` folder in the poker-eval library, including the `inline` folder, to a new folder called ``poker-eval`` in your libraries folder.

Now, you should be able to build and install the pbots_calc library:

1. Run `scons` from inside pbots_calc repo.

2. Copy `pbots_calc.dll` and `pbots_calc.lib` to your libraries folder. *TODO: improve the build scripts to put these in right place.*

## Running the Demos

There are 3 demo applications, showing how to use the library from C, Python, and Java. The demo is the same in all languages, and is just a command-line tool for running the library. Use standard hand range notation for specifying cards (see examples below for clues). In each folder, you should find a `calculator.sh` (or `calculator.bat` on Windows platforms). You can execute the demos using this script (you may need to set the script to be executable).

### Specifying Hands

Each hand can be represented by one or more hand ranges, specified in a comma-separated list. Multiple hands are separated by colons. Board cards are optional and must either be 0, 3, 4 or 5 cards. You can specify 0 or more dead (discarded) cards.

Sample 2-card hand ranges:

* 8sTd - single hand range
* 8sTd, 8sTc - 2 hand range
* 8Ts - 4 hand range: [8sTs, 8cTc, 8dTd, 8hTh]
* 8To = [8sTc, 8sTd, 8sTh, 8cTs, ...]
* 88 = [8s8c 8s8d 8s8h 8c8d 8c8h 8d8h]
* 8T = [8Ts, 8To]
* JJ+ = [JJ, QQ, KK, AA]
* 88-TT = [88, 99, TT]
* xx = random (all possible 2-card hands)

3-card hand ranges are heard to specify. Currently you can only enumerate all the hands you mean, or specify random:

* 8s9cTd,8s9cTh,8s9sTd,...
* xxx = random (all possible 3-card hands)

### Examples

* ``calculator.sh 4hqd:js9h askcqh`` will calculate the post-flop equities for a hand of 4 hearts and queen diamonds against the jack spades and 9 hearts, with a board of ace spades, king clubs, and queen hearts.
* ``calculator.sh 4qo:jts+`` will calculate pre-flop equities for the range of all 4-queen offsuit hands against all jack-ten or better suited hands.
* ``calculator.sh 6d8h:xxx 4c9d7ctskd 2c`` will calculate the post-river equity of a hand of 6 diamonds 8 hearts vs a random (unknown) 2-card hand with a full board and the 2 clubs discarded.
