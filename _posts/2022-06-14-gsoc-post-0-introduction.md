---
layout: post
title:  "GSoC Post 0: Introduction"
date:   2022-06-14 09:38:37 +0530
categories: jekyll update
---

Hello, reader! I am Suhaas Joshi, a 20-year-old 3rd-year student at CHRIST University, India. I have been selected to GSoC 2022 as a mentee in KDE. This blog will track my KDE development work during, and after, GSoC coding period.

## About the Project:

Flatpak and Snap applications run inside sandboxes, isolated from the rest of the system, and do not have access to many critical resources. As a result, they often cannot do much, and must seek permission to access the resources they require. Flatpaks access these permissions through “portals”, and Snaps do it via “interfaces”. At installation time, these permissions are usually granted by default. Presently, KDE lacks a home-grown mechanism to edit these permissions.

My [SoK 2022 Project](https://community.kde.org/SoK/Ideas/2022#Permission_management_for_Flatpak_Apps_in_Discover) had two parts: the first was to display these permissions on Discover's interface, and the second to develop a mechanism (through a KCM module, it was decided) to let users change these permissions. The first segment of the project was accomplished, but the second was leftover.

This GSoC project involves creating the KCM module for Flatpak, and also showing Snap permissions on Discover's interface in the same way as Flatpaks, as well as creating KCM modules for Snaps.

So far, I have created the repository for Flatpak KCM [here](https://invent.kde.org/jsuhaas/flatpak-kcm).

## About Me:

I am a 20-year-old from India, currently pursuing a BTech at Computer Science and Engineering. I have been programming for about 2 years, chiefly in C++, C and Python. I use Fedora KDE as my daily driver, and have been a KDE user for a year. In my free time, I read books on history and politics, and have been getting into Chess lately.

Feel free to contact me if you have any suggestions!
