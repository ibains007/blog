---
ID: 281
post_title: 'Let&#8217;s play with Ansible &#038; Cisco IOS'
author: Jon Treharne
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/lets-play-with-ansible-cisco-ios
published: true
post_date: 2017-02-10 16:23:59
---
First of all, here's a description of what we're solving with this walkthrough / work guide.
<blockquote>Manage Cisco IOS devices using Ansible, without SSH Key authentication.</blockquote>
<h2>What is Ansible?</h2>
<img class="wp-image-293 alignleft" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/02/cisco_logo-300x225.jpg" alt="cisco_logo" width="184" height="138" /><img class="wp-image-294 alignright" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/02/Ansible-Official-Logo-Black-300x300.png" alt="Ansible-Official-Logo-Black" width="119" height="119" />

&nbsp;

Ansible is an IT automation tool. It can configure systems, deploy software, and orchestrate more advanced IT tasks such as continuous deployments or zero downtime rolling updates. If you are not aware of what Ansible is or want more in depth detail, please refer to the documentation: <a href="http://docs.ansible.com/" target="_blank">Ansible Documentation</a>.

We'll be using Ansible 1.7.2 and cisco IOS 12.2. (There are newer versions of Ansible available, but not required for this walk through).

Why would you not use SSH Key authentication?!! - The simple answer is that the Cisco IOS versions we're using above do not support this, of course you would use ssh keys where available.
<h2>Recipe List...</h2>
What do we need to get this started? here's a list of what I'm using:
<ol>
 	<li>A CentOS VM. - Any unix/linux based system will suffice.</li>
 	<li>Access to the internet, to download software.</li>
 	<li>Super user or sudo access to install software to the VM.</li>
 	<li>A Cisco switch running IOS v12.2. - Any version of Cisco IOS should suffice.</li>
 	<li>Connectivity between the switch and the VM.</li>
</ol>
<h2>Getting Started...</h2>
To start with, we need to install Ansible and also sshpass to be able to authenticate with username and password. So execute the following: (<em>providing you have yum installed, you can use other ways such as pip, apt-get, brew etc.</em>)
<pre style="text-align: center;">yum install ansible
yum install sshpass</pre>
<p style="text-align: left;">Once this is complete you should now have Ansible installed under <em>/etc/ansible/</em>. if you <em>ls -ltr</em> you'll see that there is an <em>ansible.cfg</em> and a hosts file. These two files contain the default configuration for an Ansible install. In this tutorial we'll leave these files alone.</p>
<p style="text-align: left;">Lets check that Ansible is available to use and we can get it to attempt to run... by executing:</p>

<pre style="text-align: center;">ansible</pre>
<p style="text-align: left;">This should result in Ansible listing its usage and options, if this happened, then brill! If not, you may not have installed correctly, please check your installation of Ansible.</p>

<h2 style="text-align: left;">Configuring...</h2>
<p style="text-align: left;">Now we are ready to start configuring our settings, authentications and hosts. So go to a development directory and let's get started, I'm going to use:</p>

<pre style="text-align: center;">mkdir -p ~/poc/ansible/group_vars
 mkdir -p ~/poc/ansible/host_vars
 mkdir -p ~/poc/ansible/playbooks
 cd ~/poc/ansible
 touch hosts ansible.cfg</pre>
<p style="text-align: left;">This should now give us a directory tree with two files and three directories under<em> ~/poc/ansible</em>.</p>
<p style="text-align: left;">├── ansible.cfg -file
├── group_vars - directory
├── hosts -file
├── host_vars - directory
└── playbooks - directory</p>
<p style="text-align: left;">Let's now start adding some content to our files. We'll start by updating some of the settings in our <em>~/poc/ansible/ansible.cfg</em>, anything that we add in here will override the default settings in  <em>/etc/ansible/.</em></p>

<pre style="text-align: center;">vi ~/poc/ansible/ansible.cfg</pre>
<p style="text-align: left;">Insert the following:</p>

<pre style="text-align: left;">[defaults]
 transport=smart
 hostfile=./hosts
 host_key_checking=False
 timeout=5</pre>
<ol>
 	<li>Default SSH library (transport) can also be set to paramiko which utilises the python libraries rather than openssh.</li>
 	<li>We'll tell Ansible to refer to our newly created host file.</li>
 	<li>We are not checking ssh keys so set this checking to false.</li>
 	<li>As we are just testing, SSH timeout can be decreased to 5 seconds.</li>
