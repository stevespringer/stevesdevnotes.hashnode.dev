---
title: "Getting ChatGPT to clean up its mess"
datePublished: Fri Mar 24 2023 19:09:39 GMT+0000 (Coordinated Universal Time)
cuid: clfmx2h7p000509js2sv74m3f
slug: getting-chatgpt-to-clean-up-its-mess
tags: bash, chatgpt

---

Like many creative coding endeavours, working with ChatGPT can have you flip-flopping between utopian optimism and crushing disappointment.

I ran a bash script that ChatGPT wrote for me to transform timestamps contained in a text file. Instead, it flooded my working directory with new text files - one for each line of the source text. Doh!

At least ChatGPT shines when you need it to do something really simple, when you can't be bothered to think about\\look up the correct commands:

> write me a command to delete all files created today

```rust
find . -maxdepth 1 -type f -newermt "$(date -d 'today 00:00' +'%Y-%m-%d %H:%M:%S')" ! -newermt "$(date -d 'today 23:59:59' +'%Y-%m-%d %H:%M:%S')" -delete
```

Perfect, great job, ChatGPT! Luckily, it got that one right on the first try.