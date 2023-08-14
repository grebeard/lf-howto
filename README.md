# How to move from ranger to lf

tldr;
Moving from ranger to the more modern terminal file manager lf requires some configuration steps if you wish for similar behavior. This page explains how this can be achived and provides example configuration files that implement a more ranger like behavior in lf. 


## Why? ##

For those who have come to love terminal based file managers, ranger is likely one of the coolest open source tools out there. And while it has revolutionized my workflows when working with files on Linux, there were still some issues. Moving large files or navigating through directories with many files, could lead to performance issues that could not be easily fixed, eitehr because of the core architecture of rangers code or because it was written in python with intended legacy support. Fortunately, some time ago someone made lf (list files), a new terminal based file manager which was inspired by ranger but written in go. Unlike ranger, lf is build around a core feature set that focuses on file mangement, while also allowing extentive customization through its configuration. Consequently, it is both a minimal, clean file manager and at the same time it can be extended with any feature someone might wish for.

When I found lf a few weeks ago, one of the most significant hurdles for me was to configure lf with the same core features I was used from ranger. While the core keymappings of lf are inspired by vim, just like ranger, things like file openers and file preview did not work right away.

To make this transition easier and allow others to better test out lf as a replacement for ranger, I wrote down some of the most significant issues during my transition and how to fix them.


Note that lf is configured through the configuration file in ~/.config/lf/lfrc

### File opener ###

While ranger uses its own file opener called rifle, lf uses mimeopen by default to open files with the related programs. For me that meant image files would start my browser, because that was the default behavior of mimeopen. There are quite a few options for file openers and lf makes it really easy to switch between them. But since I already configured rifle to open files how I wanted to, the easiest way open files with lf is to simply configure rifle as a file opener.

This could be done either by changing the lfrc configuration file, or by simply setting the environment variable OPENER, which will also be respected by other applications.

	export OPENER='rifle'

Add this export to your shell configuration files (e.g. bashrc or zshrc) to take effect.

### File Previews ###

By default, lf will only preview text files. To enable previews for other file types, we can use the same script that is used by ranger. Since the argument number of ranger is different, we use a simple wrapper script:

Content of wrapper script named "scope-lf-wrapper.sh" 

    #!/bin/sh
    "$HOME/.config/ranger/scope.sh" "${1}" "${2}" "${3}" "" "" || true


Add to your ~/.config/lf/lfrc:
 
	set previewer ~/.config/ranger/scope-lf-wrapper.sh
	

While looking for a simple solution to enable previews in lf, I also stubled upon this additional script that adds sandboxing to the preview by using bubblewrap:

https://github.com/gokcehan/lf/wiki/Previews#sandboxing-preview-operations

Simply change the previewer to this instead if you want to add a bit of security:

	set previewer ~/.config/lf/previewSandbox.sh
	

### Deleting files / Trash ###

By default, the delete command is not enabled in lf, so we need to add it if we want to remove files or move it to the trash. One of the cool projects I found while experimenting with lf was trash-cli and how easy it integrates into lf:


	cmd trash %trash-put $fx
	map D trash


### Automatic refresh ###

To enable the same automatic file updates that we are used from ranger, lf offers the period option to poll the directory status every x seconds:


	set period 1

### File and directory size

Enable file and directory information like rangers defaults:

	set info size
	set dircounts
	

### Add directory with f7 ###

	map <f-7> push :mkdir<space>


### leave some space at the top and the bottom of the screen ###
	set scrolloff 10


### clear mark from files after pasting ###

In lf, files that have been copied are marked and stay marked after being pasted/moved. This changed the behavior back to rangers default:

	map p : paste; clear
	


### colors ###

Lf uses GNU dircolors defaults to display different file types. This can be changed by adapting the colors file in the lf configuration directory.
Add [this](https://github.com/grebeard/lf-howto/blob/main/lfrc/colors) colors files to your lf config directory (~/.config/lf/colors) to change the colors to rangers defaults.

#### The configuration in this repo has been created and tested with lf version 30


### Official documentation ###

Some useful hints for lf configurations can be found in the community wiki: https://github.com/gokcehan/lf/wiki

The official documentation can be found here: https://pkg.go.dev/github.com/gokcehan/lf
