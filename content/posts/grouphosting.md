+++
title = 'Grouphosting'
date = 2023-12-22T15:32:27-05:00
tags = ["selfhosting"]
+++

I've been diving into the world of selfhosting recently, but there's a catch: it's not just me. I'm doing it with some friends. This, of course, is fun, but I think the term "selfhosting" is a bit of a misnomer here; furthermore, I think that there is a powerful new type of selfhosting here that could change the way people selfhost.

## Grouphosting

I've been calling this concept "grouphosting". What is grouphosting? It's fairly similar to selfhosting, except there are multiple administrators. What advantages does this have? The immediately obvious answer is that server problems can (hopefully) be resolved more quickly; the more administrators you have in your group, the more likely it is that one of them will be available when the inevitable server problem occurs.

However, there's another powerful feature that is available only to grouphosters: resilient backups on a budget. In commercial environments where money is not a big problem and preserving your data is vital, sysadmins set up [3-2-1 backups](https://www.veritas.com/information-center/3-2-1-backup-rule) for maximum resiliency, which includes geographically separate data copies. Some companies already have multiple locations available to locate backup servers; others simply purchase storage from providers like Backblaze or AWS to give themselves a separate datacenter. However, for individual selfhosters, geographically redundant might mean "My server rack in the basement syncs to an old NAS in my attic."

When you grouphost, you are involving multiple people who live at separate geographical locations. Therefore, even if not everybody in the group is capable of hosting a server at their place, you hopefully will end up with at least two people in your group who are capable and willing to put a server in their house. At this point, all you need to do is provide the servers and you've achieved resilient backups without paying a cloud hosting company!

Closely related to this is the concept of load balancing. For the purposes of this blog post, let's say you're deploying Matrix. Matrix doesn't need a lot of resources for an instance that will only serve one user; a few gigs of RAM and a fairly low-power CPU would suffice. However, assuming you're selfhosting on a physical machine at your house, you probably have more than just a few gigs of RAM - even the Raspberry Pi 5 comes with a minimum of 4 GB. If you're using an old desktop or a dedicated server, you almost certainly will have 8 GB or more. This is plenty of resources to run a Matrix instance with more than just one user. By grouphosting, you can keep your machine just to a Matrix server, but now you'll be utilizing its extra capacity with accounts for your entire group, and in return you'll get access to the other services the group is hosting.

## I don't trust my friends on my server!

I understand exactly where you're coming from. It's good to keep integrity of your system at the forefront; if you aren't comfortable with grouphosting, it's your right to refuse others access to your server.

## What if I can't find anybody at all?

That's OK! Some people may need to just selfhost. I just think that it makes sense to grouphost when possible.

## I bought a domain that is based around my name. Does the whole group have to use it?

If they want to, sure; if they don't want to, tell them to buy their own domain. After all, you can get domains for a lot of great TLDs (including .com, .xyz, .dev, and .foo, to name a few) for around $10 per year or less, so it won't break the bank to share the cost of a domain.

Of course, you can always host your own stuff (like a blog) under any domain you want.

## OK, you've got me convinced. How should I start?

If you know anybody who selfhosts, ask them, but be prepared for rejection: they may not wish to migrate to a new domain. Otherwise, talk to all your friends that you would trust with your server. Once you've got a few people on board, buy yourself a server or two. I personally scored a well-equipped Dell PowerEdge R520, a server rack, and a few other things for $650 on Craigslist. Sites like eBay and [The Server Store](https://www.theserverstore.com) are also great resources for finding cheap used servers. Of course, you could also find cheap desktop PCs instead of rackmounted servers. [ServeTheHome](https://servethehome.com) is an awesome resource for finding good home server hardware, and they even have a [dedicated forum section](https://forums.servethehome.com/index.php?forums/great-deals.8/) for sharing great deals on server hardware.

Once you've got a server or two and have put them at people's homes, it's pretty similar to selfhosting. Just make sure to give the right people administrative access to your installed services. You should also probably write down some details of everything you install; that way, if anything crashes, any administrator can read your documentation to help troubleshoot the problem.

Once you're done setting up your server(s), don't just rest on your laurels. Be on the lookout for other potential grouphosters. If you have done your grouphosting right, you should be able to loop in a new grouphoster with minimal effort. Ideally, you should even be able to put the new guy's server to use, if he has one.

## Fin

I hope that at least some part of this made you go "yeah, that actually makes sense". While I doubt this post will have a huge impact, it might start some other person out into their own grouphosting, and if that happens, I'm happy. :)

{{< chat grouphosting >}}
