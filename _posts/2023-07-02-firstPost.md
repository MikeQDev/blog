---
layout: post
title:  "First post - Why I'm starting this blog"
categories: People
tags: Introduction Observability Politics
author: Mike
---

* content
{:toc}

## Leading up to this
Being a full-time software engineer since mid-2017, my practices, impact, and mission in the field have evolved (for the better.)

In the early years of my career I was a general software engineer -- a jack of all trades, without specialization. In early 2021 I applied for a software engineer position at an insurance company. One thing that made this role different from the others was that it introduced a new concept I hadn't heard before: SRE (Site Reliability Engineering). While this role wasn't exactly an SRE position, it aimed to bring SRE culture and practices to the organization... But that's a story for another time.

## Diving into observability
In mid-2022 I heard about OpenTelemetry (OTel). The idea of observability excited me, and even more: OTel was an open (and evolving) standard. I hacked together a quick PoC demonstrating distributed tracing and metrics. It was cool (a term I dislike when it comes to engineering), but I didn't fully understand how to utilize OTel to reap the vast benefits it offered. There was much more to it. I poked around the internet reading articles, listening to podcasts (commonly, "O11ycast"), and watching videos on the subject.

Gaining confidence and knowledge in the observability space, I was excited to share what I had learned with the company. I started by comparing my learnings against the existing practices at the company, and the differences were astronomical. Though the company operated on fairly legacy software and was just dipping it's toes into cloud-native/modern architecture, the company was still quite behind on practices when it came to greenfield applications. This seemed like fertile ground to drive change.

## Pushing for change
... of course, only after overcoming the hurdles that quickly presented themselves. A couple of hurdles that first come to mind (*from my perspective*) were politics, company-wide lack of education (regarding modern observability practices), and the unwillingness to depart from traditional monitoring practices.

Applying traditional practices in modern systems is not scalable. Learning from SRE + observability experts in the industry, then seeing some of the "stategy leads" at this company run the strategy in a seemingly opposite direction at times was disheartening. Not to come off as brash, but way we we operated our "modern" systems was *inexcusable*. Seeing some of the practices pushed for at this company made me want to pull my hair out, and once in a while, it still does. I remind myself this sort of evolution is a sociotechnical challenge - in this case: less so of technical, and more so with people & culture. We should've been doing much better, and we weren't.

## Wrap up
So, the primary reason I've started to blog is to share knowledge in engineering areas that I'm most passionate about. While most of what I write are opinionated views of my own, I *highly* encourage criticism. Everyone needs to be marching to the same beat to get to where we need to be.

The other two reasons for this blog are byproducts of the first:
- Networking - building relationships with others who share a similar passion, or are simply looking for ways to incorporate these practices into their own organization
- Knowledge retention - writing/explaining thoughts helps me remember them better. Also, having an easy way to share and review prior posts helps recall/propagate information in a concise way

No more settling for mediocracy. This is finally an opportunity to share knowledge, build synergetic relationships in this emerging field of focus, and improve modern software engineering for all.

\- Mike
