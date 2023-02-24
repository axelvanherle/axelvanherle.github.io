---
title: How i got this website up and running.
tags: ['webdev', 'devops', 'howto']
date: 2023-02-20
author: "Axel Vanherle"
---

Hello, this post is an explanation of how I got this website up and running.

If you are reading this, the chances are that it is hosted on GitHub Pages. While there are numerous web-hosting options available, I preferred GitHub Pages for this project because of my prior experience with it. Additionally, it enables me to publish posts efficiently as I only have to place a markdown file in the correct folder, and GitHub actions, in combination with Hugo, do the rest of the work.

The inspiration for this website came from [this video](https://youtu.be/ZFL09qhKi5I). I've been watching the content for a while and stumbled upon this video. I've always been interested in the world of web design, but it's always been somewhat of a foreign concept to me. I don't have the time to learn HTML, CSS, JS, or any other web design frameworks like React or whatnot. And to be honest, I don't really want to. So, when I found a tool like this, it sparked an interest in me to make a website like this.

Once I had a good grasp of Hugo, setting up the website was straightforward. I spent some time familiarizing myself with the tool, and once I understood it, setting up the website was a breeze. It took less than 30 minutes.

So how did i do it?

First, you need to create a GitHub repository for your website. Once you have created the repository, you can add a Hugo site to it. Hugo is a popular static site generator that allows you to create websites quickly and easily. You can install Hugo on your local machine and use it to create your site.

Next, you need to choose a theme for your website. You can browse and select a theme from the many available options. Once you have selected a theme, you need to add it to your Hugo site.

After adding the theme, you can use GitHub actions to automate the building process. GitHub actions are a set of scripts that can be triggered by certain events. In this case, you can set up a GitHub action that is triggered whenever you add a new post to your site.

The GitHub action will use the Hugo configuration and theme you have set up to build your website automatically. Once the website is built, the GitHub action will publish it to the gh-pages branch. This is a branch that GitHub uses to host websites.

Once the website is published to the gh-pages branch, another GitHub action is triggered that renews the GitHub page. This process is very fast and happens in under a minute, so your website is updated in real-time.

If you have any questions, please don't hesitate to reach out to me via my About page.
