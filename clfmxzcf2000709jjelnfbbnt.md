---
title: "ChatGPT's commit messages are better than its code"
datePublished: Fri Mar 24 2023 19:35:13 GMT+0000 (Coordinated Universal Time)
cuid: clfmxzcf2000709jjelnfbbnt
slug: chatgpts-commit-messages-are-better-than-its-code
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/EZSm8xRjnX0/upload/e41b94f3575bbc8882f65fddd8cf8026.jpeg
tags: git, chatgpt

---

Even though its script fails to do its job, it wrote a beautiful commit message when I asked it *"Can you write me a commit message for the above script"*:

```plaintext
Add script to add 30 seconds to timestamps in srt files

This commit adds a Bash script that can be used to add 30 seconds to
every timestamp in an srt file. The script uses sed to replace each
timestamp in the file with a command substitution that evaluates to the
timestamp plus 30 seconds.

The script creates a backup of the original file before making any
modifications, and evaluates the command substitutions using eval to
produce the final output. The script also includes checks to ensure that
the file exists and that an argument is provided.

This commit should make it easier for users to adjust the timing of
subtitles in srt files without having to manually edit each timestamp.
```

That's more thorough than what I would have written! Now to get the script to actually work. Details...