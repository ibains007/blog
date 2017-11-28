---
ID: 4
post_title: Immutable infrastructure
author: Indy Bains
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/immutable-infrastucture
published: true
post_date: 2016-05-25 16:33:13
---
&nbsp;

<b>What is immutable infrastructure, and is it achievable?</b>

Seems like this is flavour of the month (year) at the moment, but what actually is this and how do we intercept this into our productions services?

To discuss this in more detail lets first look at what the word ‘immutable’ means.

The dictionary definition is:

‘<i>Unchanging over time or unable to be changed’</i>

So wait, what? Immutable infrastructure; does that really mean running infrastructure that you cannot change? Whats the point of that, you may ask?

Well, not quite. Immutable infrastructure refers to the practice of treating your infrastructure components as you would any other software component. So that once a version of that component has been built and deployed it does not change. You do not, for example, make code changes and re-compile your code directly on you production environment (I hope!), you go through the full development lifecycle.

With this concept, infrastructure is treated in exactly the same way. To make a change to that infrastructure component a new version is built, tested and deployed. The components aren't really immutable, we just act as if they are. Technically there isn't anything from stopping me logging on to a server and manipulating it. However it is your build and deploy process the enforces immutability.

<b>Do we need it?</b>

The concept of immutable infrastructure was ‘<b>born in the cloud</b>’, and has really been driven out of the challenges faced when building modern environments. Challenges such as:

<i>Predictability - How can i ensure what has been deployed into production is the same as what has been tested. </i>

<i> Scaleability - How can i rapidly scale up and down my infrastructure to match my ever changing demand   </i>

<i> Self Healing - Failures happen. How do I build an environment where the components can recover from a failure, without effecting the service.</i>

<i> Public Cloud - I no longer have dedicated kit running my environment, I run on a public cloud because its cheaper and more cost efficient. That does mean that i can no longer manage resource contention.</i>

Immutable infrastructure addresses the issues above and i will discuss how in more detail. But first lets have have a look at some of the key concepts that need to be bought into, if we are to go down this route.

<i>The build process for your infrastructure components is your deliverable artefact, so the exact same approach must be used for all environments to ensure what you have tested is what you are deploying</i>

This really means treating your infrastructure-as-code and moving away from manual server builds. You wouldn't (shouldn’t) build your applications manually so why continue to do that with your infrastructure? Put your server configuration under configuration management, so you have a repeatable and automated build process. Tools like Puppet, Chef, Ansible and Saltstack do exactly that and their popularity has skyrocketing over the last decade.

<i> The components should not persist any long term data as they will be re-provisioned when they are upgraded. All persistence should be shipped off to external services.</i>

Any sysadmins reading this will tell you that there is much more to a server than just the application it runs. You need to analyse system logs to investigate failures, you may need to audit user actions, you need to apply operating system patches. These are all valid points and also drives the point:

<b><i>Not all of your infrastructure can be treated as immutable</i></b>

What that means is services such as databases, credential stores, message queues, and log management services can not be treated as immutable. They can still be built using an automated, configuration managed approach, but you probably wouldn't want to destroy all you data every time you upgrade.

For everything else, ship all your logs and anything else that needs to be persistent elsewhere. That leaves the question of operating system patches. These should be incorporated into your build and deploy process, whereby patches are managed under configuration management and deployed following the same process as your application code.

<b>Build vs Runtime configuration </b>

There is still something that I have not yet touched on, and for me its the most important part. No matter how much we try, there will still be parts of our infrastructure that cannot remain constant across all environments. The extent of this will depend on the type of applications stack you run, however, there will always be configuration that needs be environment specific, such as
<ul>
 	<li>Database passwords</li>
 	<li>DNS Entries</li>
 	<li>NTP servers</li>
 	<li>Certificates</li>
 	<li>Backend Credentials</li>
 	<li>Cluster members</li>
 	<li>Network addresses</li>
