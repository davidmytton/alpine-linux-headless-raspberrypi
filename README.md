# Howto: Deploy Alpine Linux on a headless Raspberry Pi

The [Alpine Linux
documentation](https://docs.alpinelinux.org/user-handbook/0.1a/Installing/setup_alpine.html)
assumes you have a way to access your system directly, such as via a display
and keyboard. However, there are many cases where you might want to deploy a
headless box, such as on a Raspberry Pi connected via ethernet (or wifi).

There are instructions out there for how to install [Alpine Linux on a
Raspberry Pi](https://wiki.alpinelinux.org/wiki/Raspberry_Pi), including how to
deploy on a [headless Raspberry
Pi](https://wiki.alpinelinux.org/wiki/Raspberry_Pi_-_Headless_Installation),
but I found them to be somewhat out of date and inconsistent. 

This repo packages the overaly file in a repo for version control. The full instructions
are in the [Alpine Wiki
instructions](https://wiki.alpinelinux.org/wiki/Raspberry_Pi_-_Headless_Installation).

## Credits

Thanks for the original instructions from the [Alpine Wiki user
sodface](https://wiki.alpinelinux.org/w/index.php?title=Raspberry_Pi_-_Headless_Installation&action=history),
who also hosted the overlay files on their website
([download](http://www.sodface.com/repo/headless.apkovl.tar.gz)). I expanded
and updated these instructions and then added them to GitHub so they can be
easily accessed.
