--- author: Nathaniel categories: - Links - Programming - Tutorials
date: "2015-11-20T12:08:48Z" meta: \_edit\_last: "1" pe\_theme\_meta:
O:8:"stdClass":2:{s:7:"gallery";O:8:"stdClass":3:{s:2:"id";s:2:"89";s:5:"width";s:0:"";s:6:"height";s:0:"";}s:5:"video";O:8:"stdClass":1:{s:2:"id";s:3:"504";}}
published: true status: publish tags: - howtos - tutorials title: Quick
Notes - Coding Stata do-files with Sublime in Unix/Linux type: post ---

I am used to writing code in notepad programs, such as N++ and the
`fantastic Sublime Text 3`. Here's a quick note on connecting a powerful
coding notepad in Linux to Stata.

 

Sublime, like many of these programming-oriented editing notepads, have
massively powerful tools that crush Stata's default editor. Moreover,
since many people are simultaneously juggling Python, R, and Stata (and
more) scripts for a single project, the ability to work from one
programming-oriented environment is nice.

 

While it is straightforward to run Stata do-files from Sublime Text in
Mac OS and Windows, using packages like Sublime Stata Enhanced, it
wasn't obvious how to do so in Linux. The following is a little
integration guide, which is `indebted to this Github howto here`.

#### Sublime, Stata & Unix Walk Into a Bar:

 

First, from your terminal create symbolic links for `xStata` and `Stata`
commands. The gist of creating a link in the terminal is the following,

    ln -s [target-filename] [symbolic-filename]

    sudo ln -s /usr/local/stata14/xstata /usr/local/bin/xstata && sudo ln -s /usr/local/stata14/stata /usr/local/bin/stata
    #[sudo will prompt you for your password]

Of course you can edit this to match the version of Stata (and flavor)
you are using.\
 \
The following Stata package definitely works in Linux, so we'll use it!
Download it from `https://github.com/rpowers/sublime_stata .`\
 \
Within the ZIP file from is a /Stata directory--find it and place it in
the Sublime /Packages directory on your Linux system. If you're new to
Linux, this file is likely in the folder
`/[your user name]/.config/sublime-text-3/Packages`. Notice, sometimes
these files are hidden from the user in the terminal so they may be hard
to find. Confirm that the files appear with, typing `ls -ld .?*` in the
command line.\
 \
Last, open the Stata.sublime-build file located in
`/.config/sublime-text-3/Packages/Stata/` directory. Replace all the
text with the following,

     { "cmd": ["xstata do $file"], "file_regex": "^(...?):([0-9]):?([0-9]*)", "selector": "source.stata", "shell": true, } 

Seriously--just copy and paste over the stuff in the original text file.
Save, restart Sublime Text for safe keeping, and you're good to go.\
 \
Now when you use Sublime Text, , simply typing `ctrl+b ` executes Stata
externally and runs the do-file you're currently editeing.\
 \
*Note: for some reason I have run across some issues running do files in
batch mode from the Unix terminal and such. I found adding an extra
space at the end of my code, or a superfluous `log close` does the
trick.*\
 \
 

#### References:

Sublime Text 3: `http://www.sublimetext.com/3`.

Rhocon's github article for a similar approach:
`https://github.com/rhoconlinux/Stata-12-in-Sublime-3-under-Ubuntu`.

State Enhanced for Sublime from rpowers (used on Linux systems):
`https://github.com/rpowers/sublime_stata`

Symbolic links in Unix:
`http://faculty.salina.k-state.edu/tim/unix_sg/advanced/links.html`

Sublime+Stata usage in Window and OSX:
`https://github.com/andrewheiss/SublimeStataEnhanced`
