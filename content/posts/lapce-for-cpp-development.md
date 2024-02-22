+++
title = 'Lapce for C++ Development'
date = 2024-02-18T23:08:00-05:00
tags = ["ide", "review", "c++"]
images = ["images/lapce/lapce-alpaka.png"]
+++

On February 18, [Lapce](https://lapce.dev) hit [number one on Hacker News](https://news.ycombinator.com/item?id=39421090). I was immediately intrigued to see
a new cross-platform IDE show up that was *not* built on Electron. In fact, I was intrigued enough to download Lapce and take it for a spin (in fact, I'm writing
this post in Lapce).

My review will be influenced heavily by the fact that I nearly exclusively work with C++. I sometimes use QML in conjunction with C++, and I also really enjoy using
[D](https://dlang.org), but the fact remains that in both personal and work programming, I generally use C++. Therefore, I will mainly evaluate Lapce based on its
performance as a C++ IDE. Currently, I daily-drive Qt Creator, which is a fairly polished IDE that eschews fancy visuals in favor of building a highly functional IDE
for folks like myself who work with Qt. This means that Lapce will have a high bar to clear for me to actually consider adopting it as my default IDE.

Without further ado, let's get into the review!

## Getting started

Lapce has a download button on their website, so I figured I'd try their Linux download. I was expecting to maybe get links to .deb or .rpm files or maybe an AppImage;
instead, they provide a .tar.gz that contains a single binary file. Rather unorthodox, but it actually worked fine on openSUSE Tumbleweed, so obviously they've built
it right. However, I'd prefer to see them distribute either an AppImage (which can be properly installed using [AppImageLauncher](https://news.ycombinator.com/item?id=39421090))
or a .flatpakref file that points to a Flatpak repository. After all, Lapce is already [available on Flathub](https://flathub.org/apps/dev.lapce.lapce).

After running the binary from the .tar.gz, I decided to see if they had a native openSUSE package available; I was pleasantly surprised to find that there is a `lapce`
package available in the default Tumbleweed repo (for the uninitiated, openSUSE puts most software in the `openSUSE-Tumbleweed-Oss` repo, but some software is in other
official repositories; for example, many Matrix clients are in `network:messaging:matrix`). I installed the package and found that it did not remember my settings from
the .tar.gz version. This was highly disappointing; software on Linux should always store settings in the same paths. However, upon further inspection, I found that this
is actually a good thing: while openSUSE bundles the nightly version, the .tar.gz download is the latest stable build, so they store their configuration in separate
folders to avoid a nightly build breaking your development workflow.

## The UI

My first impressions when actually running the IDE can be summed up thusly: Lapce is trying to create a more-or-less drop-in replacement for Visual Studio Code, but
written in Rust. And as long as it's not using Electron, I count that as a win.

However, I'd like to get a bit more specific about the UI. I never have really appreciated the Visual Studio Code layout; I much prefer how Visual Studio or Qt Creator 
organize the UI. Now, Lapce does a pretty good job at looking OK, but I do have a fair number of nitpicks. I've categorized them to make them seem slightly more coherent.

### Actually editing text

It's shockingly easy to lose focus on the editor, and when you do, keyboard inputs won't work like you would expect. For example, if you use the resize handle on the
file view, the editor will stop recieving input until you click somewhere else in the UI. Even global shortcuts (like Save or the command palette) don't work.

Lapce doesn't support the normal middle-click paste functionality that is generally supported on Linux. This is a huge deal-breaker for me, as I use this all the time.
Another essential text editing feature that is not implemented is dragging selected text. I suppose this may be because Lapce is using a [custom UI toolkit](https://github.com/lapce/floem)
that may not have implemented text dragging yet.

### Theme problems

The default theme has very similar colors for the general background and the editor background. This, when combined with the minimalist design, makes it rather hard to
differentiate between your file content and the file sidebar, especially when you are working with a Markdown file that is mainly white text.

### File sidebar issues

The file sidebar has a number of issues that I want to point out. First, the sidebar is laid out in two sections. At the top is open editors; at the bottom is your
filesystem view. I am used to this being the other way around; unfortunately, I can't find any way to change this. To make matters worse, you can't even resize the
two sections. The default sizing leaves me able to see only three open files at a time. Moving on, there are only two icons used in the sidebar by default: one for
folders and one for files. Thankfully, there is one extension that adds new icons (Material-themed) to Lapce, which add unique and colorful icons for many file types,
but the default experience makes it extremely difficult to find files. To make matters worse, if a file has a name long enough to start overflowing the file explorer,
Lapce will shrink and then remove the icon for the file before it will [elide](https://doc.qt.io/qt-5/qtwidgets-widgets-elidedlabel-example.html) the file name. This 
makes it harder to distinguish files while providing no real benefit in terms of file name readability.

### The command palette

The command palette is unusual, to say the least. To begin with, it uses `Ctrl+Shift+P`, which is a bit unwieldy, and simultaneously uses... `F1`?! `F1` is recognized
everywhere as the de facto Help key, not a command palette key! I tried rebinding to the more familiar and easier `Ctrl+K`, but it didn't work. It turns out that
`Ctrl+K` is used for shortcut layering. Fine, I thought, I'll use `Ctrl+K Ctrl+K`. No dice. I eventually settled on `Ctrl+Shift+K`, but it feels like a suboptimal
solution. Once I had my shortcuts in place, I found that your shortcut(s) will only open the command palette; to close it, you have to use Esc. This is bad design;
menus should be closable with the same key that opens them (see: the Windows Start menu). Once you get the command palette open, it types a colon at the beginning,
which limits you to commands. To search files, you have to remove the colon. Given that my main use for this sort of global search bar feature is quickly jumping to
files or symbols, I'm not impressed. Finally, if you click on the command palette bar, you can't type anything into it, not even Esc. You have to click outside the
palette to close it and then open it with the shortcut.

### Miscellaneous UI issues

While the file sidebar shows your open editors, there is also a tab bar at the top of the window with your open files. To me, this is a waste of precious vertical
space that could be used to show more code. After all, in IDEs, vertical space is precious, and I don't want to sacrifice visible lines of code to add a redundant
open files view. On that note, Lapce scores poorly on visible lines of code. It shows just 52 lines at a time, while Visual Studio Code manages 64 and Qt Creator
gets 66 (although I may have changed font sizes in the other two IDEs; I don't remember). After changing the font size from the default 13 points down to a
still-readable 12, Lapce still only has 58 visible lines of code.

There is a general lack of tooltips throughout the UI that makes it difficult to know what every button will do. Again, this may be due to the custom UI toolkit.

## Markdown editing
  
This post was written in Lapce as I worked with the IDE; this gave me a great opportunity to try out Lapce's Markdown support. Overall, it looks pretty nice. There's
good syntax highlighting, code blocks that have a content language specified have appropriate syntax highlighting applied to the contents, and there is even support
for the TOML front matter that Hugo generates. However, I do have a few complaints:

Generally, IDEs don't use word-wrapping by default, since it would make code extremely difficult to read. However, when you are working with Markdown, you're not
working with carefully formatted code. You're working with paragraphs of text. And when you are working with paragraphs of text, word-wrapping makes it easier to read.
Visual Studio Code word-wraps Markdown files for this reason. However, Lapce does not word-wrap Markdown. I'd love to see this fixed.

Inline code is very difficult to distinguish from regular text; the backticks are highlighted but the contents are not. I'd love to see the contents have either a
different text color or a unique background color.

List handling is so-so; while most editors will auto-generate the next list item when you press Enter, Lapce does not.

Finally, Lapce has no Markdown preview support. To me, this isn't a huge deal, but for some people it would probably be a deal-breaker.

## C++ development

Upon opening a C++ project, I immediately noticed that Lapce supports C++ syntax out of the box. Nearly every character in every file has coloring applied to it. Some
symbols such as braces, brackets, parentheses, and operators remain the default text color, but every identifier and value in the source has a color applied. As somebody
who appreciates the value of colors to better communicate what's going on in code, I am a huge fan of this. (In fact, I have changed the Qt Creator dark syntax theme to
add more color; you can get it [here](/files/qt-creator-dark-colorful.xml).)

Unfortunately, there was not much more than syntax highlighting available. After searching fruitlessly for a menu entry to allow me to manage extensions, however, I
found the extensions button at the top of the file view. Sure enough, there was a clangd extension available, so I installed it and... nothing changed, except for an
error popup declaring that the clangd extension couldn't retrieve an artifact. What? I eventually learned that it was trying (and apparently failing) to download clangd.
Fortunately, you can configure it with the path to your already-installed clangd. After doing so, I was rewarded with a fairly usable C++ experience. Here's how Lapce
looks with the code for [Alpaka](https://invent.kde.org/utilities/alpaka) open:

![The Alpaka source code open in Lapce](https://lorendb.dev/images/lapce/lapce-alpaka.png)

However, it wasn't all smooth sailing. I immediately realized that Lapce displays [inlay hints](https://www.jetbrains.com/help/idea/inlay-hints.html) when it has enough
information available to do so. I find inlay hints incredibly annoying, so I had to uncover the setting to turn them off. Another unfortunate realization was that the C++
syntax highlighting became less colorful with clangd enabled; however, there is still plenty of color in the IDE, so I don't feel like that is a huge problem. Code
completion was another rough patch; while Lapce gave me plenty of completions from clangd, I found that it wasn't completely intelligent about doing so. For example,
typing `case Foo:` in a switch statement would trigger a completion popup suggesting a variable. Normally, I would expect to be able to type a newline before any
completion popups display; with Lapce, however, I have to press Esc after typing the colon.

It quickly became obvious to me that Lapce is not designed to create an extremely powerful C++ experience. As a simple example, while Qt Creator allows you to switch
between headers and source files, there is no such functionality in Lapce. However, for a more impactful example, let's look at the C++ build environment support. In
IDEs like Lapce and Visual Studio Code, you are not given project support. You are instead given support for run configurations, which (in the case of Lapce) are TOML
files that can contain commands for running apps. In my opinion, when you already have build systems like CMake, Meson, and Cargo that are designed to handle this for
you, I shouldn't have to specify this stuff myself. I should be able to simply have Lapce run `cmake --build` on my code when I ask it to build my app.

With all this being said, I realize that I'm giving Lapce a bit of a hard time here. It's not intended to be a supercharged C++ IDE; it aims to be a replacement for Visual
Studio Code, and it does well at that. It's just that Visual Studio Code and similar editors have introduced a paradigm that I don't appreciate.

## Final assessment

Lapce is a promising IDE, and for a [hobby project](https://news.ycombinator.com/item?id=39423493) it's at an impressive level of completion. Since it is clearly marked
as being in beta, I am willing to forgive a lot of problems as bugs that are planned to be fixed before a 1.0 release.

However, I will not be switching to Lapce due to its overall lackluster C++ experience. Qt Creator provides a much more powerful experience for me; in fact, the only real
gripe I have with Qt Creator is that it doesn't support languages other than C++ and QML very well. This could technically be fixed by creating plugins for other languages,
but Qt Creator's plugin interface is not very well documented.

I do plan to keep an eye on Lapce. I dislike Visual Studio Code for its use of Electron and its invasive telemetry. If Lapce improves enough and gets a D plugin, I'll
probably switch my D developement to Lapce so I can ditch Visual Studio Code once and for all.

Finally, I want to give a shoutout to the Lapce developers for creating such a cool (and gorgeous) app. If any of the developers reads this, please forgive the ranty
tone of voice and take this as constructive criticism :)

{{< chat lapce-for-cpp-development >}}
