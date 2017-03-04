---
layout: post
title:  "The Stack Beneath This Site"
date:   2017-03-04 18:00:00 -0000
categories: news tools
---

This post is about the tools and services used to build and publish this site
plus some brief mentions of alternatives and why they weren't selected. It's a
soup-to-nuts quickie of all of the services and tools used to create, store,
and publish this website. [Email](mailto:blog@lucid-operations) me or 
<a class="twitter-share-button"
  href="https://twitter.com/intent/tweet?text=@LucidOperations">
Tweet</a>
your comments!

I'll cover the following layers

  - Domain Registration: How you reserve you domain name. In this case lucid-operations.com.
  - DNS service: This is how you associate your domain name with a specific IP (IP6?) address on the web.
  - Mail: This is how you manage mail to your domain. And yes, some people do still use email.
  - Framework: This is how you convert your content into a viewable website.
  - Hosting: These are the services which serve up that website.
  - Local tools: The programs I use on my local machine to test and build this website.
  - Cost:
  - Future: Some thoughts on changes and directions.

## Domain name registration

I chose [Gandi](https://gandi.net) for the registry of domain name. I liked
that their website wasn't littered with upsell and they had technically
oriented help pages. Obviously there's a huge plethora of registrars from which
to choose. If you're happy with the price and their systems just choose one.

## Mail

A long while back I switched most of my mail to
[FastMail](https://www.fastmail.com). Their service is really reasonably priced
and you don't have the [Big-G](https://www.google.com/) (or is it the
[Big-A](https://https://abc.xyz)) reading your mail over your shoulder. Their
servers are fast and I've had a great experience with them so far. I actually
have them serving as the primary DNS for lucid-operations.com, but you could
just as easily have Gandi do that and direct the MX records to FastMail.

## Framework

  - [Jekyll](https://jekyllrb.com): I had a few requirements for this:
    - I didn't want to have to build out a server (or multiple servers).
    - Most of the content is simply content so I didn't want a backend database
      with all the associated issues which happen with that.
    - I wanted to be able to test changes locally.
    - I wanted to be able to write my posts in Markdown format

Some options which I considered are rejected were:

  - [WordPress](https://wordpress.com): I wanted control over my content and
    files. I also didn't like the idea of having to either host a full blown
    stack or cede fine-grained control over to some wordpress host.
  - [SquareSpace](https://www.squarespace.com): And similar services
    [Wix](http://www.wix.com), [Weebly](https://www.weebly.com), etc. have nice
    WYSIWYG interfaces but ultimately they cost you money and control. They do
    offer really nice templates and a simple set up. More concretely, I wanted
    to be able to update my website using a git push and none of these offer
    this that I know of.
  - [Ghost](https://ghost.org): A couple of companies I've seen are using them
    as their blog homes. They have a nice markdown based editing system and
    management system. I could see migrating my content to ghost if the
    Jekyll/GitHub-Pages system and I part ways.

## Hosting

I decided to use [GitHub Pages](https://pages.github.com) because I already had
a [GitHub account](https://github.com/danslee) and I liked the idea of a static
website over which I had maximal control over the content.

I considered building and deploying an image to [AWS](https://aws.amazon.com)
with [NGINX](https://www.nginx.com) and running this website as a jekyll
generated static website, deployed as a docker image and served by an nginx
(docker) instance. But although it'd be interesting, I don't think it would
offer any real advantages over just using GitHub Pages. I'm already storing in
GitHub, and I'd just be adding a half-a-dozen or more steps and things which I
have to manage, update, and maintain to keep my website up.

That said, if I wanted to run some dynamic web apps, I would probably move
these pages down to a sub-domain and put up a master page with links as needed.

## Local Tools

These are the tools I use on my local machine, an aged but still remarkably
sprightly MacBookPro (Retina).

  - Mac Terminal.app: It works. I use the system wide clipboard and pbpaste/pbcopy widely and wildly.
  - [bash](https://en.wikipedia.org/wiki/Bash_%28Unix_shell%29): Because it's
    universal and does most everything I need.
  - [tmux](http://tmux.github.io): Because one shell isn't enough.
  - [git](https://git-scm.com/): to control and keep track of changes to the site.
  - [Jekyll](https://jekyllrb.com): has a nice built in server you can run after generating the site.
  - [MacVim](http://macvim-dev.github.io/macvim/): To edit the files, because
    I'm both a mac head and an old \*nix head. In one head.
  - [Solarized](http://ethanschoonover.com/solarized): Because I like
    consistency. In fact, the basic theme for this website started out as a
    solarized jekyll theme which I desaturated.
  - [Safari](https://www.apple.com/safari/): this is probably sub-optimal as
    both Chrome and Firefox have really solid dev tools. But I only needed to
    be able to dive into basic elements and css definitions and this does the
    job.

## Cost

Aside from the domain name registration, this website doesn't cost me any money
to serve. Even adding in CloudFlare for https support shouldn't cost me
anything.

## Future Thoughts

As mentioned above, I could see migrating to [Ghost](https://ghost.org) in the
future, but although it is open-source and all that, I'm leery about having to
take on the whole stack of backend stuff to run my static blog. I like that I
can test changes to this blog with one jekyll command.

One of the drawbacks of using github pages directly is that you can't get https
to your custom domain.
[CloudFlare](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/)
has a solution for this which I will be testing out next week.
