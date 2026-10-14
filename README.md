# Raspberry Pi Mastermind 🎮

**An embedded version of the classic Mastermind code-breaking game, built with C and ARM assembly for Raspberry Pi.**

The project brings a simple guessing game into the physical world: players enter a sequence using a push button, receive feedback through red and green LEDs, and see their results on a 16×2 character LCD. It is an exploration of low-level GPIO access, memory-mapped I/O, timing, and the interaction between software and electronic components.

## Highlights

- **Embedded programming:** C game logic with inline ARM assembly for low-level operations and sequence matching.
- **Physical input:** Push-button presses represent values in a guess.
- **Visual feedback:** Separate LEDs signal data and control events.
- **LCD interface:** Displays exact and approximate matches, plus end-of-game status.
- **Command-line testing:** Supports debug mode, fixed secret sequences, and direct matching tests.

## How the game works

The Raspberry Pi acts as the codekeeper and generates a secret sequence of **three numbers**, each between **1 and 3**. The player has up to **six attempts** to guess it.

For every guess, the program reports two kinds of matches:

- **Exact:** Correct number in the correct position.
- **Approximate:** Correct number in a different position.

For example:

```text
Secret: 1 2 3
Guess:  3 2 1

1 exact
2 approximate
```

In the hardware game, the player enters numbers using repeated button presses. LED blinks acknowledge input and communicate feedback, while the LCD presents the match counts. When the sequence is guessed, the LCD displays a success message and the number of rounds used.

## Hardware

- Raspberry Pi 2 or 3 with a compatible 32-bit ARM Linux environment
- 16×2 character LCD (HD44780-compatible interface)
- Two LEDs (red and green) with current-limiting resistors
- Push button
- Potentiometer for LCD contrast
- Breadboard and jumper wires

### GPIO connections

The following uses **BCM GPIO numbering**, not physical header-pin numbering.

| Component | BCM GPIO | Purpose / LCD pin |
| --- | ---: | --- |
| Green LED | 13 | Data feedback |
| Red LED | 5 | Control feedback |
| Push button | 19 | Player input |
| LCD RS | 25 | Pin 4 |
| LCD Enable | 24 | Pin 6 |
| LCD D4 | 23 | Pin 11 |
| LCD D5 | 10 | Pin 12 |
| LCD D6 | 27 | Pin 13 |
| LCD D7 | 22 | Pin 14 |

For the LCD, connect ground and power, connect RW to ground, and use the potentiometer wiper for the contrast input (LCD pin 3). **Verify your LCD module’s supply and backlight requirements before connecting power; Raspberry Pi GPIO signals are 3.3 V.**

## Build and run

This code is intended for the supported **32-bit ARM Raspberry Pi environment**, not a standard x86 desktop. Install GCC, GNU binutils, and Make on the Pi, then run:

```bash
make all
```

Start the hardware game:

```bash
sudo ./master-mind
```

Run with the secret visible for debugging:

```bash
sudo ./master-mind -d
```

Use a specific secret sequence:

```bash
sudo ./master-mind -d -s 123
```

The Makefile also defines:

```bash
make unit    # Run command-line matching tests
make test    # Run the separate test harness
make clean   # Remove build outputs
```

> **Compatibility note:** This project uses ARM-specific assembly and direct hardware access. Compilation and runtime behavior depend on the Raspberry Pi model, operating system, toolchain, and GPIO setup.

## Project structure

```text
.
├── master-mind.c   # Main game, GPIO/LCD routines, and inline ARM assembly
├── lcdBinary.c     # Hardware-interface scaffolding
├── mm-matches.s    # Standalone assembly matching scaffold
├── testm.c         # Standalone matching test harness
├── test.sh         # Command-line unit tests
├── Makefile        # Build, run, and test commands
└── README.md
```

## Implementation notes

The application uses memory-mapped GPIO access to interact with the button and LEDs. The gameplay and LCD routines live primarily in `master-mind.c`, which also contains inline ARM assembly. A matching routine compares the secret and guessed sequences while distinguishing exact matches from approximate ones.

**Project status:** This repository represents a development snapshot. The separate `lcdBinary.c` and `mm-matches.s` files still contain scaffold sections, and the hardware build/tests have not been independently verified for this published version. Review and test the code on a compatible Pi before relying on it.

## Attribution

Parts of the source include material adapted from existing example/template code and wiringPi-related implementations. Applicable third-party copyright and license notices should remain in the source. Confirm redistribution rights for all included code before publishing or assigning a repository-wide license.
