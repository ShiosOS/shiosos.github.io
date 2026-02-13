---
title: "NxtUp: Working Around YouTube's Watch Later API Limitations"
published: 2026-02-12
description: "Exploring a new architecture to manage YouTube's Watch Later playlist using a browser extension bridge"
category: Dev Blog
tags:
  - NxtUp
---

## Doom. Doom?! Doom!!!?

:::tip[Doom?]
<details>
<summary>doom.</summary>

<iframe width="100%" height="468" src="https://www.youtube.com/embed/kscTD_9a5ec" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

</details>
:::

Yes, doom.

The YouTube API doesn't directly support getting the watch later list. Even if the watch later list is considered private user data, I do not understand why an OAuth authorized API cannot access it. I almost gave up.

## Moving Forward

The only way I could find out how to realistically do this is through using an extension and combining it with a webapp.

## New Architecture

### The Core Problem

YouTube's Data API v3 does not expose the Watch Later playlist. OAuth tokens and API scopes cannot access the `WL` playlist; therefore, making server-side access impossible.

The only way to read and/or write to a user's Watch Later playlist is from within the authenticated browser session.

### System Components

#### Vue 3 Frontend

This will still be the primary user experience and interface. The playlist management will occur here.

**Requirements**

* Display the user's Watch Later playlist
* Reorder, filter, and categorize videos
* Mark videos as watched / unwatched

#### .NET Backend

The "common place" that all other components communicate through. The backend will feed data from the frontend to the browser extension and vice versa.

**Requirements**

* Google OAuth authentication
* CRUD operations for the Watch Later playlist
* Command queue for extension-bound actions
* Syncing the diff between YouTube and NxtUp

#### Browser Extension

The bridge between NxtUp and YouTube. This component will be responsible for making the changes to the Watch Later playlist on YouTube.

**Requirements**

* Read Watch Later via YouTube's internal API
* Write changes to the Watch Later
* Poll .NET backend for pending commands
* Auto-sync Watch Later on a timer

#### PostgreSQL

The database that acts as a source of truth for the Watch Later playlist.

**Requirements**

* Stores the following data
    * Users
    * Videos currently in Watch Later
    * Extension commands to be processed
    * Syncing history audit log

## TL;DR

I hope this works, but this is all speculation from things I've read on the internet.
