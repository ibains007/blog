---
ID: 31
post_title: 'DevOps &#8211; Overcoming the hype in an Enterprise World (Part 1)'
author: Paul Jacques
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/devops-overcoming-the-hype-in-an-enterprise-world-part-1
published: true
post_date: 2016-05-31 07:49:46
---
<strong>DevOps</strong>?  Wow, one word (or is that two?) that seems to be on the lips of everyone in the industry… I’ve heard views on this from Developers, Bid teams, Managers, Architects, Engineers and Operations staff.  All agree and practically shout “We should do it”.  Though they all come from different worlds, have different views on what it is, how it can help the organisation and most importantly how it’s achieved.

<strong>Take One</strong> - Within a few months of this term bounding round the organisation we had our first ‘DevOps’ Team.  Yay.  We’ve arrived.  But wait, the team consists of a handful of individuals, none actually developing applications or supporting live services, some scripting sure but not exactly automation heaven.  Hmmm… scratch that, perhaps we still have work to do.

So, time to revisit.

<img class="alignnone size-medium wp-image-32" src="http://blog.quantum-platforms.com/wp-content/uploads/2016/05/Take-2-300x225.png" alt="Take 2" width="300" height="225" /><b></b><i></i><u></u>

<strong>Take Two</strong> - Let’s sell ‘DevOps’, that’s the ticket, everyone wants it, so we’ll provide it.  Great, let’s go win the work then figure out what we’ve sold and who will deliver it after?!  This isn’t going well…

Deep Breath.  Time to revisit. Again.

<strong>Take Three</strong>? Please!

So now we begin to consider the question more.  What is DevOps?  Who can do this stuff? How do we build capability? How can we work in a way that supports this Cultural Revolution? And that dreaded question: WHY do this at all?

&nbsp;

<strong>What is DevOps?</strong>

Ha... Plenty of opinions on this one, but it is fairly key wouldn’t you say?  Trawling through the various definitions on the Internet, listening to speakers at multiple conferences and following many many MANY ;) conversations amongst the people with an opinion there are some key thoughts that are repeated:

<strong>Culture</strong> – The hardest of the lot to achieve is the first thought when discussing what DevOps is.  Hard?  Well yes, remember the blog title ‘…In an enterprise world’.  Consider a small software house, limited funding, limited resource, everyone has to chip in to help develop, test, deliver and support what’s being produced.  That’s the environment they live in; they’ve handpicked the right people, with the right mentality to start with.  DevOps Culture comes naturally in this instance.

Now expand that to 1000 people, 10,000, 100,000… now you have a wealth of people, with different levels of ability, some keen and eager, some skilled (some less so ^^).  A culture change at this level becomes much harder, though I believe is required more so than within the smaller software house!

So what Culture are we looking to move to?  What do we want people to actually do?  A couple of things really…

<strong>Automate - </strong>Everyone should be looking at their practices, processes, and workflows across the entire lifecycle and seeking to automate any manual steps.  Sounds easy =)  Well perhaps not, but there are plenty of tools to help and good examples of successes in this area.

<img class="wp-image-33 alignright" src="http://blog.quantum-platforms.com/wp-content/uploads/2016/05/puppetlabs.jpg" alt="puppetlabs" width="213" height="213" />

For instance, the rise of automated configuration tools such as Puppet, Chef and Ansible allow us to (along with a few other tools) stand-up consistent environments within minutes, 13 minutes in the case of an environment we stood up recently on a public Cloud Provider consisting of Load Balancers, Service Discovery &amp; Monitoring Tooling, Application Services, and all DNS registered to allow instant connectivity from across the internet.  Sounds great?  It is, but to be fair (and to agree with what some of you may be thinking whilst reading this) we did have to spend a fair amount of time getting the configuration to a point where we could achieve this level of consistency at this speed.

Either way, the ability to automate your tasks surrounding the actual development of application code allows you to master what I like to call <strong>Continuous &lt;X&gt;.  </strong>Sounds like a Marvel Character!  Perhaps it should be, the super hero for the IT Industry - Continuous Integration, Deployment, Testing, Monitoring… all these and more coming to an IT system near you soon.

<strong>End to End Ownership –</strong> This one frustrates me the most.  Not because I disagree with it, but because it’s something I and a handful of my colleagues have been trying, repeatedly, constantly, &lt;sigh&gt; you have no idea! &lt;/sigh&gt; to get people in the organisation to take on board.  Essentially the same people who develop the services should take ownership not only to see them delivered into the test environments but right through into Production, and then &lt;wait for it!&gt;…

<strong><em>SUPPORT THEM IN PRODUCTION</em></strong>

Woah!  Crazy I know, imagine a developer actually monitoring and supporting their own service in Production?!  The opposite is also true, get those who support the services in Production closer to the development team, meaning as one unit a team now takes all the pain for poorly developed services, so if they don’t want to be in all hours they need to ensure the service and supporting environment and monitoring all run smoothly.

After years of trying to convince these two areas to do this by way of choice DevOps allows us to give them the opportunity (Ok, Ok, force/trick them through use of fancy new labels :-) to do this through taking full control of the lifecycle of their service.  To be fair this is one thing that DevOps also brings to these teams, the freedom and ability to self-serve.  Move away from needing a multitude of supporting cast as the team now has the ability to develop, test, deploy and support their own services, everywhere.

Phew… so that’s the end of Part 1.  In Part 2 I’ll consider the questions – Who can do DevOps? How do we build capability? And I suppose we should also consider why bother at all?

Hope you enjoyed, if so leave a like (or is that only for FB or YouTube vid’s? or are they called Vlog’s? Bah…;-)

#HowHardCanItBe