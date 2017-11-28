---
ID: 90
post_title: The answer is Docker.
author: Indy Bains
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/the-answer-is-docker
published: true
post_date: 2016-06-03 19:56:00
---
<p style="text-align: left;"><img class="size-medium wp-image-91 alignleft" src="http://blog.quantum-platforms.com/wp-content/uploads/2016/06/docker-lego-whale-300x300.jpg" alt="docker-lego-whale" width="300" height="300" />The answer is Docker...</p>
<p style="text-align: left;">Does this sound like a familiar statement?</p>
<p style="text-align: left;">So if the answer is the Docker, what was the question?</p>
<p style="text-align: left;">Is it really as simple as the companies slogan?</p>

<blockquote>
<p style="text-align: left;"> Build, Ship, Run</p>
</blockquote>
<p style="text-align: left;">Before I explore this in more detail, I want to start by clarifying that I'm not against Docker, I actually think its quite cool!</p>
<p style="text-align: left;">My only qualm is that Docker is sold as the silver bullet for all our development and deployment woes. But is it really?</p>
<p style="text-align: left;"><img class="alignnone wp-image-97" src="http://blog.quantum-platforms.com/wp-content/uploads/2016/06/Screen-Shot-2016-06-03-at-20.55.03-300x128.png" alt="Screen Shot 2016-06-03 at 20.55.03" width="687" height="293" /></p>
<p style="text-align: left;">Look at the above google trends graph for the words Docker, that shows an incredible increase in the interest, and its not showing any signs of slowing down. Its like "Dark Energy", we have no idea what it is, what it does, no evidence of its existence, but we believe it to be having an exponential effect on the expansion of the universe.</p>
<p style="text-align: left;">Wait, am I really comparing Docker to Dark Energy? Well, the blog is called quantum-platforms so I need to put a bit of physics into an article every now and then :)</p>
<p style="text-align: left;">So lets start out with some facts on Docker</p>

<ul>
 	<li style="text-align: left;">DotCloud Founded in 2010</li>
 	<li style="text-align: left;">Docker was released in 2013 as an open source project</li>
 	<li style="text-align: left;">Employees in 2016: 201-500 employees (according to linkedIn - 283)</li>
 	<li style="text-align: left;">Investment funding: $160m</li>
 	<li style="text-align: left;">Revenues 2015: &lt;$10m</li>
 	<li style="text-align: left;">Company Value in 2016: $1B+</li>
</ul>
Lets face it, those stats are quite impressive. Three years to a release their initial product, managing to raise over $160m in investment funding and now valued at over a $1 Billion, despite only making $10 million in revenue last year.

Did I mention that they are worth $1 Billion? Thats a 1000 millions..
<blockquote>$1,000,000,000 !!!</blockquote>
<p style="text-align: left;">What is it that the investors know that we don't? What is it that Docker does?</p>
<p style="text-align: left;">The Docker website has the following description</p>

<blockquote>
<p style="text-align: left;">Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.</p>
</blockquote>
<p style="text-align: left;">Sounds great in principle, right?</p>
<p style="text-align: left;">But for the techies, the next question is, how does it really work?</p>
<p style="text-align: left;">The company slogan is "Build, Ship, Run", so working backwards from Run, lets delve deeper into how Docker works.</p>
<p style="text-align: left;">Explanation below lifted from their site starts to explains this.</p>

<blockquote>
<p style="text-align: left;">They [containers] run as an isolated process in userspace on the host operating system</p>
</blockquote>
<p style="text-align: left;">Does this remind anyone of chroot, thats been around for decades?</p>