</ol>
<p style="text-align: left;">We'll now update the hosts file by adding a device that we want to configure. We shall add a device and also put it into a group. A group is a number of devices that you logically group together to run the same tasks on. This could be geographically, by type, by usage etc. We'll group based on type.</p>

<pre style="text-align: center;">vi ~/poc/ansible/hosts</pre>
<p style="text-align: left;">Insert the following: (edit the group name or device name where applicable)</p>

<pre style="text-align: left;">[cisco-devices]
 switch1</pre>
<p style="text-align: left;">We now need to tell Ansible the IP to use to connect to switch1, in the cisco-devices group, we'll add this into the <em>~/poc/ansible/host_vars</em> directory.</p>

<pre style="text-align: center;">vi ~/poc/ansible/host_vars/switch1</pre>
<p style="text-align: left;">Insert the following: (edit the IP where applicable)</p>

<pre style="text-align: left;">---
 ansible_ssh_host: 10.101.88.4</pre>
<p style="text-align: left;">We can now add group settings, for instance, we need to add the user and password details that Ansible will use to connect with. We'll add this into <em>~/poc/ansible/group_vars/cisco-devices </em>file<em>.</em></p>

<pre style="text-align: center;">vi ~/poc/ansible/group_vars/cisco-devices</pre>
<p style="text-align: left;">Insert the following: (edit user and pass as applicable)</p>

<pre style="text-align: left;">---
 ansible_ssh_user: admin
 ansible_ssh_pass: password</pre>
<p style="text-align: left;">Alarm Bells! plain text password...! We'll leave it like this for now but in BAU and certainly production you'll want to hash these values out (<a href="https://github.com/ansible/ansible-examples/blob/master/language_features/user_commands.yml" target="_blank">User module</a>) and whilst using GIT for example, this file would be added to .gitignore. These choices are based on whether you have a central management server or you allow engineers to make changes in a distributed fashion.</p>

<h2 style="text-align: left;"> Initial Testing...</h2>
Now we have our first configurations complete lets try a simple command line test to check connectivity. It would be best practice to attempt an ssh connection from your machine that's running Ansible to check that connectivity exist before trying via Ansible, so from the command line:
<pre style="text-align: center;">ssh admin@10.101.88.4</pre>
<p style="text-align: left;">Enter your password on the prompt and ensure you can log in.</p>
<p style="text-align: left;">Once this is complete, log back out of the switch and let's try logging in with Ansible. We're not utilising playbooks yet, we'll run this command on the command line alone. let's try by running:</p>

<pre style="text-align: center;">ansible cisco-devices -m raw -a "show running-config"</pre>
<p style="text-align: left;">This should give you back a success prompt (<em>switch1 | success | rc=0 &gt;&gt;</em>) followed by the output of the command. This now proves we can connect via Ansible and run remote commands. If you got a failed to connect then check your config is correct.</p>

<h2 style="text-align: left;">Using a playbook...</h2>
Now that we have proved we can run remote commands to a group of devices (albeit only 1 currently) by using the module (-m) raw with the argument (-a) show running-config, we're ready to now utilise the playbook functionality.

Each playbook<b> </b>contains one or more plays, which map hosts to a certain function. Ansible does this through something called tasks, which are basically module calls. So a playbook for example could be a bootstrap that has a number of tasks that will be performed each time it is run.

Let's create our first playbook, playbooks can be reused and shared by the Ansible community at <a href="http://galaxy.ansible.com" target="_blank">Galaxy</a>. We'll start with a simple YAML playbook to test for now:
<pre style="text-align: center;">vi ~/poc/ansible/playbooks/simple.yaml</pre>
<p style="text-align: left;">Insert the following:</p>

<pre style="text-align: left;">---
 - hosts: cisco-devices
 gather_facts: false
 tasks:
 - name: Get Simple Details
 raw: show running-config</pre>
<p style="text-align: left;">We now run the playbook by executing:</p>

<pre style="text-align: center;">ansible-playbook ~/poc/ansible/playbooks/simple.yaml -v</pre>
<p style="text-align: left;">Ansible should have printed out the play, the task, the output and the summary. You have now successfully run an Ansible playbook using the raw module against a IOS device using username and password authentication.</p>

<h2 style="text-align: left;">Going forward...</h2>
You can now explore the included <a href="http://docs.ansible.com/ansible/modules_by_category.html">Ansible Modules</a> and make use of some templated playbooks already available in <a href="http://galaxy.ansible.com" target="_blank">Galaxy</a>. Have fun...!