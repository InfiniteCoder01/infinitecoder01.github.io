---
title: Compiler in 🦀
layout: /src/layouts/Layout.astro
---

# Writing a compiler backend for my programming language in 🦀

> **_Heads up!_**\
> Work in progress\
> I sometimes stream the development [here](https://twitch.tv/infinitecoder01)

This all started when I decided to make my own language. Again.

I wanted to make a rust-like language with an effect system and some more
features. You can see it on [github](https://github.com/InfiniteCoder01/Trustlang).

I\'ve tried many approaches, but settled on tokenizing text → parsing it into an AST →
building an Intermediate Representation (IR) from this AST → converting this IR into
raw bytes → packaging those bytes into an application like ELF or PE.

The first problem I encountered with the backend itself is compiling to x86\_64.
That\'s the first architecture I\'ve tried to implement and already got stuck.
Turns out that x86\_64 instruction set is really difficult (THIS IS ALL ABOUT
LEGACY and BACKWARDS COMPATIBILITY!!!). It has some weird things like REX (register extensions)
or some shortcuts (For example `add AL, 5` is taking 2 bytes (encoded as `04 05`) when
`add BL, 5` takes 3 bytes (encoded as `80 c3 05`))

I have found this great page as reference for x86\_64 opcodes: [X86-64 Instruction Encoding](https://www-user.tu-chemnitz.de/~heha/hsn/chm/x86.chm/x64.htm)
([OSDev Wiki](https://wiki.osdev.org/X86-64_Instruction_Encoding) is basically the same page)
[This](https://shell-storm.org/x86doc/) table also helped me a lot.

When I did some first steps with x86\_64, I had to pack it into some sort of executable,
which turned out a big challange. I couldn\'t find any rust crates that will write
elf executables, only ones that write relocatibles and I want to integrate a linker into
my backend. [This](https://www.youtube.com/watch?v=nC1U1LJQL8o) video helped me
A LOT with reading/writing elf files. But be aware: it has some wrong types! For example (the only one I found)
sizes in program header. Check [wikipedia](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) for that!

When I\'ve implemented a minimal functionality for my elf crate, I\'ve tried to compile a simple program
and got segmentation fault. [This video](https://www.youtube.com/watch?v=XH6jDiKxod8) helped me finally figure it
out. Essentially entry point has some strict rules.
Finally, I released this crate: [orecc-elf](https://crates.io/crates/orecc-elf)

Started writing: Sep 30, 2023\
Last edit: Jun 18, 2024
