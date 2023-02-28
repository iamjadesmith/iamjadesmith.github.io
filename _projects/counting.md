---
title: "Counting"
permalink: /projects/counting/
toc: true
---

In my spare time, I have enjoyed programming various projects and “Counting” is a fun example of this.

---

# Background

My friends and I communicate through a Discord group chat that includes a variety of channels and topics. “Counting” was developed as a simple channel we could all enjoy to pass the time. Below is an image that demonstrates the basic concept of the channel. 

![Counting Image](\assets\images\counting\first.png)

This image shows how high the original “Counting” channel has gone in number. 

![Counting Image](\assets\images\counting\last.png)

It came up in conversation with my friends that there was a high probability of mistakes being made. Since it would have been too tedious to visually sort through all the numbers and count up the mistakes for each person, I wanted to develop a program to do this. 

## The Idea

With my analytical and problem-solving skills, I have been able to use R and Bash to build a program that sorts through the data and presents statistics for each person who was involved that month. These statistics include how many numbers each person has submitted to the channel and the number of mistakes someone makes each month. The program will then copy text to the clipboard for sending a message about who the winner of the month was and presents the evidence in the form of two bar charts; one bar chart for the month and another bar chart for the year to date. 

---

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

To fix these problems, I just used these lines of code:

```r
numbers <- as.numeric(gsub(".*?([0-9]+).*", "\\1", counting$Content))
counting$people <- gsub("*[#][0-9]*", "", counting$Author)
```

> I made numbers its own list rather than adding it as another column to the data to make it easier for the for loop I integrate later to check for mistakes.

## Checking for Mistakes

First, I made a mistake counter and a list that would contain the people that made mistakes. I also made a variable called between, that would be a list containing between the numbers the mistake is made between.

```r
mistake_counter <- 0
mistake_person <- c()
between <- c()
```

When checking for mistakes in the counting, I first thought of making a while loop that starts at 1 and increments by 1 and checks if each number in `numbers` is equal to that number in the while loop. It would look like this:

```r
i <- 0
while (i < length(numbers)) {
    if (numbers[i] != i) {
        mistake_counter <- mistake_counter + 1
        mistake_person <- append(mistake_person, counting$people[i])
        between <- append(between, paste(numbers[i-1], numbers[i+1], sep = " and "))
    }
}
```

However, this initial method that I thought of to check for mistakes had a flaw in it. If a mistake was made, it would throw off the rest of the numbers also. For example:

| Numbers | `i` | Mistake |
|:-------:|:-:|:---:|
|    1    | 1 | No |
|    2    | 2 | No |
|    **4**    | **3** | **Yes** |
|    5    | 4 | **Yes** |
|    6    | 5 | **Yes** |

The mistake is made at 3, but it will continue to think it is a mistake after 3 because 5 does not equal 4 either and so on.

I could fix this with some if statements in the loop to increment i by an extra 1 or minus 1 depending on the mistake made, but that seems innefficient. I later came up with a better idea that was more efficient than my first idea.

```r
for (i in 1:length(numbers)) {
  if (numbers[i] != 1 && numbers[i] - numbers[i - 1] != 1) {
    mistake_counter <- mistake_counter + 1
    mistake_person <- append(mistake_person, counting$people[i])
    between <- append(between, paste(numbers[i-1], numbers[i+1], sep = " and "))
  }
}
```

With this code, it checks if the current number minus the number before it is exactly equal to 1. If that is not the case, a mistake is made. This makes it so the mistake is only made at the mistake, and not continously after that if the numbers after the mistake are correct.

It would make the table look like this:

| Numbers | `i` | Mistake | n[i] - n[i - 1]|
|:-------:|:-:|:---:|:---:|
|    1    | 1 | No | 0 |
|    2    | 2 | No | 1 |
|    **4**    | **3** | **Yes** | **2** |
|    5    | 4 | **No** | 1 |
|    6    | 5 | **No** | 1 |

> In my if statement in the code above, I made an exception if the number is 1 since it is the start.

## Plots

With a little help from ggplot, I was able to make some bar charts that show how many numbers each person counted during the month and also the running total to see how many numbers each person counted in total. Here is what they look like for April 2020:

![Month Count](\assets\images\counting\month_count.png)

![Running Total](\assets\images\counting\running_total.png)

> These were ran as of February 2023, so the Running Total will represent the total from the start until when I ran the program. I figured if I ran the code on the first of every month, I could have the running total run that way.

## Text Output to Chat

I also have the R program generate the text that I want to send to the Discord chat along with the plots that I send. I then have it copy that text to my clipboard on my computer with the package "clipr" which I can immediately paste into the Discord chat. For April 2020, this is what it would output:

> Welcome to the counting statistics for April 2020:
> There were 6 mistakes made this month. The mistakes were made by GoldenArcanoid, JoeJadJavaJim, yimmyyaaaaaas, Trevinator, Stella and, Clare K. Those mistakes were made between 584 and 586, 1068 and 1070, 3644 and 3646, 5482 and 5484, 7387 and 7389 and, 11009 and 11011. The counter of the month for this month is Trevinator with 2168 numbers counted. Here is a graph showing the number of numbers counted by everyone this month as well as the current running total.

With the R code written to do everything I want, I can move on to automating everything to happen with bash.

# Bash

