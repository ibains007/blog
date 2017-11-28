---
ID: 66
post_title: 'Automation &#8211; Nice to have or Essential?'
author: Dave Stokes
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/automation-nice-to-have-or-essential
published: true
post_date: 2016-06-14 06:49:08
---
So here goes, my first blog.

<img class="size-full wp-image-73 alignleft" src="http://blog.quantum-platforms.com/wp-content/uploads/2016/06/Untitled-Diagram.png" alt="Untitled Diagram" width="122" height="102" />A couple of fellow Quantum Platformers have blogged about <a href="http://blog.quantum-platforms.com/devops-overcoming-the-hype-in-an-enterprise-world-part-1">DevOps</a> and <a href="http://blog.quantum-platforms.com/immutable-infrastucture">Immutable Infrastructure</a> which got me thinking about automation and some of the things that I have come across over the last couple of years.  So, is automation "Nice to have" or "Essential"?
<blockquote>It's nice to have, it takes time to automate stuff</blockquote>
<ul>
 	<li>Build it manually we're only going to build this once</li>
 	<li>We can build it manually and document the approach</li>
</ul>
I have heard a number of times people question why automate, we're only going build this once so lets do it manually it's quicker.  The concept of building once is usually flawed, we (nearly) always build a test environment and in most cases we need resilience in the live environment so by my reckoning once is already three times.

Building it manually and documenting the steps is always an interesting one.  I am sure there are quite a few people who have ended up needing to rebuild an environment or a server from a build document that details all the steps.  Works everytime - so what's the  problem!!  Well, reality tends to be that it's not that simple because when Bob wrote the documentation when he built the server but then when you follow the steps you realise that Bob forgot a few steps because they were really obvious - well to him at least.

So, with a couple of examples automation is starting to look appealing.
<blockquote>Automate everything, it's the only way</blockquote>
Automating everything is certainly an appealing approach because it means everything will be built the same every time.  We can spend time making sure we can install components, upgrade them in situ and remove them when we need to.  Our options are endless - but this is one of the problems.

Lets spend time automating everything and make sure we can build this environment, server, service or whatever from scratch without any manual intervention.

The opposite model of automate everything can have it's drawbacks because in the Agile world and Minimum Viable Product automating everything can be time consuming.

Automating everything can lead to complex automation scripts that have many options and therefore need significant proving and testing.
<blockquote>Be pragmatic - automate where appropriate</blockquote>
The reality is that you need to find a happy medium.  No automation is definitely not the right place to be, here are few reasons why:
<ul>
 	<li>Repeatable builds in all our environments means we can prove our Platform Builds and increase confidence in our implementations into the production environment.  Yep - "Infrastructure as Code".</li>
 	<li>Cloud computing has changed the way we look at things because it is now easier to stand up new infrastructure.  Gone are the days when we had a single test environment on infrastructure that lasts for years and years.  Cloud means project teams can have their own test environments on demand, making cost effective means you need to tear down the environments as well.</li>
 	<li>Immutable infrastructure as a concept means we need to have the ability to version control and upgrade our infrastructure as and when we need to.</li>
 	<li>The ability to move Cloud providers may become a commercial requirement sooner rather than later.</li>
</ul>
As you can see these are a few examples that highlight the need to make use of automation and provide a repeatable approach to delivering your platform is more an "essential" than a "nice to have".

The key is to be pragmatic when approaching automation, spending too long can be counter-productive trying to cater for all those edge cases that you can think about.  Take time to build the platform using "Infrastructure as Code" but equally think about what is needed to build the Platform for today.  Just as important is to ensure you bring the other parts of the organisation on the journey with you because just as we take software through test environments you should do the same with the Platform.

Hopefully, this will help you in any discussions you might have when you're in meetings with some of the doubters regarding automation.

So, that was my first attempt at a Quantum Platform Blog.  Keep a watch for some up and coming blogs that talk about some of the tools we have available to us.