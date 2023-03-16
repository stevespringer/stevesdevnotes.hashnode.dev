---
title: "A simple request that ChatGPT nailed first time"
datePublished: Thu Mar 16 2023 23:52:04 GMT+0000 (Coordinated Universal Time)
cuid: clfbrmu2c000409l229lz84ur
slug: a-simple-request-that-chatgpt-nailed-first-time
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/uiSxC_-s7K0/upload/782d53838e08a52f9a69529d57f7f011.jpeg
tags: keyboard-shortcuts, autohotkey, chatgpt

---

Me:

> Can you write an autohotkey script to bind to Win+O which launches notepad and triggers the file open menu?

ChatGPT:

```rust
#o::
Run, notepad.exe
Sleep 500
Send ^o
return
```

> When you press the Win+O key combination, this script will execute the following actions:
> 
> 1. Launch Notepad by running `notepad.exe`
>     
> 2. Wait for half a second (500 milliseconds) to ensure that Notepad has finished launching.
>     
> 3. Send the keyboard shortcut for opening the file open menu (`^o`).
>     
> 
> You can save this script as a `.ahk` file and then double-click it to run it with AutoHotkey.

## Thoughts

I think this demonstrates how useful AI can be for helping you with those tasks you do so infrequently that you're very unlikely to remember syntax without having to do a lot of googling or digging through past scripts.

[AutoHotkey](https://www.autohotkey.com/) is one of those technologies that I make use of on a daily basis but almost never have to change, or write new things in.

In this instance, ChatGPT saved me mental CPU cycles, keystrokes, potential syntax errors and StackOverflow trawling.

Not revolutionary perhaps, but useful nonetheless.