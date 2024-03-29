+++
title = 'Archiving Steam games for fun and profit'
date = 2024-01-03T23:27:24-05:00
tags = ["gaming", "selfhosting", "data-hoarding"]
description = "Why do I do this to myself? I have no idea. Anyway, let's learn how to eat disk space."
+++

> tldr: if you want to create an archive of all your Steam games, check out [download-steam-games](https://github.com/LorenDB/download-steam-games) on my GitHub. It has minimal documentation, but you should be able to get it running without too much trouble.

I recently decided it would be cool to download some beta versions of [KSP2](https://www.kerbalspaceprogram.com/games-kerbal-space-program-2) so I can revisit them in a year or so and laugh about how many terrible bugs there were in the original version of the game. I decided that it would make sense to download the game backup directly to my server, so I `ssh`ed into the server, installed Steam from the system repositories, and started a new `ssh -X` session. However, when I launched Steam, I realized that my server's connection to the internet is much too slow to run `ssh -X` in any sort of usable state. In fact, it was so slow that it apparently timed something out on Steam's end and made it practically impossible to log in to Steam.

Undaunted, I started looking into [SteamCMD](https://developer.valvesoftware.com/wiki/SteamCMD). It turns out that while SteamCMD is intended for game server administrators, it will happily download any Steam game. It didn't take me long to install it and use it to download a game. However, it's a bit of a pain running through a set of instructions in SteamCMD again and again for downloading multiple games, so I set out to script it.

## But why would you do this?

Why wouldn't I do this? If you think I'm crazy, just go read [r/DataHoarder](https://www.reddit.com/r/DataHoarder/) for a while.

Seriously, while I don't expect Steam to disappear tomorrow, there is precedent for games to be removed from the platform; many of the original games on the platform have been removed. The oldest game still available (counting by which date it was made available on Steam) is the original [Counterstrike](https://store.steampowered.com/app/10) with a game ID of 10. Games 1 through 9 aren't on the platform anymore, and I'm sure they are far from the only games deleted from the platform. Archiving games allows me to avoid losing games that are removed from Steam. Also, sometimes enthusiasts like to be able to access old versions of games; one of the top posts of all time on r/DataHoarder is about [somebody who hoarded an old Minecraft alpha](https://www.reddit.com/r/DataHoarder/comments/o9cnj3/one_womans_quest_to_never_delete_anything_allowed/), giving Minecraft enthusiasts a chance at taking a more complete look at the evolution of the game.

> Update (2/23/2024): it turns out that I am wrong on this point. Games may be delisted from Steam, but they are not deleted. If you own the game, you can still download it. Also, since Steam numbers games by tens, the original Counterstrike *is* the first listed game on Steam. Apologies for this!

## A naïve bash script

My first approach was to create a bash script to wrap SteamCMD with a nice syntax. Of course, I also wanted to bypass the interactive prompt. Let's go through what I ended up building:

``` bash
STEAM_ACCOUNT=
GAME_NAME=
GAME_STEAM_ID=
GAME_FORCE_WINDOWS=
GAME_BETA=
```

These variables control some basic stuff. `STEAM_ACCOUNT` is the account you logged into SteamCMD with; while SteamCMD caches credentials, you still have to issue the `login <username>` command every time you start it, so the script has to know what username to use. `GAME_NAME` doesn't have to be the actual game name; it's just used to generate the filename for the final game archive. `GAME_STEAM_ID` is the numerical ID of the game you want to download; for example, KSP2 is [`954850`](https://store.steampowered.com/app/954850/Kerbal_Space_Program_2/).

`GAME_FORCE_WINDOWS` deserves a more detailed explanation. While I run Linux, some games do not have Linux builds available. For those games, I would like to save a backup of the Windows build instead. This will let me run the game via Proton later. Conveniently, SteamCMD allows you to override what platform you download the game for by setting `@sSteamCmdForcePlatformType <platform>`. `GAME_FORCE_WINDOWS` is used later as part of the SteamCMD script contents; therefore, if I want to add a Windows override, I can set `GAME_FORCE_WINDOWS` to `@sSteamCmdForcePlatformType windows`.

Finally, we have `GAME_BETA`. [SteamDB](https://steamdb.info) shows beta version identifiers for games; you can use those to download earlier versions of some games. This is something I want to use, given that the original purpose of this project was to archive early KSP2 builds.

The next bit of the script is simply logic to set each of these variables based on command line parameters. I've decided to skip it for brevity, as it is trivial bash logic. Moving on, we get this:

``` bash
cat > ~/.download-$GAME_NAME.txt << EOF
$GAME_FORCE_WINDOWS
force_install_dir $HOME/Steam/downloads/$GAME_NAME
login $STEAM_ACCOUNT
app_update $GAME_STEAM_ID $GAME_BETA validate
quit
EOF
```

SteamCMD can be controlled by scripts. This is how we're going to get around its interactive interface.

The first line of the script is the aforementioned platform override. After that, we use `force_install_dir` to force SteamCMD to download the game to a known location. In this version of the script, I've hardcoded it to `~/Steam/downloads`, since I've installed SteamCMD to `~/Steam`. Then we log in and issue the actual command to grab the app; once the app has downloaded, we quit SteamCMD.

``` bash
PREVIOUS_DIR=$(pwd)
cd ~/Steam
~/Steam/steamcmd.sh +runscript $HOME/.download-$GAME_NAME.txt
cd $PREVIOUS_DIR
rm ~/.download-$GAME_NAME.txt
```

Here we actually execute the script and then delete it to prevent clutter.

``` bash
PREVIOUS_DIR=$(pwd)
cd ~/Steam/downloads
tar --use-compress-program=pigz -cf $PREVIOUS_DIR/$GAME_NAME.tar.gz $GAME_NAME
cd $PREVIOUS_DIR

rm -r $HOME/Steam/downloads/$GAME_NAME
```

The final step is to compress the game folder into a single `.tar.gz` file. Again, we're dumping the final archive into `~/Steam/downloads` for now. The only really unusual part of this is that I'm telling `tar` to use `pigz` to do the gzip compression. This is because `pigz` will use all CPU cores, which is important when you're archiving many gigabytes of game data.

This is all fine and good, but you still have to manually run the command for each game. To mitigate this, I created a [simple script](https://paste.segfault.foo/loren/ac1cfff462804e70bdf85fb764328b19) that read a vaguely CSV-like file which listed games to download. That was definitely better than nothing, but it still felt pretty hacky.

## Building it the right way

I decided to port my two scripts into one app. I chose to write the app in [D](https://dlang.org); in retrospect, Python would probably have been a good choice simply because it comes preinstalled on pretty much every distro, but I much prefer D for pretty much anything. My ported script is pretty similar to the functionality shown above; however, I added various improvements. Paths are no longer hardcoded, it's possible to download games for any and all platforms (Windows, macOS, and Linux), and game data is stored as a JSON file instead of in a weird custom-ish format.

The app, which I'm calling `download-steam-games` (how original), is available [on GitHub](https://github.com/LorenDB/download-steam-games). Building it is a simple matter of running `dub build`. After that, you need to run `dub run -- --add-game` to add a game to the download list. After you've added some games, you can use `dub run` to run the application in download mode. It will download all your games for every platform you've specified, `.tar.gz` them, and put them in the output folder of your choosing.

## Limitations

The app currently is somewhat limited: there's no functionality to reconfigure the settings, you can't specify beta versions with `--add-game`, and there's no progress reporting during download. However, I think it's robust enough to actually use in production. I hope to address all of these issues soon, and while I'm at it, I'd like to add a feature to add a timestamp to the archive name so you can download many versions of the same game over time.

## Conclusion

This is a fun project that is helping get me hooked on data hoarding. If you start using my app to help hoard your own games, please leave a comment below! I'd love seeing how many gigabytes (or terabytes) of games you're hoarding.

## Updates

Shortly after writing this post, I've added some logging to the app. It ain't pefect, but it ain't terrible either.

{{< chat archiving-steam-games-for-fun-and-profit >}}
