---
title: "Using the 3 footstep bug to understand more about Pokemon Go"
tags:
  - systems
  - bugs
  - pokemon go
---

If you've played Pokemon Go and tried to track Pokemon, you are probably aware of a current bug in the system for tracking nearby Pokemon.
Hopefully, it's been fixed by the time you read this, but let's understand how tracking is *supposed* to work.

The tracking interface shows up to 9 "nearby" Pokemon.
Each Pokemon (or silhouette if you haven't seen it yet) has 1 to 3 footprints under it to signify roughly how far away it is from your location.
At 1 footprint, you're right on top of it and it should be showing up any time.
At 3 footprints, you've got some walking to do, but it's in your "immediate vicinity."
I have yet to discover what "immediate vicinity" means exactly, but that's a discussion for another post.

However, with the bug, every Pokemon always has 3 footprints under them, no matter how close they are.
Without that information, players have had to turn to another pattern:
Pokemon closer to the top of the list are closer to you.
I have observed this myself, although I haven't been able to determine if the Pokemon on the right or the left are supposed to be **closer** to you.

Before we get into learning more about the internals of the game through this bug, let's go over some things we can know *without* the bug.

**You are probably regularly communicating with the same server or servers.**
Pokemon Go is a big game across a number continents.
This means that the game is probably run both together and separately.
They probably use a lot of servers in many different locations to serve all of the players.
It wouldn't make sense to use one server or location for durability and latency reasons.
Basically, if you are playing in the same area (maybe city, county, state, or region) you are probably talking to the same set of servers responsible for that area.

**Communication payloads are probably as small as possible.**
This is out of necessity for the sheer number of communication requests the servers have to handle concurrently.

**Your client likely isn't told about the location of Pokemon around you.**
If this weren't true, hacked clients could just show all of the Pokemon.

**You probably don't get a full update every time the "Nearby Pokemon" tracker refreshes.**
You can observe this if you are near a low Pokemon population area.
If your client was on for awhile, you'll have 9 Pokemon in your "Nearby" tracker.
If you force close the app and load back in, the "Nearby" tracker will only show 2 or 3 pokemon.

**Low latency is key to a game like this working.**
Once latency hits probably above 500ms (that's half a second just for network, no server calculations), the game experience starts to degrade and requests will start backing up.
Once requests start backing up, the server either has to drop requests or increase latency, both eventually leading to downtime.

**Because of these, it's extremely important right now that server-side calculations are done as quickly as possible.**
They need Pokemon go to be up and playable, otherwise no one plays, it's not popular, and it gets shut down before they have a chance to make it better.

**Pokemon Go has had a rough start because of it's popularity.**
It's been so popular and had so many simultaneous users that the servers haven't been able to keep up, sometimes leading to hours of downtime per day.
In a case like this, the easiest thing to do is throw more servers at it, but there seem to be other restrictions that don't make this easy for them.
This could be that the network bandwidth to the servers is actually the bottleneck or a distribution model that was designed before they thought it would be this popular.

**Calculating the distance between two GPS coordinates 

So here's my conclusion.
