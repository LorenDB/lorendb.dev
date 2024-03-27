+++
title = 'Introducing the Matrix Codebrowser'
description = "If you've ever wondered what makes your Matrix client tick, you're in luck!"
date = 2024-03-27T18:54:14-04:00
tags = ["matrix", "code", "selfhosting"]
images = []
+++

Today marks the public introduction of the [Matrix Codebrowser](https://matrix.codebrowser.lorendb.dev). It's an instance of [codebrowser](https://github.com/KDAB/codebrowser) that is hosting the sources for [mtxclient](https://github.com/Nheko-Reborn/mtxclient), [libQuotient](https://github.com/quotient-im/libQuotient), and [olm](https://gitlab.matrix.org/matrix-org/olm) (with more projects to come in the future).

## Testimonials

> But it's almost surreal to see my own code in a page that I usually see when I need to understand Qt's inner workings.
>
> - [kitsune](https://matrix.to/#/@kitsune:matrix.org), [March 27, 2024](https://matrix.to/#/%23thisweekinmatrix%3Amatrix.org/%24yzI2tGmJWaS4Cxp_IPDx4XUup2Uo-qBWVx2W5LRl9s0?via=matrix.org&via=envs.net&via=element.io)

## What is it good for?

If you've never used a codebrowser instance before, you probably are wondering "why would you make such a big deal about a public source mirror?" Well, codebrowser doesn't just mirror a repository to a web interface. It also uses compilation information to detect and display all the symbols in a source file. For example, try mousing over some symbols in the [QScopedValueRollback<> source](https://codebrowser.dev/qt6/qtbase/src/corelib/tools/qscopedvaluerollback.h.html) from the [official codebrowser instance](https://codebrowser.dev).

This ability to easily navigate the content of a library's codebase is the raison d'être of codebrowser. I occasionally have a need to see exactly what a certain Qt function is doing under the hood; while I could grep through the GitHub mirror, it's far faster to look up the function in codebrowser, and I can also quickly dive into function calls from there.

## More to come

The current public version of the code only supports C++ codebases; however, the official instance has seen massive work by [KDAB](https://kdab.com) to add support for Rust and Dart code. Once KDAB's work is made public, I plan to add more repositories like [matrix-rust-sdk](https://github.com/matrix-org/matrix-rust-sdk) and [vodozemac](https://github.com/matrix-org/vodozemac).

You can also submit requests! I'm currently mainly focused on Matrix-related libraries, since that is likely the most useful code I can support, but if you want to see your favorite client or server added, I'll be happy to add it (as long as it's within reason). Just leave a comment below or come say hi [on Matrix](https://matrix.to/#/%23matrix-codebrowser%3Anheko.im?via=nheko.im).

{{< chat introducing-matrix-codebrowser >}}
