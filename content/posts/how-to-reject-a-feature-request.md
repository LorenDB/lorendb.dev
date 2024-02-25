+++
title = 'How to reject a feature request'
date = 2024-02-21T21:40:00-05:00
tags = ["opensource"]
description = "Surprisingly, you probably should be nice to other people."
+++

Open-source maintainers go through a lot. They have to deal with users who feel entitled to having their bugs fixed before the other bugs, contributers who submit pull requests of
subpar quality, and loads and loads of duplicate issues. Given all this, it's understandable that maintainers sometimes feel overwhelmed or lash out at those they feel are being
unreasonable (for example, Linus Torvalds famously [stepped down](https://lkml.org/lkml/2018/9/16/167) to address his abusive behavior, and still occasionally sends a heated email).

However, it is in everybody's best interest that maintainters stay calm and respectful. Abusive maintainers will quickly lead to projects being abandoned by both contributers and
users, which reduces the quality of open-source software in general.

> A quick note here: I don't currently maintain any large open-source projects. However, I've watched and experienced feature request rejections in various projects, and I feel like
  this is something that everybody should be reminded of periodically.

It is inevitable that a user will someday request a feature that you don't like. For example, I have observed users repeatedly requesting that [nheko](https://github.com/nheko-reborn/nheko)
gain the capability to show link previews. The maintainer has repeatedly rejected those requests on grounds that it reduces privacy (by giving your IP address to any URL that
somebody sends you, even if you don't open the URL yourself).

## The wrong way to reject a feature request

You may be tempted to tell the user off for suggesting such a clearly flawed and stupid feature. Why are you wasting my time? I'm not going to spend my evening working on a feature
that nobody but you will ever use! If you want it so bad, why don't you just fork the project and implement it yourself?

Here's the problem: you're establishing yourself as an opinionated, self-centered person who will only implement features that you will use yourself. Now, to be clear, it's not wrong
to implement features just for yourself. This is referred to as [annoyance-driven development](https://nheko.io/notes/9n4dz70ktmu0ozzu). However, a good maintainer will put some
effort into implementing features to make life easier for others.

## The right way to reject a feature request

Instead, you should politely reject the request with a clear explanation of why you think it's a bad idea. It's fine to suggest forking the project, but you should refrain from doing
that in most cases. Instead, invite them to contribute the feature themselves.

You should never reject a feature across the board unless it has serious ethical, privacy, or security concerns or if it would require a ridiculous amount of refactoring for a feature
that would affect a very small percentage of your users. Here's why: even if you refuse to implement the feature yourself, leaving that feature as a possibility could lead to a new
contributor. Once they have added the feature, you don't need to touch the code again (unless it breaks) or use that feature at all. However, some of your other users have gained a
new feature, and your software has become more compelling for new users.

## When it's OK to be a bit grouchy

Sometimes users persist in being unreasonable. Let's look at a real-life situation where a feature request was declined, but the user kept pestering the maintainer about it.

It turns out that there is a tiny group of Linux users who believe that [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/) is bloated, a security risk, and otherwise a bad idea.
One of them, @kyronsaif, [opened an issue](https://github.com/Nheko-Reborn/nheko/issues/1390) asking if D-Bus could be dropped as a dependency of nheko, where it is currently used to
show notifications on Linux and expose a useful API for other applications. The maintainer, @deepbluev7, [replied](https://github.com/Nheko-Reborn/nheko/issues/1390#issuecomment-1445040189)
with a calm, rational explanation of why nheko depends on D-Bus and why the dependency won't be dropped. Case closed... until it wasn't.

The next day, user @Tcll [opened another issue](https://github.com/Nheko-Reborn/nheko/issues/1391) asking to remove D-Bus. This issue is less of a request and more of a tirade
against D-Bus. This can be observed in multiple places. First, @Tcll says:

> I haven't found any alternative implementations of dbus
  (I personally don't care about integrations enough to look)
  
This indicates a lack of effort put into the issue. While @Tcll clearly cares enough to create an issue, there is little to no effort put into giving reasonable alternatives to
D-Bus. Later, @Tcll says:

> it breaks nemo [editor's note: nemo is a Linux file manager]

and

> other apps automatically try to integrate with it to do more than they should have to

Neither of these are issues with D-Bus, and neither of these have anything to do with nheko using D-Bus.

@deepbluev7 [responded](https://github.com/Nheko-Reborn/nheko/issues/1391#issuecomment-1445154982) with an emphatic message reiterating the reasoning from the previous issue and then
closed the issue. @Tcll fired back with a message filled with incorrect claims ("sad that the user has to be forced to build this because Linux just can't be easy to use"), lack of
willingness to put forth any personal effort ("don't ask me to spend time learning your code structure though, you already know your structure"), and personal attacks on @deepbluev7
("way to be a bad dev letting your personal preferences get in the way of others..."). 

For the sake of brevity, I won't continue to provide a play-by-play of the entire issue thread, since most of it continued in this vein. However, I will point out that every time
@deepbluev7 replied, his replies continued to contain rational reasons for rejecting the feature request. His answers do start sounding more and more frustrated:

> Any user can do anything they want with Nheko (apart from what the GPL disallows), but no user can force me to do anything or restrict me from spending my time on more useful things.
  FOSS is about collaboration, not about making others do stuff for you. And it is common and even beneficial in FOSS to just fork and implement what you like there and then have upstream
  pull it, when they think it is useful. I think you are seriously misunderstanding something here.
  
but he continues to retain a respectful tone throughout, even mentioning an alternative solution for @Tcll:

> I agree. Maintaining multiple implementations on how to do everything in Nheko as well as ways to switch between those at runtime is probably much more overhead to maintain, than just
  having multiple clients. There are actually several other clients, some of them might fit your requirements better!

As the discussion begins to taper off, @Tcll acknowledges @deepbluev7's handling of the situation:

> at least that's a more reasonable answer than "build it yourself" or other flack like I commonly get

> at least this was a much more respectful issue, and I love it when things go this way
  even if neither of us can reach a conclusion, I'd rather it end with respect than enemies being made out of it

Clearly, some of @deepbluev7's responses would not be appropriate if this were a corporate support desk, but this isn't a corporate support desk. This is an individual volunteer whose personal
time is being taken up by this discussion, and the resulting frustration is completely understandable. However, at no point did @deepbluev7 veer into the realm of blatant name-calling or
violent attacks.

## In conclusion

Maintainers must retain a certain level of professionalism in dealing with users. However, as long as you aren't running an open-source company, it's fine if you aren't entirely
professional. Nobody expects [a random guy fron Nebraska](https://xkcd.com/2347/) to behave as if he were the support staff for a Big Tech company, and neither do they expect you to
behave that way. Just stay respectful to your users, even if they are a bit unreasonable, and you'll find that others will respect you for it.
  
Also, if @deepbluev7, @kyronsaif, or @Tcll read this post, please don't take offense at being used as an example!
  
{{< chat how-to-reject-a-feature-request >}}
  
