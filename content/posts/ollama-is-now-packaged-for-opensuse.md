+++
title = "Ollama is now packaged for openSUSE"
date = 2024-02-27T20:20:00-05:00
tags = ["opensource", "opensuse", "packaging", "ai"]
images = ["images/ollama/ollama-website-install-script.png", "images/ollama/one-does-not-simply-upload-to-opensuse-factory.png"]
description = "A journey into packaging RPMs with the Open Build Service, and how I actually survived it."
+++

> TL;DR: I've packaged [Ollama](https://ollama.com) for openSUSE! As of February 27, 2024, it's available for Tumbleweed (Leap will hopefully pick it up at some point). Just run `sudo zypper ref && sudo zypper in ollama` to get it! Please note that it will not use your GPU for now; if you need GPU support, keep installing Ollama from https://ollama.com instead. Now for the main post...

As somebody who has been a local LLM enthusiast ever since the advent of the Llama models [\[citation needed\]](https://xkcd.com/285/), I have been using [Ollama](https://ollama.com) for some time now. However, Ollama has one small problem:

{{< figure
    src="https://lorendb.dev/images/ollama/ollama-website-install-script.png"
    alt="Oh noes, it's an install script!"
    title="Oh noes, it's an install script!"
>}}

That's right, it's 2024 and we still haven't learned that [Flatpaks](https://www.flatpak.org) and [AppImages](https://appimage.org) are the best way to distribute a Linux application that will work on any distro (yes, I am part of the [Snap](https://www.reddit.com/r/linux/comments/j3ajnf/whats_wrong_with_snaps_why_so_many_people_hate_it/) [hater](https://www.reddit.com/r/linux/comments/wlg2g7/why_do_linux_users_tend_to_hate_snaps/) [community](https://itsfoss.community/t/avoiding-snaps-in-ubuntu-22-04-and-derivatives/9363)). Instead, the Ollama authors have created a [script](https://github.com/ollama/ollama/blob/main/scripts/install.sh) that will download Ollama, create a systemd service, and even install CUDA drivers if necessary.

Now, to be fair, Flatpaks and AppImages aren't intended to be used for systemd services. Also, while Ollama's website doesn't inform you of the security risks of running a random script from the internet, it at least gives you a link to the script source, so you can read over it and make sure it's not going to do anything nefarious.

> By the way, in case you don't know, you can validate any install script even if the source isn't linked. Instead of directly piping the script into `sh`, instead pipe it into a temporary file. You can then read that file to make sure it looks safe before actually running the script.

The obvious best practice for distributing Linux software, however, is to provide system packages. Some projects (like [Docker](https://docs.docker.com/desktop/install/linux-install/#supported-platforms)) provide their package repositories; however, in most cases, software should be distributed through the main repository. Since the Ollama devs are not doing this themselves (which is completely understandable; most developers don't maintain their own packages), I decided to take it upon myself to refresh my knowledge of RPM and the [Open Build Service](https://build.opensuse.org) and package Ollama for openSUSE myself.

## It can't be that hard, right?

I've packaged a few things for openSUSE before, but it's been a little while, so I decided to start from scratch. I cloned my home repository to my system and created a new package:

``` bash
$ osc checkout home:LorenDB
$ cd 'home:LorenDB'
$ osc mkpac ollama
```

This just creates a blank folder. You'd think it would at least create a .spec file (or "specfile"), but no, you have to do the whole thing yourself. So I used this [RPM packaging tutorial](https://docs.fedoraproject.org/en-US/package-maintainers/Packaging_Tutorial_GNU_Hello/) from Fedora (which openSUSE links to from their wiki) and copied the sample specfile over to my local repository, adapting the project name, license, and a few other things as needed. I also created a [_service file](https://openbuildservice.org/help/manuals/obs-user-guide/cha.obs.source_service.html) to automatically fetch the Ollama source from GitHub. Then I ran `osc build` to build the package. It failed, which I was completely expecting. After all, Ollama is written in Go, so I needed to adapt the specfile to actually build Ollama.

> `osc` is the command-line tool for interacting with Open Build Service repositories and packages. It's really powerful; for example, when I call `osc build` as mentioned above, it will spin up a VM just like the VMs used on the OBS runners to build packages.

## Unexpected difficulties

Given that I am unfamiliar with the Go workflow, I copied a bunch of stuff from the [Hugo specfile](https://build.opensuse.org/package/view_file/openSUSE:Factory/hugo/hugo.spec?expand=1) and rebuilt the package.

It didn't build.

The build output told me that Go was unable to access `https://proxy.golang.org`, which is used for the Go package registry. This happens because Go wants to download all of Ollama's dependencies, but OBS's runners aren't internet-connected. After talking with the extremely helpful folks in [#packaging:opensuse.org](https://matrix.to/#/#packaging:opensuse.org), I learned that OBS actually has a mechanism in place to handle Go packages. With the power of _service files, you can build an archive of Go packages that will be passed into the runner. This way, openSUSE doesn't have to package every Go package in existence just to satisfy arbitrary Go-based packages. All you have to do to enable this is add the following service to your _service file:

``` xml
<service name="go_modules" mode="manual">
    <param name="compression">xz</param>
</service>
```

I found that a few more tweaks were required to make this work; I had to add the resulting `vendor.tar.xz` to my specfile as a source and untar it during setup. I also had to add `-mod=vendor` to the `GOFLAGS` environment variable so Go would know to use my bundled modules.

> A quick acknowledgement here: I referenced the [Arch Linux Ollama PKGBUILD](https://gitlab.archlinux.org/archlinux/packaging/packages/ollama/-/blob/main/PKGBUILD?ref_type=heads) for some stuff related to the Go build process. Thanks, Arch folks!

Having done all this, I rebuilt the package.

It didn't build.

## Git shenanigans

Ollama does not provide LLM execution capability by itself. Rather, it acts as a powerful API wrapper for the wonderful [llama.cpp](https://github.com/ggerganov/llama.cpp). This means that building Ollama needs to include a build of llama.cpp. Ollama achieves this by packaging llama.cpp as a git submodule. Unfortunately, the build script attempts to fetch the llama.cpp submodule from GitHub on every build. I was able to disable this by adding `export OLLAMA_SKIP_PATCHING=1` to the build process. However, the build still failed, as the `tar_scm` service that I used to download the source does not include the submodule in the source tarball by default.

...or so I thought when Ollama failed to build llama.cpp, complaining that `llm/llama.cpp` was not a git repository. I added the following to my `tar_scm` service:

``` xml
<param name="submodules">enable</param>
```

but llama.cpp still wouldn't build! After some sleuthing, I realized that the problem was a little bit more insiduous than I had thought. While `tar_scm` had been downloading the llama.cpp submodule all along, it was also silently discarding the `.git` directory from both the top-level Ollama repo and the llama.cpp submodule. This meant that when Ollama tried to run some git commands on llama.cpp to get the code in the proper state, git would fail, thereby crashing the build. To fix this, I had to add this parameter to `tar_scm`:

``` xml
<param name="package-meta">yes</param>
```

With all this done, I rebuilt the package... and it successfully built!

## systemd and user accounts

However, I wasn't out of the woods yet. I still had to properly package the systemd service so users don't have to open a new terminal and run `ollama serve` every time they want to talk to an AI. Bundling the service was pretty easy; I extracted the contents of the service file from the Ollama script, put them in a dedicated `ollama.service` file, added that file to the specfile, and then added the following instructions to my specfile:

``` rpm
%pre -f %{name}.pre
%service_add_pre %{name}.service

%post
%service_add_post %{name}.service

%preun
%service_del_preun %{name}.service

%postun
%service_del_postun %{name}.service
```

This installs and uninstalls the Ollama service when you install and uninstall the package. Unfortunately, it doesn't autostart the service, but that's a feature for another day.

I also had to create the `ollama` user account, since Ollama wants to run the server as a dedicated user. I achieved this by adding a file called `ollama-user.conf`:

```
#Type Name   ID GECOS      Home directory Shell
u     ollama -  "Ollama" /usr/share/ollama    -
```

and adding that file to my specfile:

``` rpm
%{sysusers_requires}

# ...

%build
%sysusers_generate_pre %{SOURCE3} %{name} %{name}-user.conf
```

With this done, I pushed the package to OBS (I had been working on the package locally until now) and spun up a fresh openSUSE Tumbleweed VM  to test the package on a clean system. Unfortunately, I had left an error in `ollama-user.conf`, so installation failed. A quick fix later, I successfully installed Ollama from an RPM package for the first time!

My joy was short-lived, however, as I quickly realized that the systemd service was failing.

{{< figure
    src="https://lorendb.dev/images/ollama/failing-systemd-service.png"
    alt="Repeating log entries showing that the Ollama service is failing to start"
>}}

Some investigation revealed that while I'd successfully specified that the `ollama` user's home folder was `/usr/share/ollama`, that folder is not automatically created. The fix ended up being as simple as registering `/usr/share/ollama` in the specfile:

``` rpm
%files
# %{_datadir} expands to "/usr/share" and %{name} expands to "ollama"
%attr(-, ollama, ollama) %{_datadir}/%{name}
```

Note the `%attr(-, ollama, ollama)`; I initially failed to set `ollama` as the owner of `/usr/share/ollama`, so Ollama was unable to access its own home folder! Also of note is that after receiving feedback on my package, I changed the home folder to `/var/lib/ollama`.

With this fix in place, I again installed the package on my test VM... and after running `sudo systemctl start ollama`, I was able to run the Ollama systemd service!

## Getting it into the openSUSE repositories

Now I just needed to upstream the package to the [openSUSE:Factory](https://build.opensuse.org/project/show/openSUSE:Factory) repository, which is the default repository for all packages that ship with openSUSE Tumbleweed. However, you can't directly submit a package to Factory.

{{< figure
    src="https://lorendb.dev/images/ollama/one-does-not-simply-upload-to-opensuse-factory.png"
    alt="One does not simply upload to openSUSE:Factory"
>}}

Instead, openSUSE packaging uses development repositories. These are repositories that are officially run by openSUSE but not directly packaged in openSUSE. Instead, they serve as a staging area to verify that packages build and look sane. Once a package passes in the devel repository, it can be submitted to Factory. In my case, Ollama fit best in the [science:machinelearning](https://build.opensuse.org/project/show/science:machinelearning) project, so I submitted my Ollama package there. Around 12 hours later, my request was accepted!

The final step to adding a package to Factory is to write to the [openSUSE Factory mailing list](https://lists.opensuse.org/archives/list/factory@lists.opensuse.org/) to announce your new package, and then submit a request with a link back to that email. Unfortunately, my first two attempts at submitting my package were rejected due to various minor problems in the specfile. However, after submitting my third request, I recieved this exciting email:

{{< figure
    src="https://lorendb.dev/images/ollama/package-finally-accepted-email.png"
    alt="An email stating that Ollama was accepted into openSUSE:Factory"
    title="This was the most exciting email I've recieved in a little while."
>}}

That's right, Ollama was finally accepted into Factory! My packaging saga was finally over!

...at least, it's over for now. Ollama releases happen more or less weekly, so I'll probably have to spend a few minutes every weekend pulling down the latest sources and sending them off to Factory, but the hard work is done.

## Some limitations of the package

If every cloud has a silver lining, then every silver lining must have a cloud... right? At any rate, this package is not a perfect solution for everyone. If you wish to use CUDA or ROCm to accellerate your LLMs by running them on the GPU instead of the CPU, you will need to install Ollama from source. openSUSE can't package a CUDA or ROCm version of Ollama, since neither of those frameworks are available in the main openSUSE repositories. ROCm is open source, so it could eventually land in the main repository, but CUDA is nonfree, so it's out of the question for now. I'm not ruling out the possibility of eventually releasing an RPM that links against CUDA or ROCm, but something will have to change in order for that to happen.

However, if you're just looking for a quick way to run an LLM on your machine and don't need extremely fast results, then enjoy running Ollama from the openSUSE repositories!

## Some sort of a conclusion

Despite the various frustrations of building the Ollama RPM, I really enjoyed this little adventure into packaging and plan to keep getting involved. I'll continue to maintain the Ollama package, and I'm also planning to take maintenance of a few other packages. Furthermore, I've created a few more packages! Specifically, I've packaged [Supernote's SN Pro font](https://supernotes.app/open-source/sn-pro/) ([package](https://build.opensuse.org/package/show/M17N:fonts/supernotes-snpro-fonts)), and I'm working on submitting a package for [dness](https://github.com/nickbabcock/dness).

At this point, I also want to thank [Richard Rahl](https://matrix.to/#/%40rrahl0%3Aopensuse.org) and [smolsheep](https://matrix.to/#/%40smolsheep%3Aopensuse.org) for their help. Whenever I asked questions in [#packaging:opensuse.org](https://matrix.to/#/#packaging%3Aopensuse.org), they were always quick to patiently answer my questions and point out problems with my package. People like them are what makes contributing to open source projects awesome. Thanks, y'all!

{{< chat ollama-is-now-packaged-for-opensuse >}}
