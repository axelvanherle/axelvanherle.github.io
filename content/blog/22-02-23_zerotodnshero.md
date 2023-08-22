---
title: From Zero to DNS Hero; My Journey Setting Up a Blog with a Custom Domain
tags: ['howto', 'story', 'webdev']
date: 2023-02-22
author: "Axel Vanherle"
draft: true
---

I am excited to announce that we have a new website link! Our website can now be found at https://axelvanherle.xyz. In this post, I will share my journey of setting up a custom domain and playing around with DNS. So, without further ado, here is "From Zero to DNS Hero; My Journey Setting Up a Blog with a Custom Domain.

So for a while now, I have been interested in setting up my own website or, at the very least, learning how to do so. Setting up a blog seemed like a good way to achieve this goal.

To me, normally, setting up a website involves getting an old Optiplex computer, installing Linux on it, and serving my page with a web server like Caddy or Nginx. Doing this results in getting a boring old IPV4 address, but I always wanted a cool custom domain name, because who wants to go to a random IPv4 address?

The idea of a custom domain name has always been there, but it wasn't really necessary with GitHub Pages since it gives you a clean link to start with. In my case, it was `axelvanherle.github.io/`. However, I wanted to play around with setting up DNS because it seemed like a cool thing to do, and I hadn't really worked with it before.

So, I bought my domain name, `axelvanherle.xyz`, and got to work. The first thing I did was go to the GitHub Pages documentation to figure out how to set it up, and I followed the tutorial. At first, I didn't understand a word of it, but after doing some more research, I got the hang of it. I configured my domain in the admin panel, set the CNAME and A record to what GitHub told me to, and I was off.

However, problems started to arise. My blog was set up using Hugo, and I had a main branch on GitHub where I could configure it. With any changes, a GitHub action would trigger, which would build the website and push it to the gh-pages branch, updating my blog. But the CNAME file needed to be in this branch, and the GitHub action caused it to be deleted. Luckily, there was a simple fix - I just needed to put the CNAME file in the static folder on the main branch, and the crisis was averted.

Now, the real trouble began. Everything was set up correctly, but some people couldn't access `axelvanherle.xyz`, while they could access `www.axelvanherle.xyz`. Interestingly, both worked for me and some other people. After researching the issue, I asked a professor at my college (and a friend) what could be wrong. It turns out that it had something to do with DNS propagation, which made sense to me. I suspected it might be some sort of cache issue, but I didn't know the official term or phenomenon to describe it.

My journey of setting up a blog and playing with DNS was a fun and educational experience. Although I encountered some challenges along the way, such as configuring the CNAME file and dealing with DNS propagation, I learned a lot about how websites work and how to set up a custom domain name.

If you are interested in setting up your own website or blog, I encourage you to give it a try! There are plenty of resources available, and with some persistence and research, you can achieve your goals. Who knows, maybe you'll end up with a cool site like mine - axelvanherle.xyz 😉.

To end this post i leave you all with a haiku;

>It’s not DNS 
>
>There’s no way it’s DNS
>
>It was DNS