<blockquote>
<div id="crayon-5753e273e8871041667241-1" class="crayon-line"><span class="crayon-e">Chroot </span><span class="crayon-st">is</span> <span class="crayon-e">an </span><span class="crayon-e">operation </span><span class="crayon-e">that </span><span class="crayon-e">changes </span><span class="crayon-e">the </span><span class="crayon-e">apparent </span><span class="crayon-e">root </span><span class="crayon-e">directory </span><span class="crayon-st">for</span> <span class="crayon-e">the </span><span class="crayon-e">current </span><span class="crayon-e">running </span><span class="crayon-e">process </span><span class="crayon-st">and</span> <span class="crayon-e">their </span><span class="crayon-v">children</span><span class="crayon-sy">.</span> <span class="crayon-i">A</span> <span class="crayon-e">program </span><span class="crayon-e">that </span><span class="crayon-st">is</span> <span class="crayon-e">run </span><span class="crayon-st">in</span> <span class="crayon-i">such</span> <span class="crayon-i">a</span> <span class="crayon-e">modified </span><span class="crayon-e">environment </span><span class="crayon-e">cannot </span><span class="crayon-e">access </span><span class="crayon-e">files </span><span class="crayon-st">and</span> <span class="crayon-e">commands </span><span class="crayon-e">outside </span><span class="crayon-e">that </span><span class="crayon-e">environmental </span><span class="crayon-e">directory </span><span class="crayon-v">tree</span><span class="crayon-sy">.</span> <span class="crayon-r">This</span> <span class="crayon-e">modified </span><span class="crayon-e">environment </span><span class="crayon-st">is</span> <span class="crayon-i">called</span> <span class="crayon-i">a</span> <span class="crayon-e">chroot </span><span class="crayon-v">jail</span><span class="crayon-sy">.</span></div>
<div id="crayon-5753e273e8871041667241-5" class="crayon-line"></div>
<div id="crayon-5753e273e8871041667241-6" class="crayon-line crayon-striped-line"><span class="crayon-o">--</span> <span class="crayon-e">From </span><span class="crayon-v">Archwiki</span><span class="crayon-sy">,</span> <span class="crayon-v">chroot</span></div></blockquote>
<div class="crayon-line crayon-striped-line">So whilst Docker isn't exactly the same a Chroot, the concept of process isolation has been around for ages.</div>
<div class="crayon-line crayon-striped-line"></div>
<p class="crayon-line crayon-striped-line">Lets move on to ship.</p>
<p class="crayon-line crayon-striped-line">Docker Containers make use of Linux Containers (LXC) that was released in 2008.</p>

<div class="crayon-line crayon-striped-line"></div>
<blockquote>
<div class="crayon-line crayon-striped-line">LXC is an OS-Level Virtualisation method for running multiple isolated <a title="Linux" href="https://en.wikipedia.org/wiki/Linux">Linux</a> systems (containers) on a control host using a single Linux kernel</div>
<div class="crayon-line crayon-striped-line"></div>
<div class="crayon-line crayon-striped-line">-- From wikipedia, LXC</div></blockquote>
<div class="crayon-line crayon-striped-line"></div>
<p class="crayon-line crayon-striped-line">This type of os-level virtualisation is not exclusive to or unique to LXC, there are similar technologies such as openVZ, Linux-Server, FreeBSD Jails, AIX Workload partitions, and Solaris Containers.</p>
<p class="crayon-line crayon-striped-line">Okay... so the containerisation concept and technology also isn't something new or unique to Docker either.</p>
<p class="crayon-line crayon-striped-line">So lets look at build. This is where Docker really comes into play. Essentially docker provides a wrapper around lxc container. As well as adding an <b>advanced multi layered unification filesystem (AUFS). </b> These two combined allow containers to be created in a much more controlled fashion.</p>
<p class="crayon-line crayon-striped-line">With AUFS each command that gets executed in a container has its own layer, think of these as git commit points. These allows for containers to be rolled back, for divergence points to be identified, and versioning is enabled by default.</p>
<p class="crayon-line crayon-striped-line">The container can be built through a Dockerfile, which allow for the containers to be held in source control. This also makes them much more portable, as you can send a Dockerfile to someone and be certain that the running container matches yours. Provided of course that you can both start from the same source container, such as one from Dockerhub.</p>
<p class="crayon-line crayon-striped-line">Dockerhub is a portal that can be thought of a repository where containers can be stored. There are hundreds of pre-built containers available, as well as containers from the all the common linux vendors, think redhat, ubuntu, and coreos. Software vendors are also creating containers with their software pre-installed, two of the latest companies to create such containers, at the time of writing, are Hashicorp &amp; Puppetlabs.</p>
<p class="crayon-line crayon-striped-line">The layered file system and the ability that it gives to version control every command executed in a container, allows for containers to be treated as deployable artefacts that are stored in a source control repository. Thus giving you Infrastructure-as-code.</p>
However, it not just the filesystem that makes Docker appealing. Its also the features that docker exposes and implements. These in essence expand on what is available by LXC alone, that coupled with an extensive API library has meant that the Docker deployment lifecycle can be fully automated. The containers can be passed certain information on startup, that can set run-time configuration. That means that you could, if done correctly, deploy the same Docker container in every environment, giving you <a href="http://blog.quantum-platforms.com/immutable-infrastucture">immutable-infrastructure</a>.

