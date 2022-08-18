+++ 
draft = false
date = 2022-08-18T00:33:14-07:00
title = "Choosing Hugo for This Site"
description = "Why I chose the static site generator Hugo over other alternatives."
slug = ""
authors = ["Fenteale"]
tags = ["Hugo", "Website"]
categories = []
externalLink = ""
series = []
+++

I realized that I wanted to make a semi-professional website for myself. I won't go too far into the reasoning in this post, but basically I thought I have enough projects and experience under my belt to warrant creating something like this now. Going into this endeavor, I know I had a few requirements for a website. Whatever website I end up choosing needed to be able to do the following:
- Be able to customize most aspects of the website
- Have a way that is easy to add content without having to do a lot of boiler-plating
- Support different types of multimedia such as embedding images, videos, and even some small JavaScript demos from time to time.
- Look relatively nice out of the box since I am not a graphic designer
- Be cheap to host, so I don't have to worry about monthly fees or the like

## Hosting

I heard about GitHub Pages a while back and thought, since I end up using GitHub a lot anyways, it would probably be easy and convenient way to host my website. A friend also recommended that I register a domain to use as well, so I ended up doing that. With hosting and choosing a domain out of the way, I needed to decide on how I was actually going to build the website.

## First Attempt

At first, I wanted to try and make my website from scratch. I have had some experience working with HTML and CSS and I felt maybe I can assemble a basic website that way. I did end up prototyping this website, but due to CSS issues it didn't render the same way across different web browsers, and plus honestly it didn't look so great. I left it this way for a while since I had other projects to work on, but recently I decided to take a look at all this stuff again, so I moved the HTML I built into the [legacy](https:/github.com/Fenteale/fenteale.github.io/tree/legacy) branch and basically started over again.

{{<figure src="/images/legacywebsite.png" caption="Picture of my old website written in html." width=540vw >}}

## Switching to a Static Site Generator

I ended up reading and seeing a lot about positive experiences people have had with various static site generators, and so I started looking into it. Honestly, I feel sometimes I can be a bit old fashioned when it comes to choosing underlying technologies for projects (mainly because they are what I know best, and also they tend to be solid foundations), but I have been trying to challenge myself lately with learning newer and more modern solutions. Learning how to use a static site generator seemed like a great way to apply that with.

Right away I decided to narrow my choices down to either Jekyll or Hugo. There were a lot of choices I could choose from when jumping into the world of Static Site Generators, but I decided, for my first attempt at least, I wanted to stick with the more well known and supported ones.

[Jekyll](https://jekyllrb.com/) immediately jumped out to me as the most popular one. Looking more into it, it seems very closely related to GitHub (I believe it was created by one of the founders), and has a lot of resources on setting it up to work with GitHub Pages. There were some problems I had with Jekyll, however. First, it seemed a bit complex to setup. I didn't have Ruby installed on my computer, and it seemed like you needed to install Ruby, then install some separate modules in order to get it up and running. This was definitely doable for me, but I was hoping for something a little bit more lightweight. I wasn't trying to do anything necessarily complex, so installing all of Ruby and getting it configured seemed like a bit overkill. Secondly, it seems like there were multiple websites to check for different themes. All in all, Jekyll was a possible option but...

I took a look at [Hugo](https://gohugo.io/). Hugo advertises itself as being faster than the alternatives, which is great. Looking at the quickstart, it's just a simple program that you can install via package manager. In addition, there is a [single website](https://themes.gohugo.io/) that hosts nearly all Hugo themes, which is hosted on the official website. Even though it is not as tightly integrated with GitHub, it seems there are still resources available for setting up a site on GitHub Pages. It seemed Hugo more closely aligned with the vision and workflow I imagined in my head, so I ultimately decided to go with Hugo.

## Working with Hugo

There were a lot of creative decisions I had to make for website that I won't go over too much, but I will talk about how I was able to set things up to work with GitHub Pages and what my plan is going forward.

I decided on the [Coder](https://github.com/luizdepra/hugo-coder) theme to use. I did try some other themes at first, but some of the other more complex themes require you to install node.js and some other packages, which I really didn't want to do. I wanted to keep things simple and easy to maintain. There was one major issue with Coder, however. The Avatar displayed on the main website page had rounded corners, which made the image I wanted to use look terrible.

{{<figure src="/images/roundedavatar.png" caption="What my avatar on the main page looks like with the default Coder theme." width=220vw >}}

I ended up making a [fork](https://github.com/Fenteale/hugo-coder) of the Coder theme repository to *just* change the shape of the avatar image back to square. I searched for the avatar style definition and edited it to remove the rounded edges.
```scss
.avatar img {
  width: 20rem;
  height: auto;
//  border-radius: 50%;

  @media only screen and (max-width: 768px) {
    width: 10rem;
  }
}
```
It worked! When I ran my website with `hugo server` it correctly was shown as a square avatar, but there were some hidden issues that arose that I didn't catch until later... For now, however, it worked, so I finished up creating the basic content for my website and got ready to deploy it onto GitHub Pages.

## GitHub Pages and Actions

Luckily, there were already [documentation](https://gohugo.io/hosting-and-deployment/hosting-on-github/) on how to use Hugo with GitHub Pages using GitHub Actions in the official Hugo documentation. I followed that guide pretty closely, but I had to change references from `main` to `master` just because my repo was created before the default branch name was changed on GitHub.

So far, so good. I created a `gh-pages` branch, since I saw the static website was getting pushed to that branch, and then pushed the GitHub actions script and watched it run.

It worked! But uh oh, my avatar appeared as rounded instead of square. I double checked to make sure that I pushed the changes to the theme to my fork, and that the submodule reference pointed to the right commit... yeah that was all correct. I searched again for references to `.avatar` to see if there was anything else I missed... I am not very familiar with "scss" files, so this may be normal behavior, but it seemed like there were generated style sheets located in the `resources/_gen` directory. Were these artifacts accidentally left over from the original author? I deleted them and restarted the Action...

{{<figure src="/images/buildfailed.png" caption="The build failed." width=300vw >}}

Now I realize what those files I deleted were. When telling Hugo to build static web pages from the repo, it can't process that "scss" file for some reason. As I mentioned earlier, I don't know exactly how those files work, but I realize those generated files were the only thing Hugo can process by default. I could solve it by generating those files myself, but I decided to use Hugo-extended in the GitHub Action instead, which has support for scss files.

Finally, after making that change, the GitHub Action was able to complete and I was able to see the website on the web! I am sure in the future I will be adding and tweaking more with this website, but for now I think it's at a point that I am happy with. As with most all of my projects, the repo for this website is hosted [on GitHub](https://github.com/Fenteale/fenteale.github.io).