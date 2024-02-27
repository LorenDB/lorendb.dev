+++
title = 'Thoughts on KSP2 For Science!'
date = 2023-12-26T09:20:21-05:00
tags = ["gaming", "ksp2"]
images = ["images/ksp2-screenshots/for-science-reentry-heating.jpg"]
description = "It's got science! It's got fire! It's got bugs!"
+++

[Kerbal Space Program 2](https://www.kerbalspaceprogram.com/games-kerbal-space-program-2) had its first
major update, called For Science!, released a week ago (as of my writing this). As a KSP nerd, I
downloaded it and ran it that day. Since then, I've done some messing around with it, and I thought
I'd write down my impressions.

## The good

There's so much to love about the For Science! update. As the first Early Access milestone, it brings a new
gameplay mode called Exploration mode, which is similar to the Science mode from the original Kerbal Space
Program. And boy, has science been improved! In KSP1, science was often cumbersome; to collect all your
science points, you needed to track every time you entered a new biome and manually run all of your
experiments. In KSP2, you just add one science part to your craft (the Science Jr. is a great example) and
start flying. Science collection runs automatically for you, meaning that you won't have to wonder if your
orbit just crossed another biome. Granted, some contracts require you to take a Kerbal on EVA and manually
make an observation, but in general, science is very passive.

And speaking of contracts, that system is also improved. I haven't personally played KSP1 in career mode
(or at least not any farther than one or two launches), but I've heard that KSP1 contracts were often bland:
you might be asked to launch a vehicle above 1000 meters. In KSP2, you immediately get believable contracts
like proving that you can get to space, then orbiting, then going on EVA, and so forth and so on.

Another huge part of For Science! is the addition of a thermals system. The KSP2 team has created a
beautiful reentry effect. Here's the thermals on reentry from Minmus (this was after a number of aerobraking
passes, so my actual velocity wasn't too high):

{{< figure
    src="https://lorendb.dev/images/ksp2-screenshots/for-science-reentry-heating.jpg"
    alt="A capsule reentering Kerbin's atmosphere at sunset"
    title="Isn't it just gorgeous?"
>}}

While some people have reported bugs with thermals, I'm happy to report I haven't encountered any yet.

There's a lot more to cover, but I'll cut this section short in interest of keeping this post readable.
However, I'll at least mention a few: wobbly rockets are fixed (!!!), there are many more easter eggs (or
discoverables, as they are now called), the boat dock has had a facelift, and buoyancy works, just to name
the most obvious ones.

## The bad

KSP2 For Science! hasn't been all sunshine and roses, however. There have been some bugs pointed out, but
I've personally experienced two of them.

The first is orbital decay. In real life, given enough time, any orbit will decay to the point that the
orbiting object crashes. In KSP and KSP2, orbital decay is not implemented; players don't want to have to
manually boost their satellites and space stations again and again. However, at release, KSP2 players found
that if you orbited the right planets or moons at a low enough altitude, your orbit would decay. The KSP2
team diagnosed and fixed the issue, or so I thought. However, my first Minmus mission in For Science!
proved otherwise. Orbital decay is still an issue. Thankfully, you can prevent orbital decay by dropping
into timewarp when possible, but in my opinion, this should be a top-priority bug for the KSP2 team.

The other major bug that I've experienced is disappearing orbital lines. Normally, when you open the map
view, you see a line that marks your orbit. However, KSP2 has a bug that will sometimes set a craft's
state to "Landed" when it is clearly not landed. When a craft is in the Landed state, it does not have
orbital lines drawn. I experienced this during an extremely simple mission to low Kerbin orbit and back;
there were no quicksaves or quickloads in the mission. At some point during ascent, I realized that my
orbital lines were gone. Due to the simplicity of the mission, I was able to complete it without orbital
lines, but for more complex missions, you would have to quicksave and quickload to continue. However,
sometimes reloading the game doesn't fix the bug; in that case, you have to shut down the game and
manually edit the craft file to override the vessel state.

## The ugly

I have a few things to point out here that fall into the "ugly" category. First, camera behavior is buggy.
When you switch to the map view and back, the camera always resets its view. This isn't too bad by itself,
but the default camera view for large craft will often be clipped into the middle of the vehicle. This
means that every time you switch to the map and back, you have to zoom out a bunch to see where you even are.

Second, sometimes clouds render as black during the night. I will add a massive disclaimer that I only
saw this once, and I didn't try to reproduce it, but the fact remains that as I landed, the clouds were
completely black, which didn't happen before For Science!

The final ugly thing? Jebediah Kerman is no longer selected by default as the first kerbonaut on any
mission! What is this world coming to?!

## The takeaway

Is KSP2 ready for mass adoption by anyone and everyone? Until orbital decay and disappearing map lines are
fixed, no. I can't recommend a game that has such serious gameplay-affecting bugs. However, I am optimistic
that the KSP2 team is working on fixes for these and other problems. Who knows? I have a friend who's waiting
for KSP2 to have the bugs fixed before he dives into the Kerbal universe for the first time; maybe in a few
months I can tell him that KSP2 is ready!

## If you work on KSP2

If any KSP2 devs read this, first, thank you for making a great game! I personally have entirely switched
from KSP1 to KSP2. However, it'd be awesome if you'd pull the right strings to get those bugs fixed. :)

{{< chat thoughts-on-ksp2-for-science >}}
