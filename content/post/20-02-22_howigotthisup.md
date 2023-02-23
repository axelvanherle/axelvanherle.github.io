---
title: How i got this website up and running.
tags: ['webdev', 'devops', 'howto']
date: 2023-02-20
author: "Axel Vanherle"
---

Hello, this post is a quick explanation of how I got this website up and running.

### Rambling

First of all, you're probably reading this while it is hosted on GitHub Pages. I can host this wherever i want, but since i have done a previous project like this, using GitHub Pages is the most obvious choice for this project. It's also easy for me at add posts, as i just put a markdown file in the correct directory, and github actions takes care of the rest.

It all started with [this video](https://youtu.be/ZFL09qhKi5I). I've been watching the content for a while and stumbled upon this video. I've always been interested in the world of web design, but it's always been somewhat of a foreign concept to me. I don't have the time to learn HTML, CSS, JS, or any other web design frameworks like React or whatnot. And to be honest, I don't really want to. So, when I found a tool like this, it sparked an interest in me to make a website like this - something where everyone could submit posts, and using GitHub Actions to do all the heavy lifting.

It wasn't difficult to set this up once I got the hang of Hugo. I spent some time playing with it until I understood how it worked. And once I understood it, it was smooth sailing from there. Now that i've used hugo a few times setting up a website was this was a breeze. It took maybe 30 minutes?

### How

I started with a simple GitHub repository, to which I added a Hugo site. After i had a site, i needed a theme. I had starred a bunch of themes, but this one stood out to me, it's simple, but clean. Then i added a github actions to automate the building part for me.

Basically, once you add a post, the hugo workflow gets triggered that builds the website using the configuration, theme, etc. that I set up. This workflow publishes the built website to the gh-pages branch. Once that is pushed, another workflow gets triggered that renews the GitHub Page. All of this happens in under a minute, so it's all pretty real-time.

### Questions

If you have any questions, you can ask you can learn how to contact me on my about page.