Docker's api library has another benefit; there is a huge number of 3rd party tools that makes use of these and extend its capability. Tools that give PaaS-like deployment (Dokku, Deis, Flynn), multi-node orchestration (maestro, salt, mesos, openstack nova), management dashboards (docker-ui, openstack horizon, shipyard), configuration management (chef, puppet), continuous integration (jenkins, strider, travis)
<blockquote>
<p class="crayon-line crayon-striped-line">So, it's true Docker is the answer! What you have heard is true! right?</p>
<p class="crayon-line crayon-striped-line">Well not exactly..</p>
</blockquote>
Its good, real good, and done correctly can vastly simplify your application lifecycle. There are however a few but's:
<ul>
 	<li>Not every application is suitable for running in a container. LXC container make use Linux facilities: chroot, cgroups, and namespaces and as such lightweight single process run best in a container.</li>
 	<li>As all the container use the same kernel, resource isolation is not as high as on a virtual machine. If one of your containers cause a kernel panic that will knock out your Docker host and all containers it is running.</li>
 	<li>Communication between containers running on different hosts is tricky. I say tricky as this has become easier as Docker has matured and so has the tooling to manage containers. It does still need some up-front thought and design.</li>
 	<li>You need to run Docker on Linux with a supported Kernel version (you can run it on windows too, but I don't have sufficient knowledge of this yet). So if you're organisation is still stuck in RHEL 5 (yes, there are still some), then forget it.</li>
</ul>
<div class="crayon-line crayon-striped-line">So to summarise (I have waffled on a bit),  Docker is a great tool, built on some solid proven concepts and technologies. It is not however the answer to everything. You must be prepared to adapt your ways of working, the mindset of your developers/operations teams, and your technology stack.</div>
<div class="crayon-line crayon-striped-line"></div>
<p class="crayon-line crayon-striped-line">So is it worth the hype and the <strong>$1bn</strong> valuation? I simply don't know..</p>

<blockquote>
<div class="crayon-line crayon-striped-line">But... Is it the answer?</div>
<div class="crayon-line crayon-striped-line">In a word - No.</div></blockquote>
<p class="crayon-line crayon-striped-line">Not by itself, Docker forms part of the answer, for reasons listed above; immutable infrastructure, infrastructure-as-code, versioning, and the portability. Docker is just a new technology, the reason for its recent success is that it encourages and enforces some of the best practices that have been around for ages.</p>
<p class="crayon-line crayon-striped-line">Yes, more and more companies are running Docker in production, but they are still quite scarce. It is has great potential for the right applications. Don't however try and shoehorn it in, if the shoe doesn't fit then it simply doesn't.</p>
<p class="crayon-line crayon-striped-line">Docker is a technology, and rarely is technology alone the answer. Technology is an enabler but the real challenges are processes and culture.</p>
<p class="crayon-line crayon-striped-line">So try it for yourself but be prepared to fail fast. You may find that Docker is simply not mature enough or doesn't yet fit in with your organisational culture or workflow. That doesn't mean it will never work, you may find a use case that is the perfect fit for docker, and that could be the catalyst to drive Docker and the principles it brings further into your organisation.</p>
<p class="crayon-line crayon-striped-line"></p>