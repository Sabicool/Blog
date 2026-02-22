---
title: "Voice Recordings to Inbox Emacs Workflow"
author: ["Sab"]
date: 2026-02-22T12:56:00+11:00
lastmod: 2026-02-22T12:56:29+11:00
categories: ["Productivity"]
draft: false
tags: ["Emacs", "Workflow"]
---

Before I discovered Emacs, I had trialled using many different types of todo management software including:

-   Todoist
-   TickTick
-   Remember the Milk
-   Things 3
-   Habitica

I ended up using Emacs org-mode because of how programmatic and extensible it is. It remains easy to implement whatever feature and workflow I needed instead of adapting to the workflow provided within the Todo Software. This article is a prime example of this.

I recently came across a feature in Todoist called ramble (see more on their website [here](https://www.todoist.com/help/articles/dictate-to-add-tasks-with-ramble-P1Raq7vVF)). I wanted to make a rudimentary version of this. It fixed a minor problem I had with my org-mode system.

Generally, there was some friction to capturing tasks on the go. Typing on a phone is tedious, and by the time I'm back at my computer, I've often forgotten half of what I needed to capture. Capturing all tasks that came up is probably the most important aspect of task management.

Enter voice notes. They're fast, natural, and perfect for brain dumps. But then you're stuck with a pile of audio files that need to be transcribed and processed. This was what the Todoist ramble feature offered to solve, and I wanted a rudimentary replica of this. Ultimately, what I made works really well and allows me to even record audio from my smart watch to be processed into my inbox file. It is available [here](https://github.com/Sabicool/Ramble).


## The Problem {#the-problem}

I wanted to:

-   Speak naturally: "Call the dentist next Wednesday, finish the quarterly report by Friday, and study pharmacology tomorrow"
-   Have it automatically become properly formatted org-mode tasks with deadlines, scheduled dates, tags, and priorities
-   Do it all locally (no sending my voice to cloud APIs)


## The Solution {#the-solution}

I built a Python script that:

1.  Watches a folder for new audio recordings (synced via Syncthing from my phone)
2.  Transcribes them locally using OpenAI's Whisper
3.  Parses natural language to extract:
    -   Individual tasks from rambling speech
    -   Dates ("next Wednesday", "by February 26th", "tomorrow")
    -   Context tags (@home, @computer, @phone)
    -   Priorities ("urgent", "important")
    -   Whether something is a deadline vs. scheduled date
4.  Generates org-mode entries with proper formatting and properties
5.  Archives the audio for reference


## Example {#example}

I record on my phone:

> Call the dentist next Wednesday, finish quarterly report by Friday tag that as important, and study cardiovascular pharmacology on Tuesday

The python script automatically creates in my inbox.org file:

```org
* TODO Call the dentist
SCHEDULED: <2026-02-12 Wed>
:PROPERTIES:
:RECORDED: [2026-02-08 15:30]
:RECORDING_FILE: Voice_001.m4a
:TRANSCRIPT: Call the dentist next Wednesday...
:END:

* TODO Finish quarterly report
DEADLINE: <2026-02-14 Fri>
[#A]
:PROPERTIES:
:RECORDED: [2026-02-08 15:30]
:RECORDING_FILE: Voice_001.m4a
:TRANSCRIPT: Call the dentist next Wednesday...
:END:

* TODO Study cardiovascular pharmacology :@notetaking:
SCHEDULED: <2026-02-11 Tue>
:PROPERTIES:
:RECORDED: [2026-02-08 15:30]
:RECORDING_FILE: Voice_001.m4a
:TRANSCRIPT: Call the dentist next Wednesday...
:END:
```


## How It Works {#how-it-works}

The script uses:

-   faster-whisper for local transcription (no API calls, fully private)
-   watchdog for monitoring file system changes
-   Regular expressions for parsing natural language dates and keywords
-   Pattern matching to split rambles into discrete tasks

It handles edge cases like:

-   Files syncing via Syncthing (which don't always trigger normal file creation events)
-   Multiple date formats ("tomorrow", "next Friday", "February 26th")
-   Distinguishing deadlines from scheduled dates based on context ("by Friday" vs "on Friday")
-   Automatic tagging based on keywords


## Running It {#running-it}

The script runs as a background service on my Mac (via launchd), constantly watching the sync folder. When a new voice memo appears, it's processed within seconds.


## The Workflow {#the-workflow}

The friction of capture is real. Every extra step between "having a thought" and "having it in your system" is a chance for that thought to be lost. Voice notes remove that friction, but only if they're automatically processed into your actual workflow.

Now my capture flow is:

1.  Pull out phone or tap record on my watch
2.  Record voice note
3.  Done

The tasks appear in my org-mode inbox, properly formatted, with the right dates and contexts. No typing, no manual processing, no friction. I do need to later process my inbox to ensure it gets refiled to the correct spot, but that's part of my daily review workflow.
