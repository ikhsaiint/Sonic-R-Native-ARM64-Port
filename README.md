# Sonic R Native ARM64 Port for PortMaster

A native AArch64 port of a portable C reimplementation of Sonic R for PortMaster-compatible Linux handhelds.

The project uses the [sonic-r](https://github.com/jnmartin84/sonic-r) reimplementation by jnmartin84. It runs the original PC game's gameplay and tracks without requiring the original Windows executable.

**Project status:** Testing build. Physical device compatibility has not yet been verified.

## Technical Information

| Property               | Details                               |
| ---------------------- | ------------------------------------- |
| Game                   | Sonic R                               |
| Platform               | PortMaster                            |
| Architecture           | AArch64 (ARM64)                       |
| Engine                 | Portable C reimplementation           |
| Graphics               | GL4ES                                 |
| Declared minimum GLIBC | 2.27                                  |
| Game data              | Required from the original PC release |
| Background music       | Optional, supplied separately         |
| Ready to Run           | No                                    |
| Testing status         | Awaiting physical device testing      |

The GLIBC version listed above comes from the package metadata and static inspection. Compatibility with individual custom firmware distributions and hardware remains to be tested.

## Features

* Native ARM64 Linux executable.
* PortMaster launch script.
* GL4ES graphics compatibility libraries.
* SDL game controller support.
* Configurable controls through the game's Options menu.
* Optional background music support for multiple audio formats.
* Original game data loaded separately from the user's own installation.

## Requirements

You will need:

* A PortMaster-compatible device running ARM64 Linux.
* A compatible custom firmware distribution.
* The Sonic R PC game data from a legally obtained copy.
* A system environment providing the required runtime libraries.
* GLIBC 2.27 or a compatible newer version, subject to actual device compatibility.

The native executable is named `sonicr.aarch64`.

## Installation

### 1. Install the PortMaster Package

Install the `sonicr.zip` package using PortMaster.

After installation, the game directory should be available at:

```text
ports/sonicr/
```

### 2. Provide the Original Game Data

Copy the required folders from your own Sonic R PC installation into:

```text
ports/sonicr/gamedata/
```

Preserve the original uppercase directory and file names.

The following file is mandatory:

```text
ports/sonicr/gamedata/GENERAL/SONICR.BIT
```

The current package does not contain this required file. The game will not start until the required game data has been supplied.

Do not rename the original data directories or files unless the engine documentation explicitly permits it.

## Background Music

Background music is optional and is not included as a complete music collection.

To enable music, create:

```text
ports/sonicr/gamedata/MUSIC/
```

Add music files named `track2` through `track21`, using lowercase filenames.

### Supported Formats

| Extension | Description                                  |
| --------- | -------------------------------------------- |
| `.adx`    | ADX music from the Sonic R Updater music mod |
| `.son`    | Raw PCM audio                                |
| `.ogg`    | Ogg Vorbis audio                             |
| `.mp3`    | MP3 audio                                    |
| `.flac`   | FLAC audio                                   |
| `.wav`    | WAV audio                                    |

For `.son` files, the required format is signed 16-bit little-endian stereo PCM at 44,100 Hz.

One way to convert a WAV track into the required raw PCM format is:

```bash
ffmpeg -i trackN.wav -f s16le -ar 44100 -ac 2 trackN.son
```

Replace `N` with the track number.

Use one format per track. When both `.son` and `.adx` files are available for the same track, `.son` takes priority.

Without background music files, the game is intended to run with sound effects only.

## Controls

| Button | Action                       |
| ------ | ---------------------------- |
| D-Pad  | Steering and menu navigation |
| A      | Jump                         |
| B      | Accelerate                   |
| X      | Accelerate                   |
| Y      | Cycle camera                 |
| L1     | Drift                        |
| R1     | Drift                        |
| L2     | Drift                        |
| R2     | Drift                        |
| Start  | Pause and confirm            |

Controls can be reassigned in the game:

**Options > Controls**

The engine handles SDL game controller input natively. The supplied `sonicr.ini` intentionally contains no custom button mappings.

## Building from Source

The documented build procedure uses the PortMaster AArch64 builder environment, which supplies the SDL2 and SDL2_mixer development environment required by the project.

Refer to the official [PortMaster Docker documentation](https://portmaster.games/docker.html) for the builder environment.

### 1. Install Additional Dependencies

```bash
apt-get update

apt-get install -y --no-install-recommends \
  cmake \
  git \
  libdrm-dev \
  libegl-dev \
  libgbm-dev \
  libgl-dev \
  libgles2-mesa-dev \
  libminiupnpc-dev \
  pkg-config \
  zip
```

Do not replace the builder environment's patched SDL2 with an incompatible distribution-provided SDL2 package.

### 2. Build GL4ES

Clone the GL4ES source:

```bash
git clone --depth 1 \
  https://github.com/ptitSeb/gl4es.git \
  third_party/gl4es
```

Configure and build it:

```bash
cmake \
  -S third_party/gl4es \
  -B third_party/gl4es/build-portmaster \
  -DNOX11=ON \
  -DGLX_STUBS=ON \
  -DEGL_WRAPPER=ON \
  -DGBM=ON

cmake --build third_party/gl4es/build-portmaster \
  --parallel "$(nproc)"
```

### 3. Build the Sonic R Engine

Clone the engine repository:

```bash
git clone https://github.com/jnmartin84/sonic-r.git

cd sonic-r
```

Build the PortMaster release:

```bash
make -C source/sdl PORTMASTER=1 release-clean

make -C source/sdl \
  PORTMASTER=1 \
  release \
  --jobs="$(nproc)"
```

The expected native executable is:

```text
source/sdl/build-portmaster/sonicr.aarch64
```

A successful compilation does not, by itself, establish compatibility with every PortMaster device. The binary must still be checked against the target environment's runtime dependencies.

## Compatibility Testing

This project is seeking testing feedback from owners of PortMaster-compatible handhelds.

Please report the following information when submitting a bug report:

* Device model.
* Custom firmware name and version.
* Screen resolution.
* Whether the game launches successfully.
* Graphics and performance observations.
* Sound effects and music playback.
* Controller input and menu behavior.
* Whether the game exits cleanly to the frontend.
* Relevant output from `ports/sonicr/log.txt`.

### Testing Checklist

| Test                         | Status           |
| ---------------------------- | ---------------- |
| ARM64 executable inspection  | Completed        |
| Static package inspection    | Completed        |
| GLIBC requirement inspection | Completed        |
| ArkOS                        | Completed |
| AmberELEC                    | Not yet verified |
| ROCKNIX                      | Not yet verified |
| muOS                         | Not yet verified |
| Knulli                       | Not yet verified |
| 640x480 resolution           | Completed |
| Audio playback               | Not yet verified |
| Controller input             | Not yet verified |
| Full gameplay                | Not yet verified |

These statuses describe the current testing record, not guaranteed compatibility.

## Troubleshooting

### Missing Game Data

If the game reports that Sonic R data is missing, verify that this file exists:

```text
ports/sonicr/gamedata/GENERAL/SONICR.BIT
```

Check directory spelling and capitalization.

### Missing Background Music

Background music is optional. Verify that the files are located under:

```text
ports/sonicr/gamedata/MUSIC/
```

Check the lowercase track filenames and supported formats.

### Game Fails to Launch

Check:

```text
ports/sonicr/log.txt
```

Include the log, device model, firmware version, and tested resolution when reporting the issue.

A GLIBC or shared-library error may indicate that the executable or one of its dependencies is incompatible with the device's system environment.

## Licensing and Third-Party Components

The package includes separate license notices under:

```text
ports/sonicr/licenses/
```

Review the applicable notices before modifying or redistributing the software.

The Sonic R reimplementation's included license notice prohibits selling the code in any form. GL4ES has its own license notice.

Sonic R is a SEGA property. This project is a community port and is not an official SEGA release.

**Game data and redistribution:** The project requires data from the original PC game. Do not distribute proprietary game assets unless you have the necessary rights. The current testing archive contains WAV files under `sonicr/gamedata/SOUND/`; their redistribution rights must be confirmed before publishing that archive publicly.

## Credits

* **jnmartin84:** Portable C reimplementation of Sonic R.
* **ptitSeb:** GL4ES graphics compatibility layer.
* **PortMaster contributors:** Porting infrastructure and ARM64 build environment.

Thanks to everyone contributing compatibility reports and testing feedback.

## Disclaimer

This is an experimental testing build. It has not yet been verified on physical PortMaster hardware.

Architecture and minimum GLIBC metadata alone do not guarantee successful execution on every device.