</ul>
Lets pick passwords from the above list. Your development database password won't be the same as production, so what do you do? Do you create two deployment configurations? one for development then one for production? Maybe, but this approach brings you its own challenges.

How would you then ensure that the only difference between development and production is the password?

The most likely occurrence will be that the development teams will have to manage two streams of code, just so that they can keep the passwords managed. Throw in multiple releases and multiple environments, and this becomes a code merge nightmare! Not to mention that this moves away from the single artefact in all environments. Following a fully ‘pre-baked’ component also has other draw-backs such as how do you update you database password, following a mandated password expiry policy? Do you have to re-build, re-test and re-deploy every time? That will become unmanageable very quick. We have only looked at password changes, throw in the other environment specific config and the whole concept starts to look very unappealing.

This leads on to the crux of this article; how do you implement immutable infrastructure without creating a process whereby every change has to go through the full build &amp; deployment lifecycle?

The answer is you split the configuration into two buckets:
<ol>
 	<li>Build-time configuration</li>
 	<li>Run-time configuration</li>
</ol>
These can be thought of as; build configuration is all the ‘stuff’ that should remain constant throughout the environments and will never change in production. And runtime configuration as the ‘stuff’ that has to be environment specific or will change frequently but doesn't require the component to be re-provisioned. Examples of each are:

Build-time config:
<ul>
 	<li>Package versions</li>
 	<li>Service users and groups</li>
 	<li>OS Patches</li>
 	<li>Middleware components</li>
</ul>
Run-time config:
<ul>
 	<li>Backend Passwords</li>
 	<li>NTP servers</li>
 	<li>Backend service address</li>
 	<li>Certificates</li>
 	<li>Load balancer backend services</li>
 	<li>SSH keys</li>
</ul>
This allows for what I called a ‘half-baked’ component to be created. This allows for all the config to be stored in configuration management. When an instance of a component is deployment it will use the combination of the build and run configuration. This allows for much more finer control over your infrastructure configuration. This approach also allow for an easier approach for managing your production environment. For example if you need to renew a certificate you can do so without re-building.

The benefits of a split of build and run configurations helps resolve the issues mentioned earlier.

It also allow for a separation-of-concerns. For example the development teams can manage the build-time configuration and the ops team manage the run-time configuration. (This is still applicable in a DevOps setup as the product teams will be responsible for managing both the run and build configuration).

<i>Predictability - Your deployments are all versioned so you can ensure the component is functionally the same as what was tested. The ability to run the same exact build configuration in multiple environments with confidence that the only differences are environment specific.</i>

<i> Scaleability - As your components are all built exactly the same in each environment, scaling becomes easier. You can move away from working with specific servers but rather work on managing swarms of servers all performing a single role. As you now have the ability to manage run-time configuration of  your entire swarm from a single source, traditional server config fragmentation concerns are no longer applicable.</i>

<i> Self Healing - When working with swarms of identical servers, you can specify operating parameters of each of these swarms. For example you may have a minimum swarm size of 10, if a server fails  the number of healthy servers drops below 10, this will then trigger the orchestration service to commission another server to replace it. This will ensure that your service is still healthy, albeit you will probably need to do some investigations if this becomes a regular occurrence. </i>

<i> Public Cloud - Capacity planning is no longer a viable way of ensuring throughput in a shared tenancy cloud. The number of resources you need to service the same amount of transactions can vary day per day, as you no longer have control or visibility of the entire physical infrastructure. Therefore the ability to run identical components and to scale each component is key to ensuring that the required throughput is achieved.</i>

So there you have it, my thoughts on immutable infrastructure.
<ul>
 	<li>Is it truly immutable - no</li>
 	<li>Will this work for my environment - probably</li>
 	<li>Do i need this - absolutely</li>
 	<li>How difficult is this to achieve - Well, that depends on your organisation. As always the technology is not the difficult part, the challenges are faced with the cultural mind shift that goes with this approach.</li>
</ul>