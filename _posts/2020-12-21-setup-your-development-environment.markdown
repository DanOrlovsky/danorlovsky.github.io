---
layout: post
title:  "Setting Up Your Dev Environment"
date:   2020-12-21 10:45:47 -0400
categories: Development
---
Here is a list of tools you're likely going to want to have if you are interested in coding.

## Text Editor

### Software

Visual studio code is an incredibly powerful, lightweight, and easy to use text editor.

Follow the [link to install](https://code.visualstudio.com/) VS Code.  Most default options should suffice, but _Make sure you
allow it to add Visual Studio Code shortcuts to the Context Menus.  This is super helpful._

Follow the installation, and open it up if it doesn't automatically fire up.  The first thing you may want to do is pin it to 
your task-bar.  I'm sure most os's have this capability, but at least on Windows 10, right-click the icon and select "Pin to taskbar"

![Pin To Taskbar](/assets/images/PinToTaskbar.png){:class="img-responsive"}

### Extensions

While a very powerful editor directly out of the box, you will also likely rely heavily on extensions.  The extension explorer can be found 
on the left-side navigation pane (or shortcut keys: ctrl-shift-x).

![Extension Explorer](/assets/images/ExtensionExplorer.png){:class="img-responsive}

#### Use an Extension

For a quick example of a useful extension, go to the explorer and search "Beautify" - and click "Install"

![Beautify Search](/assets/images/BeautifySearch.png){:class="img-responsive}

Now: 

1. Create a new file (ctrl-n)
2. At the bottom-right hand corner, click the label that says: "plain text"
3. In the search bar that opens up, search and select "html"
4. Copy and paste this code directly into the editor:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<header>
<h1>This won't be a webpage that anyone sees</h1>
</header>
<ol>
<li>Item 1</li>
<li>Item 2</li>
<li>Item 3</li>
<p>
And here is some text
</p>
<footer>
<h1>Copyright Yesterday</h1>
</footer>
</ol>
</body>
</html>
```
*Don't worry about what any of this is doing.  We just want to see the functionality of the Beautify extension
5. Open the Command Palette (ctrl-shift-p) this will open another search dialog
6. Search and Select 'Beautify'
7. Your code should have instantly formatted.

This is a super useful extension as it helps maintain readable code.  It's something you'll get better at as you continue 
to write code, but for now, it does help.  Especially if someone else needs to _read_ your code.  Also, don't be afraid to explore
extensions.  Finding and installing/selecting different themes is a fun and not at all dangerous way to familiarize yourself with extensions.

The command palette is also incredibly powerful and useful, but we're not going to dig too deep into that today.

One last thing to note: you _really_ want to get used to the idea of _opening folders_ in visual studio code.  This will make your projects much easier to
manage.  You can view all sorts of file in VS Code, so just bring your entire project in!  If you allowed VS Code to add shortcuts to the Context Menus (right-click),
you can simply right-click your project folder and select "Open With Code."

![Open With Code](/assets/images/OpenWithCode.png){:class="img-responsive"}

## CLI

A (C)ommand (L)ine (I)nterface is very important for all aspects of development.  A lot of tools are command-line driven, so it's important for you 
to not only have access to common commands, but you're also going to want to spend some time familiarizing yourself with one.

Some standard options are:

* Windows Command Prompt
* Powershell
* Git Bash 

Git Bash in my opinion, is a good CLI to start with as it's widely used, the commands (to me) seem to be the most common,
so it will give you the least amount of headaches if you're following some advice online.

Here is an in-depth step-by-step guide to [Installing Git Bash](https://www.stanleyulili.com/git/how-to-install-git-bash-on-windows/) on
Windows.  You should also go ahead and pin that to your taskbar as well.

## NodeJS

It will likely be a good idea to install Node Js and Npm.  Some of the most popular frameworks (especially front-end), and a lot of aspects of
web development will require you to have them installed.  Whatever the current LTS version will likely be fine.  **Make sure you select ADD TO PATH**. 
It should be selected by default.

[NodeJs](https://nodejs.org/en/)

Once you are finished, open a fresh Bash terminal (any time the PATH is updated, you need to restart the terminal), and try typing both:

```
node -v
``` 
and then
```
npm -v
```

Both commands should run successfully and should respond with the corresponding version numbers:

![Node and npm version numbers](/assets/images/NodeAndNpmVersions.png){:class="img-responsive"}