---
layout: post
title:  "GitHub Universe 2025"
date:   2025-10-30 00:00:00 +0000
categories: update
---

I expected GitHub Universe to be all talks about AI, and AI tools and not very
technical. The talks were hit or miss. Sometimes they were well structured, and
some were all fluff. I was happy to see a few talks that were technical, _and_
I was able to attend some talks that were about building developer community. I
really enjoyed the talk by LEGO. The presenter walked through the different
strategies implemented at the company to drive collaboration and product
development.

The layout of the conference was strange. There isn't a single conference room.
Instead they booked a _campus_. It has several  buildings and sometimes talks
are difficult to get to. They can either be far apart, or I just get lost
getting to it. For example there was a building that has a large conference
room on the north end. However GitHub blocked off a section of the building for
GitHub partners. So to get to the conference room on the north end, I needed to
go outside, and walk around the partner area. Below are some notes from a
couple of the talks I found particularly interesting.

## Developer Experience at LEGO

I particularly enjoyed the talk on building developer experience at LEGO. The
company broke down silos and simplified efforts into products. Product teams
are a cross-functional group that have the people and knowledge necessary to
develop and maintain a product. They split the initiative into 3 main concerns.
Discoverability, Community, and inner-source / documentation.

### Discoverability

Finding the right person to help with a problem is often a friction point for
developers at any large company. It can be difficult to find the right person
to message for any particular problem. It can also be difficult to find
existing packages or APIs you can leverage that already solve your problem. Of
course the tool used is a heavily customized Backstage instance. Backstage is
where you can search for documentation, apis, packages, or other products. Not
only does it make everything discoverable, but it serves as an onboarding tool
for developers new to your product. Communities are also an important
discoverable catalog item in backstage.

### Community

Building community is difficult. To have a community you need to have people
that are driven to work together. It requires people with a shared interest and
energy to thrive. Communities drive newsletters, dev fairs, and an annual
devcon where developers can come listen or speak to their peers about a topic.

### Inner-sourcing and Documentation

Inner-sourcing helps prevent the "Waiting for someone" problem. Often
developers find problems with a dependent package or api that needs to be
correct before the work can continue. Most of the time developers are happy to
contribute to the dependent project to not only lift the block, but also to
contribute back to the larger community of projects using the dependency. For
inner-sourcing to work, each project needs to define contribution guidelines to
facilitate incoming change requests from other developers.

## CodeQL

CodeQL is a tool I have been using for some time now, but I wasn't aware of the
feature that allows for custom extensions. The feature allows for you search
for specific code patterns and alert developers on what was found. The
documentation provided to the developer is included in a markdown file and can
include as much information as necessary to explain why it's an issue. I am
currently using CodeQL to look for accidental commits with secrets in them, but
I wonder if I can use CodeQL to look for or help teams with migrations. There
will always be documentation that is out of date, and can lead development
teams down the wrong path. With CodeQL I think we can improve the adoption of
newer practices.
