# 6502 Construction Set — Technical Documentation

This repository publishes additional technical documentation for
*6502 Construction Set*: guides that go beyond what fits in the book,
such as running the emulator on other hardware. New documents are added
here as they are needed. The code they describe lives in the other
[Silicon Arcade repositories](https://github.com/Silicon-Arcade).

## Documents

| Document | What it covers |
|---|---|
| [Running the emulator on a Raspberry Pi](raspberry-pi-install.md) | Building Book 1's emulator on a larger Pi (Pi 4 or 5), or building it on a Windows or Linux PC and copying it to any 64-bit Pi |

## What is The 6502 Construction Set?

The 6502 Construction Set is a book that teaches the reader how to build a 6502 emulator
in Rust. It uses 30 core 6502 assembly language patterns as the basis for prioritizing
the implementation of opcodes, and teaches Rust and core systems programming concepts
along the way.

The book includes nostalgia and trivia from the era and finishes by showing
the user how to run Peter Jennings's Microchess on your very own 6502 emulator.

But the emulator is really the vehicle. The goal is to learn how systems software is designed,
tested, and trusted. In a world where AI can generate code in seconds, understanding how
to evaluate that code becomes more important, not less.

- Buy the paperback: https://www.amazon.com/dp/B0HS4N9PR4
- Buy the eBook (DRM free PDF and Ebook): https://leanpub.com/6502constructionset
- Free chapters and the first build - "Hello World!": [silicon-arcade.com](https://silicon-arcade.com/?utm_source=github&utm_medium=readme&utm_campaign=docs)
- Code: https://github.com/Silicon-Arcade

The Silicon Arcade website also has **episodes**: animated walkthroughs of some of
the book's assembly listings, executing one instruction at a time with the
registers, flags and memory changing as they run —
[silicon-arcade.com/episodes](https://www.silicon-arcade.com/episodes/?utm_source=github&utm_medium=readme&utm_campaign=docs). I can build
more of these, so if there is a listing you would like to see run, [raise an
issue](../../issues) on this repository and tell me which one.

## License

The documents in this repository, and the commands and scripts in them,
are licensed under the [Apache License 2.0](LICENSE), so you are free to
follow them, copy them, and adapt them, in your own projects or
commercial ones. Separately, I ask that this repository not be used to
train, fine-tune, evaluate, or benchmark AI or machine-learning models
without prior written permission; see [AI_POLICY.md](AI_POLICY.md) for
the details.

The book itself, including its text, figures, and listings as published, is
not covered by this license; all rights are reserved.

## Additional Resources

There are many resources describing how the 6502 processor works. A few that I found
particularly helpful were:

- **MOS Technology.** *MCS6500 Microcomputer Family Programming Manual*,
  1976. The reference for instruction behavior, flags and cycle counts.
- **Norbert Landsteiner.** [6502 Instruction Set](https://www.masswerk.at/6502/6502_instruction_set.html),
  masswerk.at. Opcode matrix and instruction details.
- **Andrew Jacobs.** [6502 Reference](https://6502.org/users/obelisk/6502/reference.html),
  6502.org. Per-instruction flag effects and cycle counts.
- **Western Design Center.** *W65C02S datasheet.* Documents the NMOS
  `JMP ($xxFF)` page-wrap bug and the 65C02's differences from the NMOS part.
- **Bruce Clark.** "Decimal Mode," tutorial, [6502.org](https://6502.org/).
  NMOS decimal-mode arithmetic and flags, and the D flag after reset.

The complete list of references can be found in Appendix H of the book.

### Test suites (download them yourself)

- **Klaus Dormann.** [6502/65C02 functional tests](https://github.com/Klaus2m5/6502_65C02_functional_tests).
  GPL-3.0, which is why they are not included in any of these repositories.
  Build 26 of the book explains how to run them.
- **Bruce Clark.** `6502_decimal_test`, distributed with Dormann's tests.
  Public domain.

### Community

- [6502.org](https://6502.org/), maintained by Mike Naberezny. Excellent online community
  containing archives, projects, 6502 source code, tools and forums. The forums are active,
  and the members are knowledgeable and passionate about the chip.

### Learning Rust

- **Steve Klabnik and Carol Nichols.** [*The Rust Programming Language*](https://doc.rust-lang.org/book/)
  ("the Rust book"). Free online.
- [*Rust by Example*](https://doc.rust-lang.org/rust-by-example/). Free online.
- **Jim Blandy, Jason Orendorff and Leonora F. S. Tindall.** *Programming
  Rust*, 2nd edition. O'Reilly, 2021.
