+++
title = "The year of Linux on the desktop, and why we can't have nice things"
description = "Maybe 2024 will finally be the year of Linux on the desktop, but I'm not holding my breath."
date = 2024-03-19T15:32:00-04:00
tags = ["rant", "opensource"]
images = []
+++

Ever since the dawn of time (er, Linux), enthusiastic users have predicted the advent of the year of Linux on the desktop. Hardly a year has gone by without somebody proclaiming it to be such a year. And yet Linux only [recently crossed 4%](https://arstechnica.com/gadgets/2024/03/linux-continues-growing-market-share-reaches-4-of-desktops/) of desktop market share worldwide. What gives?

On the surface, it's easy to point to different reasons. Maybe it's the [overwhelming sea of distros](https://en.wikipedia.org/wiki/List_of_Linux_distributions), or the lack of official support for Adobe (or whatever proprietary Windows-only software you use), or the gaps in the user experience that leave you poking around in the terminal, wondering where it all went wrong. Maybe it's even the lack of prebuilt Linux PCs from major manufacturers. These are all valid arguments, but I think there's a deeper problem here.

> Heads up - this post is a bit of a rant. Proceed at your own risk; I provide absolutely no guarantee that you will agree with the stated sentiments.
>
> Also, pedants likely will notice that I'm not treating ChromeOS as Linux. This is intentional; the Linux experience that everybody wants to see become popular doesn't include Google accounts and spyware throughout the OS. Instead, I'm using the term Linux to refer to the more traditional distros.

## Opensource is not capitalism

It's no secret that opensource software is a very socialist way to do things. For example, companies whose product is opensource tend to make their money by selling hosted versions of their software or providing paid support for their products. Unfortunately for Linux, this directly conflicts with the capitalist economy that most of the world runs on. Why is this unfortunate for Linux? Because it drives down contributions.

Now, I can hear you screaming at me already: "But everybody hires kernel developers nowadays! Even Microsoft funds kernel developers!" And you're right! However, you need to remember a few things. First, developers from, say, Microsoft are only concerned about kernel development that directly impacts Microsoft. This practically means that Microsoft's kernel involvement is limited to making [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux) possible and supporting their custom Linux distro that they use for Azure. Beyond that, they have little incentive to get involved. This means that while the kernel does get plenty of attention, there's also potential for resources to be focused in areas that don't directly benefit many users.

Second, and perhaps more importantly, Linux is not just the kernel. While I don't want to fan the flames of the [GNU/Linux](https://stallman-copypasta.github.io) warriors, their arguments do point to the underlying issue here: while the kernel provides a rock-solid base for your operating system, there are dozens of other components that make up a Linux OS (init system, desktop environment, network connection managers, etc.). Every one of those components must itself receive maintenance in order for your system to run smoothly.

The question, then, is "who maintains all of this software?" Sure, there are plenty of volunteers out there who are looking to fix bugs on their systems or just have a fun time writing code, but you can't count on a volunteer to provide forty hours of programming a week. To build a truly polished and reliable product, paid development is nearly essential. You may disagree with me, but I'd like to point out that much of the polished Linux software we love has had developers behind it paid to work on it: besides the kernel itself, there's systemd, many KDE components, much of Pop_OS!, and many other projects, not to mention applications like Firefox, Element, and Visual Studio Code, all of which are direct products of companies instead of being volunteer projects.

At this point, you're probably wondering where I'm going with this. Well, if you'll [bear with me](https://xkcd.com/365/), I promise it will all make sense in a bit.

## This is why we can't have nice things

If so many companies are already investing in Linux and the wider opensource ecosystem, why hasn't the world switched to Linux? To understand that, let's go back to what I said earlier about Linux kernel development:

> Developers from, say, Microsoft are only concerned about kernel development that directly impacts Microsoft.

And therein lies the rub. While we see many companies embracing opensource, nearly all of them end up being like, say, Signal: they provide value to the ecosystem and create an incredible product, but at the end of the day, they are only investing into the apps part of the Linux ecosystem. I'm certainly not trying to downplay what companies like Signal are doing, but at the end of the day, the core system components are left without support.

This is not to say that core software is without support; again, volunteer effort goes a long way to keep the lights on, and companies like [Blue Systems](https://blue-systems.com) (who work on KDE) and [System76](https://system76.com) (who have their own distro and have contributed massively to the entire opensource stack, including Coreboot) are doing amazing things to supplement volunteer work with commercially-funded development. However, the number of companies paying developers to work on projects like KDE is vastly lower than the number of companies paying developers to work on the kernel.

In my opinion, this lack of commercial support is what leads to the rough edges in the Linux experience. If you want to see what I mean, the best way is to try a volunteer-built distro like Debian or Manjaro against a commercially-built distro like Zorin OS or Pop_OS!. The distros backed by for-profit companies have had much more work put into making the best experience possible, whereas the volunteer distros largely depend on upstream development to identify and fix rough edges.

However, with most opensource companies focused on building only one or two apps rather than funding an ecosystem, we can't fix the problem overnight.

## The bright light is... Valve?

Interestingly enough, the trail to fixing this ecosystem problem may have been blazed by a gaming company. That's right, Valve is back to save the day again, but to understand why, we must first look away from Valve.

If commercially-backed development only happens when a company has an incentive to provide that development, we need to figure out what companies would directly benefit from a better Linux desktop experience. We don't have to look far, though. As I mentioned above, System76 has poured lots of time and effort into developing the best Linux experience for their hardware, going so far as to create their own distro (and they're building their own desktop environment, too!). But it doesn't have to be just System76. It could be any company selling a Linux PC.

Unfortunately, there's not a lot of those out there today. But that could change in the future! Specifically, Lenovo is a prime candidate to become a major provider of Linux systems. A few months ago, the user mindcrime [revealed](https://news.ycombinator.com/item?id=39137536) in a Hacker News thread that Lenovo prefers Linux:

> Lenovo secretly want Linux to catch on big-time, because they HATE the amount of leverage Microsoft have over them due to the ubiquity of Windows on so many of their devices. But they have to be careful to not embrace Linux too openly for fear of Microsoft retaliation. So they're in a bit of a catch-22 situation. \[...]
>
> - [mindcrime](https://news.ycombinator.com/user?id=mindcrime)

If mindcrime is right, Lenovo would be eager to produce Linux hardware the moment Linux seemed to be gaining significant market share (and indeed, [they already do make some Linux machines](https://www.lenovo.com/us/en/d/linux-laptops-desktops/)). However, Lenovo is going to need something more than a report of 4% Linux usage worldwide from Statcounter before they pull the trigger on Linux devices. And for that something, we need to look to Valve.

Valve has a long history with Linux. For ten years, they've used it as the base of SteamOS, and their games have supported Linux for many years as well. However, Valve wasn't content to stop there. They currently are supporting development of KDE to run on the Steam Deck. Futhermore, they've invested heavily into development of Proton to allow Windows-only games to run on Linux, and according to [ProtonDB](https://www.protondb.com), fully 40% of games released on Steam are known to be partially or entirely playable on Linux.

The thing is, Proton is just a soft fork of Wine, which is aimed at running any Windows software on Linux, and most (if not all) Proton code is ultimately upstreamed into Wine proper. In other words, Valve's work on Proton is just another ecosystem investment. Like their work on KDE, their Wine contributions have the capability to unlock Linux for everyone; if and when Wine gets good enough to run nearly all Windows software, including major products like Adobe or Office, with few to no glitches, we'll have one less blocker for potential new Linux users, and it'll be due in no small part to Valve.

How does this relate to Lenovo? In my opinion, there are two major blockers for Lenovo (or other OEMs) to adopt Linux: app support and ease of use. Valve is helping on the app support front by developing Proton and on the ease of use front by contributing to KDE. Without major OEM contributions, I foresee Valve being the primary driver of these efforts until OEMs start pushing Linux big-time. In other words, Valve may provide the impetus that drives Linux popularity up to the point that Lenovo feels ready to publicly move toward Linux.

It's also worth pointing out that we shouldn't expect OEMs to freeload on Linux. Granted, there will probably be smaller system integrators that will simply OEM-install Ubuntu with their own wallpaper and call it a day, but larger system integrators will almost certainly invest money into the ecosystem. How can I predict that so confidently? Because OEMs already spend enormous amounts of money on Windows.

Let's take a closer look at the numbers. According to [Gartner](https://www.gartner.com/en/newsroom/press-releases/01-10-2024-gartner-says-worldwide-pc-shipments-increased-zero-point-three-percent-in-fourth-quarter-of-2023-but-declined-fourteen-point-eight-percent-for-the-year) (table 3), in 2023, Lenovo alone shipped 59.7 million PCs. I think it's safe to assume that most of them were running Windows, but to be conservative, let's say just 40 million of those PCs shipped with an active copy of Windows (maybe the other 20 million were running ChromeOS). I wasn't able to find what large OEMs pay per Windows license (drop a comment below if you know!), but let's choose an absurdly low number for sake of argument. Assuming Lenovo pays just $5 per Windows OEM key, that works out to $200 million going to Microsoft per year. That's enough to hire 1,000 developers and pay them $200,000 per year to work on the Linux ecosystem. (I'm extrapolating potential results per ) If we adjust the OEM license key price to even $20 (which still seems like a very low estimate), the developer count jumps to 4,000. And $200,000 is a pretty good salary; I'm sure there are many developers who would be happy to work full-time on opensource for much less than that.

Granted, Lenovo will probably take the opportunity to recoup some of the licensing costs as profit instead of reinvesting that money, and they also won't entirely abandon Windows, but imagine the effect that even 100 full-time developers could have on your favorite desktop environment. And now remember that there are other manufacturers that will likely jump on board; looking at Gartner's data, the top five Windows PC manufacturers shipped a combined 185.8 million PCs in 2023. Given a $20 OEM license cost and assuming just half of that money gets redirected to Linux development at $150,000 per developer, OEMs could hire over twelve thousand developers! A developer army of that size would undoubtedly make short work of bug trackers everywhere.

## So which year will Linux take over?

It's tough to predict when Linux will truly take off. I do think that it has a strong chance to become dominant in the future once Microsoft [switches Windows to a cloud service](https://www.theverge.com/2023/6/27/23775117/microsoft-windows-11-cloud-consumer-strategy), as many people won't want to hand over their PC to Microsoft or pay a subscription to use Windows (although Google will probably attempt to push ChromeOS into the gap left by Microsoft). However, could it rise sooner than that?

Microsoft's cloud PC ambitions are likely many years away from being fully realized, so we must look to alternate ways that Linux could rise. As I mentioned above, Valve's investments are pushing the Linux ecosystem into a place where it can serve most users' needs. For example, many gamers are learning that Linux actually works just fine on their Steam Decks and can even run their games on their PCs. This could definitely help push Linux upward; however, we're still missing a major piece of the puzzle.

If you walk into your local Best Buy, Walmart, or Costco and have a look at the various computers displayed, you'll notice an overwhelming selection of Windows machines with a few Macs and Chromebooks thrown in. There are no machines with Linux installed. Ditto for web retailers. While you can find Linux machines on some websites, they are almost never shown as front-page products, and many retailers just don't carry Linux machines at all. This means that most users don't even get a chance to evaluate Linux as an option.

For those who are interested in Linux, then, there are two options: learn how to create a virtual machine or figure out how to boot from a Linux USB and install it. To be fair, the Linux installation experience has had an incredible amount of work put into it, and it's extremely polished and easy to use (in my opinion, better than the Windows experience), but it's still beyond the range of many people's technical ability.

Linux will suffer from this deemphasizing effect until some company steps up to provide Linux computers that are promoted at common retailers. To be honest, Valve could save us again here: if they built and sold a line of gaming PCs preloaded with a desktop-oriented version of SteamOS, desktop Linux would likely get into the hands of hundreds of thousands of gamers. Valve wouldn't even have to build the PCs; even if some OEM had Valve slap a Steam logo on a gaming PC with SteamOS installed, we'd likely see a massive uptick in Linux users.

Of course, stores like Walmart tend to stock low- to mid-range PCs intended for casual use rather than dedicated gaming, so the Valve approach might not work well for getting Linux PCs into stores. Unfortunately, this is going to be a little harder to overcome, but I think there's another problem we've been overlooking this whole time: today's prebuilt Linux systems are almost all high-end workstations. For example, with the exception of the Meerkat mini PC, System76 doesn't sell desktops or laptops for less than $1,000. With many consumers being constrained by a tight budget, then, System76 is out of the question. However, if we could get something like the [Pinebook Pro](https://pine64.org/devices/pinebook_pro/) (but perhaps built with an i3 or similar processor) into the hands of consumers, it would be very appealing. I think that a low-end Linux laptop could easily be built for $300 or less without majorly sacrificing build quality; after all, the Pinebook Pro has a magnesium alloy chassis and is still only a little over $200.

Finally, I think it's worth looking at what I'm going to call the Hail Mary approach. In this approach, a major OEM like Lenovo would invest millions of dollars into a fast-track Linux development campaign, with the goal of creating a distro with a frictionless experience in just a year's time. From the development side, I think this would be possible; if they used an already solid distro for the base (say, Fedora or openSUSE) and picked Plasma for the UI, they are already at least 85% of the way to a beginner-friendly product. Their year of development could mainly focus on removing rough edges, implementing a few missing features, and generally making sure the experience was absolutely competitive with Windows.

However, from a business perspective, the Hail Mary approach is extremely risky. Unless it was extremely well cloaked, Microsoft would quickly learn that Lenovo was about to ditch Windows. If Microsoft decided to immediately cut off business relations, Lenovo would be left unable to ship either Windows machines or polished Linux machines for quite a while. This kind of risk means that a Hail Mary will almost certainly not happen.

With all things considered, then, I predict that while the year of the Linux desktop has a high chance of happening, it will almost certainly not happen in the next three to five years. For that to happen, we'd need Microsoft to go all-in on Windows 365 within a few years from now, as well as large efforts from at least one major player to develop the Linux ecosystem.

## A few ways we can game the system

First off, if you can, please consider contributing to your favorite opensource product to help fill the gap. For example, I have made a few contributions to KDE in the form of code; you may prefer to pick a different desktop environment, work on an application, or even contribute by helping with non-programming tasks (documentation, bug triage, etc.) or donating money.

However, I think there's another way we could push Linux faster. If some enterprising person launches a Linux computer startup that focuses heavily on marketing, strikes deals with retailers to promote their computers, and ships a polished distro that will make the transition from Windows as easy as possible, I think there is a real possibility that Linux on the desktop could happen sooner than we expect. Unfortunately, I don't know of any tech billionares who are looking for a cool side project, but if that describes you, you're welcome to talk to me about it!

## Hang in there!

Regardless of the difficulties that we face, Linux will almost certainly get its time in the limelight within the next ten years. It's already showing steady growth, especially in India, where it commands 15% market share, and it ultimately beats its opponents in nearly every way. Unlike Windows, it's lean and mean (and developer-friendly); unlike macOS, it's completely under your control and allows freedom of hardware choice; unlike ChromeOS (and Windows and macOS), it's privacy-respecting, so your daily computer usage won't be sent off to Big Tech's servers. Finally, despite its reliance on volunteer contributions, it provides a remarkably stable and polished experience already. With this combination of features, it's just a matter of time before people start to switch, and once global Linux adoption reaches a certain critical mass, OEM adoption and contribution will do the rest.

{{< chat the-year-of-linux-on-the-desktop >}}
