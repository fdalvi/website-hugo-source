---
date: 2018-08-20T16:00:00+03:00
lastmod: 2018-08-20T16:00:00+03:00
title: Introducing GroupMe Archiver
authors: ["fdalvi"]
categories:
  - announcements
tags:
  - groupme
  - backup
  - archive
  - chats
slug: 2018-08-20-introducing-groupme-archiver
---

I was recently looking into various ways of archiving old chats into something more future proof, but it seems like these tools are very limited. They either don't exist, or only work for specific scenarios like one-on-one chats without attachments.

Today, the situation gets a bit better for one platform, [GroupMe](https://www.groupme.com). GroupMe is a platform focused on group messaging, and features nice subtle features like _likes per message_, attachments and so on. They also provide an awesome dev REST API - so it was very easy to pull historical data. The result is [hosted on GitHub](https://github.com/fdalvi/groupme-archiver) - scripts to archive any chat on GroupMe, along with its metadata like group and user avatars, mentions, likes and attachments. 

{{% center %}}
  {{% figure src="https://github.com/fdalvi/groupme-archiver/raw/master/docs/render.jpg?raw=true" caption="Sample render for a test chat" %}}
{{% /center %}}

The scripts allows you to store all of the data as raw files and structured JSON, and also render everything as a neat HTML for easy viewing. Head over to the repo at https://github.com/fdalvi/groupme-archiver and check the project out!