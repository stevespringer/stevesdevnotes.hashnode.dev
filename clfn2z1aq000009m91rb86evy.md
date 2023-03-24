---
title: "Copy-paste docker admin with ChatGPT"
datePublished: Fri Mar 24 2023 21:54:56 GMT+0000 (Coordinated Universal Time)
cuid: clfn2z1aq000009m91rb86evy
slug: copy-paste-docker-admin-with-chatgpt
tags: docker, chatgpt

---

Me:

> This is the result of a call to docker inspect. How much disk space is the container using?  
> &lt;&lt;entire, long json results from `docker inspect` copy-pasted&gt;&gt;

ChatGPT:

> The container is using 7.7 GB of disk space, as indicated by the "Size" and "VirtualSize" fields in the output of the `docker inspect` command.

Yay! I didn't even need to convert 7705093834 bytes into a human-readable format.

I know there is a neat way to call docker inspect (or some other command) to get the same info. But I haven't done docker stuff in ages, I remembered docker inspect, and I knew the size was somewhere in the json it spat out. ChatGPT saved me from giving it another thought!