---
layout: post
title: Create Your Own Podcast with Github Pages and AWS-S3
toc: true
subtitle: Create Your Own Podcast with Github Pages and AWS-S3
tags: [linux,tutorial,install,podcast,AWS,S3,Github,pages,free]
comments: true
---

## Create Your Own Podcast with Github Pages and AWS-S3

So you want to create your own podcast but you dont want to use a third party hosting service? This tutorial is just what you need!

## Table of Contents
{: .no_toc}
* TOC
{:toc}

## Step 1: Setup Github pages

If you haven't done so already setup a site using Github pages as its free and it is what we will be using to host our static site!
I use [beautiful jekyll](https://github.com/daattali/beautiful-jekyll) as my theme but you can choose to use whatever you want that is compatible with github pages.

## Step 2: Setup RSS feed

Podcasts work by feeding from an RSS feed which is an xml document that states certain values that podcast sites like Apple and Spotify recognize as podcast feeds. Depending on who you where you want your podcast to be found you need to follow their xml specifications you can find that info on their sites. For example https://podcasters.apple.com/ has specifications to add to your feed.rss and so does Spotify at https://podcasters.spotify.com. Once you have that setup you simply add your feed to these websites to add to Apple and Spotify respectively. 

## Step 3: How to Record

I personally record via Discord and a handy bot called Craig. More information on that bot can be found here https://craig.chat/. You can invite this bot to your server and have it join your voice channels where it will begin recording. It can even record in seperate audio streams per user if you want to slice and dice audio. Once you are done recording Craig will send you DM where you can find the audio files for your latest recording.

## Step 4: Processing after Recording

Depending on what type of files you need and what the hosting platform you are using requires you will need to process the files Craig gives you into another format. I wont cover that here but some googling on converting this format to that format may help or use chatGPT https://openai.com/blog/chatgpt/ . Once you have those files processed we are onto the last 2 steps.

## Step 5: Upload to S3

I use AWS S3 as the hosting for my audio files. Im not going to cover how to create an AWS account here but once you have simply upload the files to AWS S3 and make sure to make the permissions public as the hosting provier i.e Apple or Spotify will need access to these endpoints to listen to the audio. DO NOT DO THIS FOR FILES YOU DO NOT WANT PUBLIC.
