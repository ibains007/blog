---
ID: 137
post_title: Automating the loss of knowledge
author: Jon Treharne
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/automating-the-loss-of-knowledge
published: true
post_date: 2016-06-09 19:19:50
---
Well, on behalf of DevOps engineers, I can confirm we’re not attempting to automate the loss of knowledge! But this seems to be a growing issue the more we automate and has maybe been around for years, let me explain…

There was once a time where everything was manual (well nearly everything), this led to time consuming tasks
including coding, compilations, deployments and platform configuration amongst others. But behold the power of automation, from shell scripting and Hudson, to Puppet and Consul. These are just an example but the new era of infrastructure as code and automated deployments have saved thousands of FTEs.

But has this had a negative effect?
<h2><strong>How can this be negative?!</strong></h2>
Of course, for the clients/businesses and end users this is amazing, 24/7 services with elastic potential during peak periods and automated backups etc. We are at a time where downtime isn’t really a word anymore. The negative is the developer, the platform engineer or the tester losing the knowledge.

This <span style="color: #ff0000;">SHOULDN’T</span> be the case but I have witnessed on many occasions. The natural human laziness kicks in, and where teams have been hitting an automated build for 2 years without fail, suddenly it has broken. The guy who had written it has moved on. It now becomes apparent that no one knows what that build does, other than give us a shiny new rpm or micro service. This is obviously a simple example, they can just check the scripts and configuration to see what it is doing but the theory remains the same.
<h2><strong>You said growing trend?!</strong></h2>
Yes, yes I did. If we look back to only the early java days, developers or as they were called “programmers” would be cutting code in notepad or the likes and manually compiling, this forced the programmer to gain a better understanding of syntax and java imports and methods. The new breed of developers have powerful IDEs that compile on the fly, make suggestions and even auto fix code, they don’t need to know as much now.
<h2>So what do we do about it?</h2>
This is similar to any kind of change, the individuals need to adapt and change their culture. They need to be eager to understand what they are doing, not just the end result. How is the micro service made? What does the script actually do? How is this automation helping?

Automation is great but let us not forget what it is saving us from. We need to encourage our colleagues, peers and new talent into wanting to understand what automation does for us, look beyond the scripts and use some commands. Ultimately understand the changes that we have now hidden from ourselves, take the time to learn because when the button no longer works, we don’t have the luxury of time.