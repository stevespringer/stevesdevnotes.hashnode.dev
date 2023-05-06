---
title: "Unix design philosophy and music are great teammates"
datePublished: Sat May 06 2023 22:24:34 GMT+0000 (Coordinated Universal Time)
cuid: clhcjyre2000509ml50tygwy1
slug: unix-design-philosophy-and-music-are-great-teammates
tags: music, unix

---

One of my favourite things about working with any Linux OS is leveraging Unix commands, piping the inputs and outputs of powerful but single-minded tools to compose a larger command that meets your needs.

The [Unix philosophy](https://en.wikipedia.org/wiki/Unix_philosophy) is all about having separate programs which each do just one thing well. If you want to do something different with your program's output, you use it as an input to another program.

## How does this apply to music?

[Div's MIDI Utilities](http://www.sreal.com/~div/midi-utilities/) is a set of programs that work a little bit like those standard Unix commands, but instead of text, they are built to work with MIDI files and MIDI inputs and outputs

I've found the `brainstorm` program fantastic. Here's its description:

> *Brainstorm* functions as a dictation machine for MIDI. It listens for incoming MIDI events and saves them to a new MIDI file every time you pause in your playing for a few seconds. The filenames are generated automatically based on the current time, so it requires no interaction. I find it useful for recording brainstorming sessions, hence the name, and use it more than all the other utilities put together.

This gives a really nice workflow for capturing ideas while noodling on the (digital) piano - you know that your few-second silence will demarcate the files that get created, so you can visit those files at the end to review what you came up with.

### Chaining commands

An elegant feature of brainstorm is its `--confirmation` option. It allows you to specify a command to be executed every time a new file is created, using `%s` as a placeholder for the filename.

It's an optional argument but I normally specify a simple `echo %s` so that I get the newly created filenames reported in the console.

However, you can also use it to chain the output (midi file representing what you just played) to the input of another of its commands.

In my case, I've opted to have it call `playsmf.exe`, so that once a musical idea has been captured and I've stopped playing new notes for a few seconds, I get to have it played back to me through my piano right away. This is very useful when practising a musical phrase or testing out musical ideas as it gives instant feedback.

The command to achieve this looks like this:

```powershell
brainstorm.exe --in Clavinova --confirmation "playsmf.exe --out Clavinova %s"
```