+++
title = 'Making Godot More Secure'
date = 2025-06-18T14:14:54+10:00
draft = false
+++

## Godot has a small problem
In the world of Godot there is a lot of good to be found. It feels like making games has never been easier to get into. However there is a big problem with this game engine.

## How do exports actually work?
Godot is smart when it comes to exporting games. It uses a template system that uses a compiled version of the engine and attaches your project to it in the form of a `.pck` file. So someone launching your game is similar to you opening your game with the Godot Editor. This has the huge benefit of letting developers build for any platform. (even MacOS!!!) The reason this works is because all of the low level compiling is being done by godot elsewhere. We just download the templates and we can export away.

## One small caveat...
Since the Godot project itself isn't compiled and is just stored in a `.pck` file it becomes braindead simple to steal someone else's work. All you need is a tool like [gdsdecomp](https://github.com/GDRETools/gdsdecomp) and the `.pck` file. This lead to a case where an indie dev team, VoltekPlay had their [game stolen](https://youtu.be/3bCJ7BieUss) and reuploaded to the AppStore. The thief made over $60,000 in gross revenue according to [VoltekPlay's](https://www.reddit.com/r/godot/comments/1je90av/how_to_protect_your_godot_game_from_being_stolen/) reddit post. While large studios would not have a massive problem with this, small indies can get exploited if they don't protect their games.

## How do we do that?
While there is no way to 100% guaruntee your game is secure we don't actually care too much. Like how a lock can be picked, encryption can be cracked and people who are determined will find a way. We simply want to make it harder and take too much time to bother. Right now someone could probably scrape [itch.io](https://itch.io/) and automate the stealing process. This is where **PCK Encryption** comes in. Godot has a built in way to encrypt the contents of your project. You just need an AES-256 bit encryption key and just tick the box in the export settings right? Well. . .

## The Decent into Hell
In theory that is right! The PCK is encrypted! Woo!! It just won't open. . . wait a second!

This is because the Godot Engine doesn't have access to the encryption key. We have locked the door to our project but forgot to give the key to Godot to reopen it on launch. This is where things get complicated. To give Godot the key we need to compile it directly into it's source code. Which means we need to build our own Export Templates. Now this has significant drawbacks. (like no MacOS or IOS build unless you have a Mac to build from) It also takes a lot of tools and environment setup to get working, especially if you are building for more than one platform. Looking at this nightmare and bundle of tools I was going to have to install into my, already struggling, PC. I decided there must be a better way. So I made a better way. (I'm also way too stubborn to fork out the $6 to [godotbuilder](https://www.godotbuilder.com/))

## Introducing the [Godot Export Templater](https://github.com/Precipire/Godot-Export-Templater)
This is a [tool](https://github.com/Precipire/Godot-Export-Templater) I have thrown together with about as much glue and tape I could find. It's not ready for general use but it is getting there. I created a Docker container and installed all of the tools inside it instead. This way I don't have to stuff my system full of build tools. I then combined this with a shell script that can build out the scons command from some user input variables. The cherry on top is the Godot Plugin I made that interfaces with the whole thing and makes it run. Now you only need docker installed and you can make encrypted templates for most platforms. 

> Almost all Platforms...
>
> Currently the tool is only verified to support Windows and Web (but I am working on getting as many working as I can)

## Making Godot Secure shouldn't be this Hard
A lot of people I know got started in programming by making games. Some never went beyond this and I think that should be ok. As an indie dev you tend to wear a lot of different hats. (art, music, programming, etc) Your mental energy is already stretched across so many disciplines. Adding security to the pile and diving into low level C compiling is not something most indie devs want to deal with. We want to make games, not learn how to set up build tools or use scons. Which is why a design goal with my tool has been to make it as hands off as possible. For myself and others. Check it out [here](https://github.com/Precipire/Godot-Export-Templater)