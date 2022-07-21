---
layout: post
title:  "GSoC Post 1: FlatpakKCM Update 1"
date:   2022-07-21 09:38:37 +0530
categories: jekyll update
---

The previous blog post introduced my project for this summer. This blog post gives an update on the work that has been done so far in the past 5 weeks, and sketches out the plans for upcoming weeks.

## Basic Concepts:

### Types of Permissions:

Flatpak Permissions are categorized under "shared", "sockets", "devices", "features", "filesystems", "session bus policy", "system bus policy" and "environment".

Among these, the first 5 belong to the "Context" group of permissions, while the last 3 are their own groups (going by the same name).

Among these, the first 4 are of (what I term) the "simple" type: they have only an on/off value, and nothing more. They can either be turned on, or off. No further complications or specifications.

The "filesystems" permissions can either be set off, or be set to "read-only", "read/write" (this is default when the permission is "on" but there is nothing to specify the access level) or "create".

The Session and System Bus Policy permissions (which are just names of buses with corresponding values) can have the values "talk", "own", "see" and "None". The first three mean that the permission is "on", and the last means that the permission is "off".

The Environment permissions can have any value that the user types, instead of having a fixed set of values to choose from.

### Metadata and Overrides File Structure:

The default values for all these permissions are loaded from their InstalledRef's metadata file. The "current" values (that is, values changed from default by the user) are located in an overrides file (that is named the same as the Flatpak app's ID in a overrides directory, usually at ~/.local/share/flatpak/overrides).

The permission sections of the metadata file is described below: 

For "simple" permissions, if the name of the permission exists with the respective category under the Context group, then the permission is granted by default. For "filesystem" permissions, if the permission name exists with the "filesystem" category under the Context group, the permission is granted by default too, and the access level is specified after a semi-colon (or not specified at all, in which case it is "read/write"). Since these permissions are under the Context group, they are specified under a "[Context]" heading.

The "bus" permissions are specified under the name of the bus policy group. Each bus is specified in its own line. The format is:
    busName=value
If the bus name is specified under the relevant header, the bus exists. It is not set to "off". The value specifies if the application can just talk to the bus, or own it.

The "environment" permissions are listed exactly as the bus permissions, under the "[Environment]" header. The only difference is that these may have any value, and not one from a fixed set.

To see this format for yourself, type the following command on the terminal:
    flatpak info -M id.of.any.flatpak.app.installed.on.your.system

Having described this context (pun intended), I proceed to track what work is done:  

## Work Done:

I started with loading all installed flatpak applications (in the "FlatpakRef" class), specifying their IDs, display names, versions, icons etc. This was easily done using FlatpakInstalledRef struct and related functions. Once this was done, I progressed to loading the permissions granted to each application.

To do this, the first step was to load all the permissions regardless of whether or not it is granted. Therefore, I created instances of FlatpakPermission class with names, descriptions and values for all permissions listed in the [Flatpak Metadata](https://www.man7.org/linux/man-pages/man5/flatpak-metadata.5.html). This is necessary because the user needs to be able to see all the permissions on the menu for them to edit. At this point, the permissions were just loaded, their values were not granted.

The next step was to load their default values. This is done by parsing the metadata file (as described in the above section). After this, current values had to be loaded. Therefore, we parse the overrides files (if it exists at all) and store the current values.

After this, I implemented the ability to set and unset the permissions. For simple permissions, this was straightforward, since the new value of the permission had to just be the opposite of the old value.

For filesystems, bus and environment permissions (hereby collectively called "complex" permissions), the user needs the ability to "edit" the permissions (that is, change their level, such as upgrade "talk" to "own" for buses or downgrade "read/write" to "read-only" for filesystems) in addition to setting/unsetting the permissions.

For complex permissions, if you set a permission to on from off, the permission is restored to the same level that it had before it was set to off. For permissions that never had their levels changed, this is the default level. For permissions that did have their level changed, this was the last change the user made before setting it off. For permissions that were never turned on (that is, those that were also off by default), the level ends up being whatever the default for that permission is (eg read/write for filesystems).

I implemented the loading of default values, current values and setting of permissions for simple permissions first, and then moved on to the complex ones. I did this because the simple ones were simple, and would give me an idea of what to do next, but this also led to some design decisions that were not accomodative of the demands of complex permissions, so I spent some time between these two refactoring.

<img src="{{ "/assets/img/sspost1.png" | prepend: site.baseurl | prepend: site.url}}" alt="Screenshot"/>


## Future plans:

I am presently working on implementing the apply/default buttons (currently, just setting the values changes the value for real, instead of waiting for the user to click apply), and allowing the user to set those complex permissions that are not mentioned explicitly in the metadata. Once this is done, I will begin working on the redesign of the interface. The redesign we have in mind is this:

Divide the permissions page into two parts: the "basic" section and the "advanced" section. The latter is hidden by default and can be accessed through a button labelled "Advanced...". The basic permissions will be few and clumped together, while the advanced ones would be many categorized under their respective section names. For the advanced permissions, we will be providing some tooltips to help the user navigate them better.

## Learnings:

The learning has been immense:

1. I got better at using Qt and learned a lot more about model/view programming.
2. I experienced and learned from the costs of writing bad code without thinking through properly. But fortunately, this gave me invaluable lessons on also _how_ to think through properly and write code that is generic yet elegant (I am quite excited to embark on a refactoring quest after GSoC formally ends and put this learning to use:D).
3. I had always struggled with file handling across all languages I have used, but now I am quite comfortable with it.
4. Understood some modern C++ concepts, such as lambdas and smart pointers, which I intend to start using for all future development.
5. Finally, I got an insight into how Linux applications work and behave, specifically flatpaks, such as the different files that the app depends on and their locations on the system.

Here, I end this blog post. Hopefully, my next blog post will be a week or two from now describing the end of the work outlined above. Until next time!