# Rumble Video Lister v2

This is a substantially improved version of the [previous](https://github.com/Novgorod/Rumble-Video-Lister) Rumble research tool for metadata listing of all publicly accessible video IDs including unlisted and anonymized / embed-only (posted by "hostid1") videos. The tool performs a multi-threaded scan of the sequential video IDs posted within a user-defined time window by using the efficient oEmbed JSON API and checking only even video IDs (odd IDs are not used by Rumble). The efficiency improvements over the old version allow scanning up to 100 video IDs per second (depending on the latency of the internet connection) while generating only 10% of the web traffic.

<img width="703" height="851" alt="Rumble_lister_v2" src="https://github.com/user-attachments/assets/f591643c-2017-42da-8f1f-0a1b276d0ac5" />

## Features

The tool displays a metadata list of discovered videos, which can be optionally logged to a text file. Anonymized videos do not have descriptive metadata (title and channel name) and are embed-only, i.e., only accessible through their embed URL. The collected metadata includes:
- Timestamp (either the time of the upload or the scheduled publication time; currently running livestreams are marked as "LIVE")
- Video duration
- Channel name (for anonymized videos "hostid1")
- Title (for anonymized videos this only includes a sequential numeric ID)
- Canonical video URL (for anonymized videos the embed URL, since the canonical URL appears as private)

The user can now list video metadata between a user-defined starting and ending timestamp (all timestamps refer to the local system's time zone) instead of manually looking up corresponding video IDs. The translation between (sequential) video ID and timestamp is obtained by scanning a short range of video IDs in order to extrapolate (and refine) the approximate video IDs at the start and end of the user-defined time window. Note that the mapping between timestamp and video ID is not strictly monotonic because a small percentage of videos have a manually set a publication time (appearing as the timestamp) that is different from the posting time (when the video ID is generated). The search for the video IDs at the user-defined timestamps therefore uses statistical methods to eliminate these outliers, which might fail in very rare cases requiring a slight change of the user-defined time window. In addition, videos that were posted outside the user-defined time window but with a publication time manually set within that time window will not be listed, since the scan is performed by sequential video ID (i.e., posting time), not by publication time. The automatic determination of the video IDs corresponding to the user-defined time window takes less than 10 seconds on average.

The scan can be optionally logged to a text file and the scan is automatically stopped as soon as an optionally provided keyword is detected in the video title or channel name. The scan always starts from the starting timestamp, even if the ending timestamp predates it, in which case the scan is performed in reverse.

## Usage

Labview 2023 or later is required to view and run the code. You can use the free [community edition](https://www.ni.com/en/support/downloads/software-products/download.labview-community.html). It should run on all supported Labview platforms, but it was only tested on Windows. Clone / download the repo, then open and run the "Rumble lister v2.vi". Enter a "Start time" and "Stop time", choose a path for the log file (leave blank if not needed) and press "SCAN". The scan is stopped when the keyword is found in the title or channel name, which is marked in the listing log. If the keyword is blank, the entire time window is scanned. The keyword can be changed during the scan. Depending on the number of CPU cores / threads and internet connection speed, the performance may be improved by changing the "Threads" number from the default value. Note that the metadata list is not strictly sorted by video ID, since it is generated live from the multi-threaded scan. Consecutive video IDs can be separated by up to the number of threads.

## Building

The repository contains a Labview project file with a build specification for a standalone executable on Windows (.exe). Verify the correct paths in the build specification, (re-)save the project file to create the .aliases file, and build the specification. Building standalone applications requires the full Labview development environment.
