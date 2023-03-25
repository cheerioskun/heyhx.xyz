
---
title: "Blogging Pipeline"
date: 2023-03-25
draft: false
---

# Creating a blogging pipeline from within Obsidian

I have wanted to start blogging for some time now but the friction of getting a domain, setting up a pipeline and *then* getting to the writing had been delaying it for me. It's one of the [asymmetric opportunities](https://twitter.com/naval/status/1054984950192181248) that feels the most accessible to me.

## Deciding on a flow

I started by trying to hash out what I wanted the flow to be. I wanted to answer these questions:

- Where would I write my articles
- Where would my server pick them up from
- Where would I end up seeing them

### Where would I write my articles
This was probably the easiest one, as the title suggests. I am very enthusiastic about Obsidian and it was primarily the trigger for me to start writing my thoughts. I wanted the richtext, markdown way of formatting to be preserved through the pipeline and look as appealing as it would on my desktop app.

### Where would my server pick them up from
This was also not that complicated to reason. I want my blog to be public and version controlled so I can just put it on Github. Github actions to regenerate the static site whenever there is a new commit.

### Where would I end up seeing them
I bought a domain today: `heyhx.xyz`
I have access to the DNS records so I will brainstorm where to host now. Maybe EC2 free tier will be enough. Or maybe I can just use some lambda functions to plumb. Anyway, I will set up a server with proper TLS certs and everything in the coming days. Will also document this process.

## Update : March 25, 2023
### Revisiting the pipeline

This effort got lost because I probably grew bored and distracted but coming back to it now, I have finally set everything up! Here is how it finally turned out

### Creating the website

I set up a new [Hugo](gohugo.io) project and a git repo to host my website.

```zsh
hugo new site heyhx-blog
cd heyhx-blog
git init
git remote add origin <your repo>
git push -u origin master
```

You can have a look at basic configurations for hugo (or ask GPT to help) and once you are set up you can start creating the deployment actions.

I realized I did not need a server at all since I don't have a backend. Github pages would be good enough for me as I only have to host static content. Github actions are pretty nice in this regard. They just had a Hugo workflow available which I used as is.

I added the Archie theme for now and am planning to iterate on the design and feel and performance now that I have the basic setup down.

### Hooking up my custom domain to it

I asked GPT to help me in this section heavily. I needed to get redirected from my domain `heyhx.xyz` to the where my site was hosted `cheerioskun.github.io`

The steps it had me follow were to 
1. Add github pages' serving IPs to my DNS A records. (`185.199.108.153 185.199.109.153 185.199.110.153 185.199.111.153`)
2. Add a CNAME record with Name: `blog` and Value: `cheerioskun.github.io`

The way the plumbing works now is
1. User tries to go to `blog.heyhx.xyz` and the browser makes a DNS query
2. The DNS record resolves the URL to the github pages server IPs
3. The request has the authority or host header as your custom domain name: in this case `blog.heyhx.xyz` . This is important because we configure github pages to host on this domain and subsequently, it has provisioned a certificate with that as the Common Name.
4. This authority header is also how github pages will figure out what content it has to serve. Since all the pages (for other users too) share the same IP. This is something called virtual hosting.



