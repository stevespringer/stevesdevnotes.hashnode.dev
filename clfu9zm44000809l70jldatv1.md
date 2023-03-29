---
title: "Search video files by their dialogue"
datePublished: Wed Mar 29 2023 22:45:44 GMT+0000 (Coordinated Universal Time)
cuid: clfu9zm44000809l70jldatv1
slug: search-video-files-by-their-dialogue
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/V5vqWC9gyEU/upload/b046a11cbcfd3ce3e8a34fa060a7ef25.jpeg
tags: search, openai, video-annotation, video-dataset, subtitles

---

[Whisper](https://github.com/openai/whisper) is a tool by OpenAI designed to:

* transcribe speech from audio files into written form
    
* translate speech from audio files into a different language
    

### My use case

I have a bunch of instructional video files on my local computer, some very short and some which are hours long, and it can be tricky to find the specific parts of those videos (which consist mainly of speech) that I'm interested in.

I wanted to explore a way to:

1. Take a folder full of **videos**
    
2. Perform a **textual search** against the contents of the speech in the videos
    
3. Get a list of results that will allow me to **start the videos from the relevant moment** when the search term is mentioned
    

### Before starting...

I should mention 2 things:

* this is a simple proof-of-concept\\experiment and not a polished solution
    
* there may be cloud solutions out there to do this already; I just wanted a simple way to do it on my local machine.
    

### Step 1: Produce the transcriptions

Whisper can be used via its Python API, but the CLI is great. Its available features extend far beyond the needs of this experiment.

Many output formats are available, including `srt` and other subtitle formats, and a detailed `json` output. But for my basic purposes, a 3-column tab-delimited record will do: start time, end time, and matching text.

This command will transcribe the speech of every video in my folder, placing the results in `.tsv` files with the same filenames as the \``` .mp4` `` files they were transcribed from:

```bash
find . -type f -exec whisper --output_format tsv --output_dir /videos/ --model base.en {} \;
```

### Step 2: Search the transcriptions

I used a basic `grep` call to search the files, but you can explore fuzzy search, NLP techniques, and more. I recommend reading [Awesome Search](https://github.com/frutik/awesome-search).

The command below performs a case-insensitive search on every `tsv` file in the `videos` subdirectory.

```bash
grep -wriHT "SEARCH TERM HERE" videos/*.tsv
```

To convert the results to `json,` I pipe the above command to this `jq` command (it will also sub back in the `.mp4` extension to point to the video file):

```bash
jq -Rs '
        split("\n") |
        map(select(length > 0)) |
        map(split("\t")) |
        map(
            {"file": (.[0] | sub("\\.tsv:$"; ".mp4")),
            "start": .[1],
            "end": .[2],
            "text": .[3]}
        ) '
```

### Step 3: Display the results on a webpage

#### Attempt 1: no web server (file:// scheme):

This served video very quickly and was lightweight, but due to browser security requirements, I needed several messy workarounds, and dealing with paths across [Windows Subsystem for Linux](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux) was clunky.

#### Attempt 2: inbuilt Python static web server

This is handy but for this case, it failed when I wanted to start videos some way into their duration (I think because it has no `Accept-Ranges: bytes` support.

#### Attempt 3 (success!): nginx over docker

It's pretty easy to launch a static web server at the current directory:

```bash
docker create --name video-nginx -p 8000:80 -v $(pwd):/usr/share/nginx/html:ro nginx
docker start video-nginx
```

### Source code

The code in this article can be found at [https://github.com/stevespringer/video-dialogue-search](https://github.com/stevespringer/video-dialogue-search).

The commands above can be run via a script, `search.sh`, which takes the search term and the base URL of your local webserver at the root of the project.

The script outputs a URL (which, in Windows Terminal you can `ctrl+click` to open) that takes you to the search results.

### Demo

%[https://twitter.com/stevepspringer/status/1641208182600073216]