---
ID: 238
post_title: Server less or Server-yes?
author: Jon Treharne
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/server-less-or-server-yes
published: true
post_date: 2016-07-26 06:29:26
---
This is a question that is bouncing around the ‘Cloud talks’, engineer events and conferences alike. But what does it mean for the user or the customer, what is the affect on the run time? It’s like any architectural decision, we should use it when its right to do so.
<h2 class="p1">So, what is server less computing?</h2>
<p class="p1">Of course we cannot deploy applications and functions without the use of servers, they are the backbone of all compute power, but we can hide them away from the developers and architects so that they do not need to worry about utilisation and management. Functions and APIs are deployed to the cloud and the provider deals with the compute power and elastic scaling.</p>
<p class="p1">This is natural growth into these patterns of cloud deployments using software defined networks, we’re not physically tied down to infrastructure anymore. If we roll back to 2014, Amazon released the Lambda offering, this was the main stable offering for enterprise level deployments. Since then multiple offerings have sprung into the market, Forbes list the 5 to watch out for and probably consider using in current and future projects.</p>

<blockquote>
<p class="p3"><a href="http://www.forbes.com/sites/janakirammsv/2016/03/22/five-serverless-computing-frameworks-to-watch-out-for/#1d56a4f259d7">http://www.forbes.com/sites/janakirammsv/2016/03/22/five-serverless-computing-frameworks-to-watch-out-for/#1d56a4f259d7</a></p>
</blockquote>
<p class="p1">To a user of the deployed service the end result remains the same whether they are targeting a server or server less deployment strategy. The pros that get picked up here are costing and efficiency. The provider can charge based on usage at a service level, when the service is being used, down to each request. AWS call this Subsecond Metering where they charge on every 100ms when your ‘code’ is triggered.<span class="Apple-converted-space">  </span>This is efficient, you are billed for exactly what you use, which would be exactly how much you needed to. You’re not billed for having a server running continuously used or not, like in a PAAS offering.</p>

<h2 class="p1">Sounds like you’re re-selling PAAS…!</h2>
<p class="p1">It is a similar concept to Platform As A Service but even more abstracted, sitting closely to a SAAS as well. With a PAAS offering you get your base level operating system and some optional extras, but ultimately need to manage your deployment onto the server, with containers, software and other products. With server less you just deploy your code to the provider, everything you need to do on top of PAAS is taken care of, especially at run time.</p>
<p class="p1">In essence the server less option gives you your framework as a service, a platform to run on and scaling ability, down to each request, a PAAS offering does not offer that flexibility and cost saving.</p>
<p class="p1">This is the beauty of server less, your software engineer just puts the code into the environment without needing the infrastructure knowledge, this gives the potential for amazing feedback times between development and test, even in a waterfall manner. This doesn't mean there are no ops though, you are basically outsourcing this to the provider.</p>

<h2 class="p1">So, why would we ever want a server-yes solution?</h2>
<p class="p1">With anything there are inherent drawbacks, the main drawbacks with server less is that you’re outsourcing the management to a provider, this could cause multiple issues including security of data. With such a drive into efficiency the provider will have multi tenants running on the same physical kit. Yes this is also a potential issue with cloud deployments now, but less so due to maturity and cloud development. Server less is a new concept and at an an immature stage in the scale of things.</p>
<p class="p1">Testing is another sore point to touch, how do you test your application in an integrated system test or Quality assurance when you don’t know what the actual 3rd party provider settings are going to be exactly.</p>
<p class="p1">There are more points that we could touch, such as, server optimisation, monitoring and debugging, Martin Fowler lists some good points here:</p>

<blockquote>
<p class="p1"><a href="http://martinfowler.com/articles/serverless.html#drawbacks"><span class="s1">http://martinfowler.com/articles/serverless.html#drawbacks</span></a></p>
</blockquote>
<h2 class="p1">So, server less or server-yes?</h2>
<p class="p1">Well, that entirely depends on the architectural design, if the pros out weigh the cons then go for it, you’ll save money for the client and have an efficient deployment approach.</p>
<p class="p1">Now to go and remove my IAAS configuration files…  :)</p>