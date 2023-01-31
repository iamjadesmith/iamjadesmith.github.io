---
title: "Counting"
permalink: /counting/
toc: true
---

This was one project that I chose to do on my spare time. I often like to do various programming things in my spare time, and I think this is a fun example of this.

# Background

My friends and I have a group chat in Discord to communicate. In that Discord, we have various channels that we use to communicate different things about different topics. One of my friends thought of the idea for something that we could all do when we are bored. This channel is called counting, and here is an image to demonstrate what happens in this channel.

![Counting Image](\assets\images\counting\first.png)

As you can see from the image, one person says a number, then the next person says the number that is supposed to be after that and so on. The only rule is that you cannot go twice in a row.

Here is another image that shows how long this channel has gone:

![Counting Image](\assets\images\counting\last.png)

After a long time of counting, we have made it to very large numbers. With numbers getting this large, it came up in conversations with my friends that what if someone has made a mistake in this counting channel. This got me thinking about possible ways of checking for mistakes. Obviously going through each number one by one would be painful to look at, and I could even miss a mistake trying to do that myself.

## The Idea

This is where the problem solving and analytical side came out of me. I thought of the idea to make a program check if there are any mistakes in the Discord channel. I also thought of the idea to make statistics as well to see how many numbers each person counts during the current month as well as a running total of the numbers counted by each person throughout the entirety of the channel. This is how the project came to be.

# Research

I had a general idea about how to make a program that could tell if there were no mistakes in the channel. However, there was one thing that I needed to research. That thing that I needed to research was how to get the channel exported in a way that would allow me to run a program on it.

I found this [Discord Chat Exporter](https://github.com/Tyrrrz/DiscordChatExporter) from Tyrrrz that was perfect for the job. It allows me to export the channel nicely into a csv, txt, or other formats which is awesome. It even has a CLI which will be perfect for automating it.