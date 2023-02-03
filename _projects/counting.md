---
title: "Counting"
permalink: /projects/counting/
toc: true
---

In my spare time, I have enjoyed programming various projects and “Counting” is a fun example of this.

# Background

My friends and I communicate through a Discord group chat that includes a variety of channels and topics. “Counting” was developed as a simple channel we could all enjoy to pass the time. Below is an image that demonstrates the basic concept of the channel. 

![Counting Image](\assets\images\counting\first.png)

This image shows how high the original “Counting” channel has gone in number. 

![Counting Image](\assets\images\counting\last.png)

It came up in conversation with my friends that there was a high probability of mistakes being made. Since it would have been too tedious to visually sort through all the numbers and count up the mistakes for each person, I wanted to develop a program to do this. 

## The Idea

With my analytical and problem-solving skills, I have been able to use R and Bash to build a program that sorts through the data and presents statistics for each person who was involved that month. These statistics include how many numbers each person has submitted to the channel and the number of mistakes someone makes each month. The program will then copy text to the clipboard for sending a message about who the winner of the month was and presents the evidence in the form of two bar charts; one bar chart for the month and another bar chart for the year to date. 

# Research

I had a general idea about how to make a program that could tell if there were no mistakes in the channel. However, there was one thing that I needed to research. That thing that I needed to research was how to get the channel exported in a way that would allow me to run a program on it.

I found this [Discord Chat Exporter](https://github.com/Tyrrrz/DiscordChatExporter) from Tyrrrz that was perfect for the job. It allows me to export the channel nicely into a csv, txt, or other formats which is awesome. It even has a CLI which will be perfect for automating everything with a bash script.

I used this line of code to export the channel into a csv into the directory that I want (token and channelid are inserted with my Discord credentials):

```bash
dotnet DiscordChatExporter.Cli.dll export -t "token" -c channelid -f Csv -o "/Users/jade/Documents/Counting/DiscordChatExporter/Exports/counting.csv" --dateformat "MMMM-yyyy"
```


This is the format of the data exported:

![Data](\assets\images\counting\data.png)

---

# R Code

## Cleaning

There were two apparent things that stood out to me when I looked at the data:

1. Text with the number in the Content column making the content column read as character instead of numeric
2. The #nnnn as the last 5 characters of the Author column

### Making Content Numeric

