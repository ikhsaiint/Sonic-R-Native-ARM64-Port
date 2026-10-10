**Reddit Title: Sonic R Native ARM64 Port for PortMaster | Testing Build**

Hello everyone!

I'd like to share a native ARM64 port of Sonic R for PortMaster, currently available for compatibility testing.

The port uses a portable C reimplementation of the original Sonic R PC engine by [jnmartin84](https://github.com/jnmartin84/sonic-r). It includes an AArch64 Linux executable and GL4ES graphics compatibility libraries.

### Technical Information

* **Platform:** PortMaster
* **Architecture:** AArch64 (ARM64)
* **Minimum GLIBC:** 2.27
* **Graphics:** GL4ES
* **Engine:** Portable C reimplementation
* **Game Data:** Additional original game data required
* **Testing Status:** Awaiting physical device testing

The GLIBC requirement is based on inspecting the executable and bundled GL4ES libraries. Compatibility with individual custom firmware distributions and devices has not yet been confirmed.

### Installation and Game Data

You must provide the necessary data from your own legally obtained Sonic R PC installation.

After installing the port:

1. Open `ports/sonicr/gamedata/`.
2. Copy the required game folders into this directory.
3. Preserve the original uppercase folder and file names.
4. Make sure `gamedata/GENERAL/SONICR.BIT` exists before launching.

**Important:** The current ZIP does not contain the required `GENERAL/SONICR.BIT` file. The launcher will display an error and exit until the required game data is supplied.

### Background Music

Background music is optional and is not included.

To enable music, create the following directory:

`ports/sonicr/gamedata/MUSIC/`

Add tracks named `track2` through `track21`, using lowercase filenames.

Supported formats:

* `.adx`
* `.son`
* `.ogg`
* `.mp3`
* `.flac`
* `.wav`

ADX tracks can be obtained through the Sonic R Updater music mod. Alternatively, you can use your own CD rip. The README documents how to convert WAV tracks into the required raw PCM `.son` format.

Use one format per track. If both `.son` and `.adx` versions exist, `.son` takes priority.

Without background music files, the game is intended to run with sound effects only.

### Controls

| Button  | Action                       |
| ------- | ---------------------------- |
| D-Pad   | Steering and menu navigation |
| A       | Jump                         |
| B       | Accelerate                   |
| X       | Accelerate                   |
| Y       | Cycle camera                 |
| L1 / R1 | Drift                        |
| L2 / R2 | Drift                        |
| Start   | Pause / Confirm              |

Controls can also be rebound under Options > Controls.

### Compatibility Testing

I'm looking for feedback on the following:

* Successful launch and game-data loading.
* Graphics rendering and performance.
* Audio playback and sound effects.
* Controller input and menu navigation.
* Screen resolution compatibility.
* Returning to the frontend after exiting the game.
* Compatibility with different GLIBC versions and custom firmware distributions.

Please include your device model, CFW name and version, tested resolution, and the contents of `ports/sonicr/log.txt` if you encounter an error.

**Current status:** The package and its binary requirements have undergone static inspection, but the game has not yet been verified on physical PortMaster hardware.

This is a testing build, so feedback and bug reports are welcome.

Thanks to jnmartin84 for the original portable C reimplementation, and thanks to everyone willing to help with compatibility testing!
