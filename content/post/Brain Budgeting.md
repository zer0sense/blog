---
title: "Brain Budgeting"
date: 2024-08-07T00:30:33Z
description: "Brain Budgeting"
tags: [Notes]
categories: [Article]
comments: false
draft: false
---

Being a life long learner is such a catchy tag line, but what does that entail. If you are in the world of technology, it means you have heard of more emerging technologies and tools that you can't possibly remember them all. If you are not interacting with these tools and technology on a very frequent basis it starts moving into the long term storage portion of your brain.

This requires a storage strategy for your own brain, at least it did for me. I remember the information overload when I was chasing the cyber security dream. The strategy is what I discovered to be something commonly known as building a second brain. The research and advice people give for building a second brain can be fantastic. Not every strategy works for everyone, but you do have options. This article is to share my strategy and how I build my second brain.

## Note Taking

In my honest opinion, taking notes was one thing that went through the most changes. When I was studying for my certifications I was taking notes with the good ol' pen and paper. This was very time consuming. 

I watched a video from [John Hammond](https://www.youtube.com/watch?v=MQGozZzHUwQ) about taking notes in markdown, and my second brain was born from this. I do almost everything in markdown, if I can. This blog is in markdown, my resume is in markdown, and my notes are started in markdown. This made formatting in one markup language and using them across multiple platforms and programs so easy.

This makes all my content i write available across multiple note taking applications and readers. This also makes backing up and moving the files incredible valuable. Personally, all my markdown files are backed up to a gitlab repository. Using gitlab makes it backed up,sync'd, and available from any device at any time. A side benefit is that gitlab and github support markdown natively, so all the formatting you do while note taking you can see within github and gitlab without any other software.

### Obsidian

{{<figure src="/images/Posts/017/obsidian_graph.jpg">}}

I tried a ton of note taking applications when I started my markdown note taking journey. I ended up on obsidian as my main note taking app. I tried joplin, simple notes, vscode, and others that I can't recall. 

Truth is I don't even use obsidian to it's true potential. The relational data is very interesting when I look into it. However, I have most of my notes broken down in folders that contain the notes for the topic. For instance when I was studying for CCNA I had a folder with different topics broken down as different files. When I started a new course I aso create a new folder for that main topic as well. 

The layout of obsidian and easy management of the application, along with the official and community plugin support it can be very very powerful. I did see features that I never thought of when I saw others post their building a second brain strategy. It is a great piece of software within a free tier that is just amazing. Switching between editing and preview mode to see your formatting is so simple and effortless. 

The one thing I did have to do when using obsidian is adding the ```.obsdian``` and ```.obsidian/*``` to my gitignore file. If not then it will tell you to commit new updates constantly. Every time you make a setting change it wants to commit. 

### Notion

The first time you open notion you might not understand it's potential. I know I didn't. I watched a __lot__ of productivity videos revolving around notion. I can honestly say I am probably using about 1% of the capabilities. 

I will have to say, even with that 1%, notion is __INCREDIBLY__ powerful. Do yourself a favor and find a free template to build off of. The biggest thing holding back notion is honestly your imagination.....and handwritten notes. 

In line databases are something I didn't know I needed until I saw they were implemented. Let me tell you, when you mention the word database around me I get a nervous tick in my neck from losing data in my homelab multiple times while learning. The way notion handles your data, however, requires the most of simple of technical knowledge. This is the biggest strength for me. It is so versatile and powerful that it could go from the calculator app to microsoft access in a blink of an eye. 

The drawbacks, yes, it has one huge drawback for me. It's paid software!! Now before you send an angry mob my way, hear me out. First, if you are a student you can get this software for free. Also, this software is very popular amongst productivity and project managers worldwide for a reason. The final point for this software is to backup your data somewhere that you own. There are markdown files and you can always export important data; just make sure you do in case you don't keep the paid version going you have access to your data.

## Archive

I already informed you that all my notes are already backed up to a [GitLab](https://gitlab.com) repo. This is so crucial to having my notes being available no matter device, but in the case that there is no longer a free tier of these platforms I have another overkill solution. 

This solution is hosting my own wiki with [mediawiki](https://kalifornia909.info/post/media-wiki-docker/). This is how I own my content, my notes, my second brain. Wiki makes things relatable, searchable, and easy to read. This could be the one and done solution for some, and thats great. Making a wiki didn't come to me until I was already heavily invested of doing everything in markdown. 

That brings me to the drawback of using a wiki in my workflow. It doesn't support markdown. Wiki language is it's own beast. The way I get around spending hours rewriting everything is called [pandoc]. This is a linux program that converts text. This will take my markdown files and covert them to wiki lang. The one thing that doesn't always go well is converting tables. There are [table generators](https://www.tablesgenerator.com/mediawiki_tables)to help where the conversion is not as reliable. This is more work. This is extra. This is, well, me. 

## Hidden Benefits

My short term memory isn't great. I have ADHD and there are still medical studies that are now being published of all the dumb stuff I did as a child, I am sure. When drinking from a fire hose and learning something as vast and fast as cyber security it was a challenge managing information. I forgot more than I learned my first couple months chasing cyber. This was my solution for that. 

This strategy has helped in my professional and personal life. I recently did a semester of college, and having a solid second brain strategy was crucial. If my biological brain fails to recall the information I need then I can recall it with my second brain. Take a look at all the tools that you have learned and try to realize that new tools are developed every day. Today's best can be replaced in a blink of an eye. Everything is volatile and trying to remember every syntax for every tool will burn you out quick. Depending on something written by you and for you will alleviate one hurdle and big stressor when learning something new. 

## Conclusion

The point of this article was to bring awareness and help you build your strategy. There are no wrong strategies because everyone's brain works differently. Remember in this fast pace world you have a finite resource with energy and brain power. The more you can offload to a second brain the more you can focus and retain the important information.

