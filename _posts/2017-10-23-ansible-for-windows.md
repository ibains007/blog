---
ID: 391
post_title: Ansible for windows
author: Rich Hicklin
post_excerpt: ""
layout: post
permalink: >
  http://blog.quantum-platforms.com/ansible-for-windows
published: true
post_date: 2017-10-23 14:31:18
---
<em>How to install Ansible and configure it to manage windows host via WinRM</em><em> </em>
<h2><em>Overview </em></h2>
<em>So recently I was looking at the approach to managing windows servers that are in a domain using Ansible and leveraging windows remote manager. Initially the concept seemed a simple one, but this was a bit of a challenge for a chap with a minimal amount of Linux exposure, least of all on a fedora based distro. Challenge accepted :)</em>

<em>“The following guide below describes how I got this working and the steps involved”</em>

<em>In order to figure out what I needed to do I created a test lab, best to fail on throw away kit. With that in mind I used Oracle Virtual Box and built 3 windows servers, the break down was as follows</em>
<h1><em>Lab Setup</em></h1>
<em>Before we get going full we need to build some windows servers and a Linux box. This is a bit of a shopping list to enable you to follow the guides</em>
<h3>Servers (Virtual)</h3>
<em>Windows 2008 R2 x 3</em>

<em>Centos 7</em>
<h3>Applications</h3>
<ul>
 	<li><em>Software that is used in this walkthrough is:</em></li>
 	<li><em>Oracle Virtual Box</em></li>
 	<li><em>Windows 2008.iso</em></li>
 	<li><em>CentOS7.iso</em></li>
 	<li><em>Centos net-tools</em></li>
 	<li><em>Centos component for oracle VM epel-release-7-0.2.noarch.rpm</em></li>
 	<li><em>Ansible 2.x</em></li>
</ul>
<strong><em>Ansible modules:</em></strong>
<ul>
 	<li><em>sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python –y</em></li>
</ul>
<strong><em>Windows updates</em></strong>
<ul>
 	<li><em>PowerShell Windows6.1-KB2506143-x64.msu</em></li>
 	<li><em>.net framework version 4.5 KB2858728-x86-x64-</em></li>
</ul>
<h1>Server 1</h1>
<em>Role: Domain Controller</em>

<em>OS 2008 R2</em>

<em>Info: this server was built and promoted to the domain controller.</em>

<em>The old school method of opening a command prompt and typing dcpromo still works so that’s the method I used to install the directory services components.</em>

<em>The domain name used during promotion is not important so feel free to pick what you want. In this example I used TEST.INT</em>

<em>I used dsa.msc to create a domain user as follows</em>

<em>Domain user name: winrmuser</em>

<em>Password: Passw0rdPassw0rd</em>

<em>Group Membership: Domain admins (this is for testing only as this account is to be secured at a later stage)</em>
<h1><em>Server 2 </em></h1>
<em>Role: Member server</em>

<em>OS 2008 R2</em>

<em>Info: this server was built with a static IP assigned and then joined to the test domain TEST.INT</em>

<em>To take advantage of the Ansible WINRM PowerShell script that’s available, the installed version of PowerShell needs to be upgraded. By default when built, it’s version 2. </em>

<em>You can see this by opening the run box, typing PowerShell and selecting the PowerShell application that’s displayed.</em>

<img class="alignnone size-medium wp-image-401" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-1-3-236x300.png" alt="" width="236" height="300" />

<em>Once PowerShell console is open type host and simply press enter. You will see it is version 2.</em>

<img class="alignnone size-medium wp-image-402" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-2-1-300x96.png" alt="" width="300" height="96" />

<em>Upgrading the version of PowerShell requires patch .net framework version 4.5 KB2858728-x86-x64-ALLOS-ENU.exe and also Windows Update for PowerShell Windows6.1-KB2506143-x64.msu</em>

<img class="alignnone size-medium wp-image-403" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-3-1-300x29.png" alt="" width="300" height="29" />

<em>Both installs are a next &gt; Next &gt; Next affair. Then the statuary Microsoft reboot.</em>

<em>After the reboot re run the host test in PowerShell it should now read version 3</em>

<img class="alignnone size-medium wp-image-404" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-4-1-300x110.png" alt="" width="300" height="110" />

<em>We are going to use a PowerShell script that’s available at.</em>

<a href="https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"><em>https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1</em></a>

<strong>Copy the code….. From #Start to #End</strong>
<pre><em>#Start</em>

<em>#Requires -Version 3.0</em>



<em># Configure a Windows host for remote management with Ansible</em>

<em># -----------------------------------------------------------</em>

<em>#</em>

<em># This script checks the current WinRM (PS Remoting) configuration and makes</em>

<em># the necessary changes to allow Ansible to connect, authenticate and</em>

<em># execute PowerShell commands.</em>

<em>#</em>

<em># All events are logged to the Windows EventLog, useful for unattended runs.</em>

<em>#</em>

<em># Use option -Verbose in order to see the verbose output messages.</em>

<em>#</em>

<em># Use option -CertValidityDays to specify how long this certificate is valid</em>

<em># starting from today. So you would specify -CertValidityDays 3650 to get</em>

<em># a 10-year valid certificate.</em>

<em>#</em>

<em># Use option -ForceNewSSLCert if the system has been SysPreped and a new</em>

<em># SSL Certificate must be forced on the WinRM Listener when re-running this</em>

<em># script. This is necessary when a new SID and CN name is created.</em>

<em>#</em>

<em># Use option -SkipNetworkProfileCheck to skip the network profile check.</em>

<em># Without specifying this the script will only run if the device's interfaces</em>

<em># are in DOMAIN or PRIVATE zones.  Provide this switch if you want to enable</em>

<em># WinRM on a device with an interface in PUBLIC zone.</em>

<em>#</em>

<em># Use option -SubjectName to specify the CN name of the certificate. This</em>

<em># defaults to the system's hostname and generally should not be specified.</em>



<em># Written by Trond Hindenes &lt;trond@hindenes.com&gt;</em>

<em># Updated by Chris Church &lt;cchurch@ansible.com&gt;</em>

<em># Updated by Michael Crilly &lt;mike@autologic.cm&gt;</em>

<em># Updated by Anton Ouzounov &lt;Anton.Ouzounov@careerbuilder.com&gt;</em>

<em># Updated by Nicolas Simond &lt;contact@nicolas-simond.com&gt;</em>

<em># Updated by Dag Wieërs &lt;dag@wieers.com&gt;</em>

<em># Updated by Jordan Borean &lt;jborean93@gmail.com&gt;</em>

<em>#</em>

<em># Version 1.0 - 2014-07-06</em>

<em># Version 1.1 - 2014-11-11</em>

<em># Version 1.2 - 2015-05-15</em>

<em># Version 1.3 - 2016-04-04</em>

<em># Version 1.4 - 2017-01-05</em>

<em># Version 1.5 - 2017-02-09</em>

<em># Version 1.6 - 2017-04-18</em>



<em># Support -Verbose option</em>

<em>[CmdletBinding()]</em>



<em>Param (</em>

<em>    [string]$SubjectName = $env:COMPUTERNAME,</em>

<em>    [int]$CertValidityDays = 1095,</em>

<em>    [switch]$SkipNetworkProfileCheck,</em>

<em>    $CreateSelfSignedCert = $true,</em>

<em>    [switch]$ForceNewSSLCert,</em>

<em>    [switch]$EnableCredSSP</em>

<em>)</em>



<em>Function Write-Log</em>

<em>{</em>

<em>    $Message = $args[0]</em>

<em>    Write-EventLog -LogName Application -Source $EventSource -EntryType Information -EventId 1 -Message $Message</em>

<em>}</em>



<em>Function Write-VerboseLog</em>

<em>{</em>

<em>    $Message = $args[0]</em>

<em>    Write-Verbose $Message</em>

<em>    Write-Log $Message</em>

<em>}</em>



<em>Function Write-HostLog</em>

<em>{</em>

<em>    $Message = $args[0]</em>

<em>    Write-Host $Message</em>

<em>    Write-Log $Message</em>

<em>}</em>



<em>Function New-LegacySelfSignedCert</em>

<em>{</em>

<em>    Param (</em>

<em>        [string]$SubjectName,</em>

<em>        [int]$ValidDays = 1095</em>

<em>    )</em>



<em>    $name = New-Object -COM "X509Enrollment.CX500DistinguishedName.1"</em>

<em>    $name.Encode("CN=$SubjectName", 0)</em>



<em>    $key = New-Object -COM "X509Enrollment.CX509PrivateKey.1"</em>

<em>    $key.ProviderName = "Microsoft RSA SChannel Cryptographic Provider"</em>

<em>    $key.KeySpec = 1</em>

<em>    $key.Length = 4096</em>

<em>    $key.SecurityDescriptor = "D:PAI(A;;0xd01f01ff;;;SY)(A;;0xd01f01ff;;;BA)(A;;0x80120089;;;NS)"</em>

<em>    $key.MachineContext = 1</em>

<em>    $key.Create()</em>



<em>    $serverauthoid = New-Object -COM "X509Enrollment.CObjectId.1"</em>

<em>    $serverauthoid.InitializeFromValue("1.3.6.1.5.5.7.3.1")</em>

<em>    $ekuoids = New-Object -COM "X509Enrollment.CObjectIds.1"</em>

<em>    $ekuoids.Add($serverauthoid)</em>

<em>    $ekuext = New-Object -COM "X509Enrollment.CX509ExtensionEnhancedKeyUsage.1"</em>

<em>    $ekuext.InitializeEncode($ekuoids)</em>



<em>    $cert = New-Object -COM "X509Enrollment.CX509CertificateRequestCertificate.1"</em>

<em>    $cert.InitializeFromPrivateKey(2, $key, "")</em>

<em>    $cert.Subject = $name</em>

<em>    $cert.Issuer = $cert.Subject</em>

<em>    $cert.NotBefore = (Get-Date).AddDays(-1)</em>

<em>    $cert.NotAfter = $cert.NotBefore.AddDays($ValidDays)</em>

<em>    $cert.X509Extensions.Add($ekuext)</em>

<em>    $cert.Encode()</em>



<em>    $enrollment = New-Object -COM "X509Enrollment.CX509Enrollment.1"</em>

<em>    $enrollment.InitializeFromRequest($cert)</em>

<em>    $certdata = $enrollment.CreateRequest(0)</em>

<em>    $enrollment.InstallResponse(2, $certdata, 0, "")</em>



<em>    # extract/return the thumbprint from the generated cert</em>

<em>    $parsed_cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2</em>

<em>    $parsed_cert.Import([System.Text.Encoding]::UTF8.GetBytes($certdata))</em>



<em>    return $parsed_cert.Thumbprint</em>

<em>}</em>



<em># Setup error handling.</em>

<em>Trap</em>

<em>{</em>

<em>    $_</em>

<em>    Exit 1</em>

<em>}</em>

<em>$ErrorActionPreference = "Stop"</em>



<em># Get the ID and security principal of the current user account</em>

<em>$myWindowsID=[System.Security.Principal.WindowsIdentity]::GetCurrent()</em>

<em>$myWindowsPrincipal=new-object System.Security.Principal.WindowsPrincipal($myWindowsID)</em>



<em># Get the security principal for the Administrator role</em>

<em>$adminRole=[System.Security.Principal.WindowsBuiltInRole]::Administrator</em>



<em># Check to see if we are currently running "as Administrator"</em>

<em>if (-Not $myWindowsPrincipal.IsInRole($adminRole))</em>

<em>{</em>

<em>    Write-Host "ERROR: You need elevated Administrator privileges in order to run this script."</em>

<em>    Write-Host "       Start Windows PowerShell by using the Run as Administrator option."</em>

<em>    Exit 2</em>

<em>}</em>



<em>$EventSource = $MyInvocation.MyCommand.Name</em>

<em>If (-Not $EventSource)</em>

<em>{</em>

<em>    $EventSource = "Powershell CLI"</em>

<em>}</em>



<em>If ([System.Diagnostics.EventLog]::Exists('Application') -eq $False -or [System.Diagnostics.EventLog]::SourceExists($EventSource) -eq $False)</em>

<em>{</em>

<em>    New-EventLog -LogName Application -Source $EventSource</em>

<em>}</em>



<em># Detect PowerShell version.</em>

<em>If ($PSVersionTable.PSVersion.Major -lt 3)</em>

<em>{</em>

<em>    Write-Log "PowerShell version 3 or higher is required."</em>

<em>    Throw "PowerShell version 3 or higher is required."</em>

<em>}</em>



<em># Find and start the WinRM service.</em>

<em>Write-Verbose "Verifying WinRM service."</em>

<em>If (!(Get-Service "WinRM"))</em>

<em>{</em>

<em>    Write-Log "Unable to find the WinRM service."</em>

<em>    Throw "Unable to find the WinRM service."</em>

<em>}</em>

<em>ElseIf ((Get-Service "WinRM").Status -ne "Running")</em>

<em>{</em>

<em>    Write-Verbose "Setting WinRM service to start automatically on boot."</em>

<em>    Set-Service -Name "WinRM" -StartupType Automatic</em>

<em>    Write-Log "Set WinRM service to start automatically on boot."</em>

<em>    Write-Verbose "Starting WinRM service."</em>

<em>    Start-Service -Name "WinRM" -ErrorAction Stop</em>

<em>    Write-Log "Started WinRM service."</em>



<em>}</em>



<em># WinRM should be running; check that we have a PS session config.</em>

<em>If (!(Get-PSSessionConfiguration -Verbose:$false) -or (!(Get-ChildItem WSMan:\localhost\Listener)))</em>

<em>{</em>

<em>  If ($SkipNetworkProfileCheck) {</em>

<em>    Write-Verbose "Enabling PS Remoting without checking Network profile."</em>

<em>    Enable-PSRemoting -SkipNetworkProfileCheck -Force -ErrorAction Stop</em>

<em>    Write-Log "Enabled PS Remoting without checking Network profile."</em>

<em>  }</em>

<em>  Else {</em>

<em>    Write-Verbose "Enabling PS Remoting."</em>

<em>    Enable-PSRemoting -Force -ErrorAction Stop</em>

<em>    Write-Log "Enabled PS Remoting."</em>

<em>  }</em>

<em>}</em>

<em>Else</em>

<em>{</em>

<em>    Write-Verbose "PS Remoting is already enabled."</em>

<em>}</em>



<em># Make sure there is a SSL listener.</em>

<em>$listeners = Get-ChildItem WSMan:\localhost\Listener</em>

<em>If (!($listeners | Where {$_.Keys -like "TRANSPORT=HTTPS"}))</em>

<em>{</em>

<em>    # We cannot use New-SelfSignedCertificate on 2012R2 and earlier</em>

<em>    $thumbprint = New-LegacySelfSignedCert -SubjectName $SubjectName -ValidDays $CertValidityDays</em>

<em>    Write-HostLog "Self-signed SSL certificate generated; thumbprint: $thumbprint"</em>



<em>    # Create the hashtables of settings to be used.</em>

<em>    $valueset = @{</em>

<em>        Hostname = $SubjectName</em>

<em>        CertificateThumbprint = $thumbprint</em>

<em>    }</em>



<em>    $selectorset = @{</em>

<em>        Transport = "HTTPS"</em>

<em>        Address = "*"</em>

<em>    }</em>



<em>    Write-Verbose "Enabling SSL listener."</em>

<em>    New-WSManInstance -ResourceURI 'winrm/config/Listener' -SelectorSet $selectorset -ValueSet $valueset</em>

<em>    Write-Log "Enabled SSL listener."</em>

<em>}</em>

<em>Else</em>

<em>{</em>

<em>    Write-Verbose "SSL listener is already active."</em>



<em>    # Force a new SSL cert on Listener if the $ForceNewSSLCert</em>

<em>    If ($ForceNewSSLCert)</em>

<em>    {</em>



<em>        # We cannot use New-SelfSignedCertificate on 2012R2 and earlier</em>

<em>        $thumbprint = New-LegacySelfSignedCert -SubjectName $SubjectName -ValidDays $CertValidityDays</em>

<em>        Write-HostLog "Self-signed SSL certificate generated; thumbprint: $thumbprint"</em>



<em>        $valueset = @{</em>

<em>            CertificateThumbprint = $thumbprint</em>

<em>            Hostname = $SubjectName</em>

<em>        }</em>



<em>        # Delete the listener for SSL</em>

<em>        $selectorset = @{</em>

<em>            Address = "*"</em>

<em>            Transport = "HTTPS"</em>

<em>        }</em>

<em>        Remove-WSManInstance -ResourceURI 'winrm/config/Listener' -SelectorSet $selectorset</em>



<em>        # Add new Listener with new SSL cert</em>

<em>        New-WSManInstance -ResourceURI 'winrm/config/Listener' -SelectorSet $selectorset -ValueSet $valueset</em>

<em>    }</em>

<em>}</em>



<em># Check for basic authentication.</em>

<em>$basicAuthSetting = Get-ChildItem WSMan:\localhost\Service\Auth | Where {$_.Name -eq "Basic"}</em>

<em>If (($basicAuthSetting.Value) -eq $false)</em>

<em>{</em>

<em>    Write-Verbose "Enabling basic auth support."</em>

<em>    Set-Item -Path "WSMan:\localhost\Service\Auth\Basic" -Value $true</em>

<em>    Write-Log "Enabled basic auth support."</em>

<em>}</em>

<em>Else</em>

<em>{</em>

<em>    Write-Verbose "Basic auth is already enabled."</em>

<em>}</em>



<em># If EnableCredSSP if set to true</em>

<em>If ($EnableCredSSP)</em>

<em>{</em>

<em>    # Check for CredSSP authentication</em>

<em>    $credsspAuthSetting = Get-ChildItem WSMan:\localhost\Service\Auth | Where {$_.Name -eq "CredSSP"}</em>

<em>    If (($credsspAuthSetting.Value) -eq $false)</em>

<em>    {</em>

<em>        Write-Verbose "Enabling CredSSP auth support."</em>

<em>        Enable-WSManCredSSP -role server -Force</em>

<em>        Write-Log "Enabled CredSSP auth support."</em>

<em>    }</em>

<em>}</em>



<em># Configure firewall to allow WinRM HTTPS connections.</em>

<em>$fwtest1 = netsh advfirewall firewall show rule name="Allow WinRM HTTPS"</em>

<em>$fwtest2 = netsh advfirewall firewall show rule name="Allow WinRM HTTPS" profile=any</em>

<em>If ($fwtest1.count -lt 5)</em>

<em>{</em>

<em>    Write-Verbose "Adding firewall rule to allow WinRM HTTPS."</em>

<em>    netsh advfirewall firewall add rule profile=any name="Allow WinRM HTTPS" dir=in localport=5986 protocol=TCP action=allow</em>

<em>    Write-Log "Added firewall rule to allow WinRM HTTPS."</em>

<em>}</em>

<em>ElseIf (($fwtest1.count -ge 5) -and ($fwtest2.count -lt 5))</em>

<em>{</em>

<em>    Write-Verbose "Updating firewall rule to allow WinRM HTTPS for any profile."</em>

<em>    netsh advfirewall firewall set rule name="Allow WinRM HTTPS" new profile=any</em>

<em>    Write-Log "Updated firewall rule to allow WinRM HTTPS for any profile."</em>

<em>}</em>

<em>Else</em>

<em>{</em>

<em>    Write-Verbose "Firewall rule already exists to allow WinRM HTTPS."</em>

<em>}</em>



<em># Test a remoting connection to localhost, which should work.</em>

<em>$httpResult = Invoke-Command -ComputerName "localhost" -ScriptBlock {$env:COMPUTERNAME} -ErrorVariable httpError -ErrorAction SilentlyContinue</em>

<em>$httpsOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck</em>



<em>$httpsResult = New-PSSession -UseSSL -ComputerName "localhost" -SessionOption $httpsOptions -ErrorVariable httpsError -ErrorAction SilentlyContinue</em>



<em>If ($httpResult -and $httpsResult)</em>

<em>{</em>

<em>    Write-Verbose "HTTP: Enabled | HTTPS: Enabled"</em>

<em>}</em>

<em>ElseIf ($httpsResult -and !$httpResult)</em>

<em>{</em>

<em>    Write-Verbose "HTTP: Disabled | HTTPS: Enabled"</em>

<em>}</em>

<em>ElseIf ($httpResult -and !$httpsResult)</em>

<em>{</em>

<em>    Write-Verbose "HTTP: Enabled | HTTPS: Disabled"</em>

<em>}</em>

<em>Else</em>

<em>{</em>

<em>    Write-Log "Unable to establish an HTTP or HTTPS remoting session."</em>

<em>    Throw "Unable to establish an HTTP or HTTPS remoting session."</em>

<em>}</em>

<em>Write-VerboseLog "PS Remoting has been successfully configured for Ansible."</em>



<em>#End</em></pre>
<em>Open a notepad on the 2008 server and paste the content of your clip board. “This assumes that you enabled a bidirectional clip board in the oracle VM” if you have no paste option :)</em><em> of to the VM setting we go.</em>

<img class="alignnone size-medium wp-image-405" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/1-300x247.png" alt="" width="300" height="247" />

<em>Change the shared clipboard and drag and drop to Bidirectional</em>

<em>Create a new file on the Windows 2008 member servers desktop and call it WinRM.ps1, Paste the content of the clip board into the new file.</em>

<img class="alignnone size-medium wp-image-407" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/winrm-config-1-300x191.png" alt="" width="300" height="191" />

<em>Select File &gt; Save and change the save as type to all File Encoding ANSI and then select save. Select yes to over write the file if prompted.</em>

<img class="alignnone size-medium wp-image-408" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/winrm-config-2-300x148.png" alt="" width="300" height="148" />
<h3><em>Running the PowerShell script.</em></h3>
<em>As a default install of windows it’s highly likely that the execution policy will block the installation of the script. To fix this simply open PowerShell from run box and make sure you select Run as Administrator, by right clicking the PowerShell application icon</em>

<img class="alignnone size-medium wp-image-409" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/winrm-config-3-243x300.png" alt="" width="243" height="300" />

<em>Now type </em><strong>Set-ExecutionPolicy RemoteSigned</strong><em> select yes at the pop up message.</em>

<img class="alignnone size-medium wp-image-410" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-5-300x50.png" alt="" width="300" height="50" />

<em>To execute the WinRM code. Type cd “C:\User\Administartor\Desktop” all were doing is changing to the location that we created the Winrm.ps1 file so we can execute it. Make sure you’re in the correct location using LS of DIR in the command line</em>

<img class="alignnone size-medium wp-image-411" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/Powershell-image-6-300x92.png" alt="" width="300" height="92" />

<em>Type ./WinRM.ps1 in the command line and press enter. Allow the code to complete. </em>

<em>This script in general setups a WinRM listener on a https port and create a self-signed cert with a duration of 3 years to use for network SSL encapsulation.</em>

<em>To display the WinRM config type: </em><strong>winrm get winrm/config</strong>

<img class="alignnone size-medium wp-image-412" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/winrm-config-info-300x294.png" alt="" width="300" height="294" />

<em> </em>
<h1>Server 3</h1>
<em>The 3<sup>rd</sup> Server is configured exactly the same as the 2<sup>nd</sup> server just setup. (it’s used to prove the setup and apply test against server hardening configuration outside of the scope of this how to guide”)</em>

<em>This concludes the Windows parts for now :)</em><em> and I know there are so many ways with shortcuts and scripts that we could have used to make the task slicker, but as an overview it’s visual and should shed more light on what’s going on in the setup.</em>
<h1><em>Setting up Ansible.</em></h1>
<em>So here I moved out of my comfort zone, its goes without saying I had a lot of attempts and fails for the Ansible configuration and integration with windows. The following set of instruction allowed me to successfully connect to the windows servers in the end. So the Ansible server for the labs is</em>
<h1><em>Server 4 </em></h1>
<em>Role: Ansible server</em>

<em>OS Centos 7</em>

<em>I added 2 network interfaces. So I could use putty from a device that’s hosting the oracle VM and another Network card on the same local area network of the windows server. In my lab the networks were a Nat Network / host only interface</em>

<img class="alignnone size-medium wp-image-413" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/virtual-box-settings-1-300x119.png" alt="" width="300" height="119" />

<em>And the 2<sup>nd</sup> interface was host-only / Virtual Host-Only interface – this is so putty can be used to administer the server later, much more efficient than the console.</em>

<img class="alignnone size-medium wp-image-414" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/virtual-box-settings-2-300x117.png" alt="" width="300" height="117" />

<em>Server Notes.</em>

<em>When building a server on Oracle installing the Oracle drivers can be a pain to sort this out first run. Also the server is being configured as root not best practice but for a lab its fine for now. To get the centos server in a working state prior to installing Ansible complete the following tasks</em>

<em>Centos post install</em>
<ul>
 	<li><strong>yum update</strong></li>
 	<li><strong>yum install net-tools</strong></li>
 	<li><strong>yum install epel-release</strong></li>
</ul>
<em>I did a reboot as some windows habits are hard to break, I am sure it’s not needed :)</em>

<em>To install the oracle drivers I executed the following command from the console.</em>
<ul>
 	<li><strong>wget http://dl.fedoraproject.org/pub/epel/beta/7/x86_64/epel-release-7-0.2.noarch.rpm</strong></li>
 	<li><strong>yum install epel-release-7-0.2.noarch.rpm</strong></li>
 	<li><strong>yum install dkms cd /etc/sysconfig/network-scripts/</strong></li>
</ul>
<em>Note I had a few snags with the 2<sup>nd</sup> network interface not picking up an address. I found this was due to a config in its ifcfg-&lt;NIC&gt; settings. I resolved this using an editor “I used nano once I had run the yum install nano command” I am still new to the stack and have to admit I've managed to mangle too many file with VI.</em>

<em>The offending file is located in directory /etc/systemconfig/network-scripts/</em>

<img class="alignnone size-medium wp-image-415" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/ansible-setup-1-300x115.png" alt="" width="300" height="115" />

<em>Locate the file that’s named ifcfg and has the same network id as the network interface that’s not obtaining a DHCP address and open the editor. You will see the setting at the bottom of the file and it will be set as </em><strong>ONBOOT=”no”</strong>

<img class="alignnone size-medium wp-image-416" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/nic-1-300x206.png" alt="" width="300" height="206" />
<ul>
 	<li><em>Change this to </em><strong>ONBOOT=”Yes”</strong></li>
</ul>
<img class="alignnone size-medium wp-image-417" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/nic-2-300x186.png" alt="" width="300" height="186" />

<em>I used a reboot command again old school windows chap, but it did the trick and I managed to obtain a IP address bound to my local machine that’s hosting the oracle VM’s we can use putty now. So open a putty client for the rest of the install.</em>

<em>We want to apply a static IP to the windows LAN this is to make life simple in the LAB.</em>
<ul>
 	<li><em>Use ip add &lt;enter&gt; and make a note of the interfaces name that’s on the windows LAN host only network. In my lab that was </em></li>
 	<li><em>Open using Vi </em><strong>or nano execute /etc/systemconfig/network-scripts/ifcfg- enp0s3</strong></li>
 	<li><em>Change the value of:</em></li>
</ul>
<pre>BOOTPROTO="dhcp" to BOOTPROTO="static"</pre>
<ul>
 	<li><em>Add to the bottom of the config file</em></li>
</ul>
<pre>IPADDR=”10.0.2.10”

NETMASK=”255.255.255.0”

GATEWAY=”10.0.2.1”</pre>
<ul>
 	<li><em>Save and close the file.</em></li>
</ul>
<em>Working example</em>

<img class="alignnone size-medium wp-image-418" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/nic-3-280x300.png" alt="" width="280" height="300" />

<em>We need to make some change to the resolve.conf file now so we can add the Ansible server to the Domain stood up in the Windows server.</em>
<ul>
 	<li><em>“run VI or nano” </em><strong>nano /etc/resolve.conf</strong></li>
</ul>
<em>Amend the config file to reflect your LAB setup, the search is the NAME of your domain and the nameserver is the DNS server address by IP, the domain we created has DNS installed by default so we can use this servers IP</em>
<pre># Generated by NetworkManager

search TEST.INT

nameserver 10.0.2.100</pre>
<em>Example:</em>

<img class="alignnone size-medium wp-image-419" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/2-1-300x93.png" alt="" width="300" height="93" />

<em>Time to run a quick test to make sure everything working. Best laid plans tend to fail on sill things. Using a ping command we can ping the domain name.</em>
<ul>
 	<li><em>Run a reboot to ensure that the IP changes apply</em></li>
 	<li><em>Type </em><strong>ping TEST.INT</strong></li>
</ul>
<em>We should get a reply form the 10.0.2.100 address or the nameserver IP address we used in the resolve.conf file. If not.</em>

<em>Make sure the VM running the domain is up and its network card is connected virtually of course.</em>

<em>Disable the windows network firewall if its on for now, testing purpose only in a real setup we could apply a network rule to allow a ICMP response to and from the Ansible server. </em>

<em>Retest the ping command until you get a reply it’s important to make sure we can resolve the domain as we are going to joining the Ansible sever to the windows Domain.</em>
<h2><em>Setup the HOST name on the Ansible server</em></h2>
<em>Although the server will ultimately have a record in DNS, it is also necessary to set the host name to be the FQDN and ensure both the short name and FQDN are present in the local /etc/hosts file (against the server facing address.</em>

<em>The following command resets the hostname, using Ansible-Demo as an example</em>
<ul>
 	<li><strong>hostnamectl set-hostname &lt;hostname&gt;.&lt;domain&gt;</strong></li>
</ul>
<em>Example</em>

<img class="alignnone size-medium wp-image-420" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/3-1-300x38.png" alt="" width="300" height="38" />

<em>We need to add the fully qualified name of the domain controller to the etc/hosts file,</em>
<ul>
 	<li><em>using VI or nano execute </em><strong>nano /etc/hosts and add a line to the bottom</strong></li>
</ul>
<em>&lt;IP-OF-YOUR-DOMAIN-CONTOLER&gt; TAB &lt;FQDN&gt; TAB &lt;NETBIOS-NAME-DOAMIN-CONTOLER&gt;               </em>

<em>Eaxmple: </em>
<pre><em>10.0.2.100           win-e131f7pf8pu.TEST.INT           win-e131f7pf8pu</em></pre>
<em> <img class="alignnone size-medium wp-image-421" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/4-300x58.png" alt="" width="300" height="58" /></em>
<h2><em>Joining the realm</em></h2>
<em>Pre requisites </em>

<em>We need some packages so execute the following command</em>
<ul>
 	<li><strong>yum install sssd realmd oddjob oddjob-mkhomedir adcli samba-common samba-common-tools krb5-workstation openldap-clients policycoreutils-python -y</strong></li>
</ul>
<em>It should now be possible to run the realm join. This creates a computer object in AD, a DNS record for the host and configures sssd and Kerberos to allow GSSAPI authentication. In order to complete this step it is necessary to have access to a user with domain admins privileges.</em>

<em>Discover the realm</em>

<strong><em>realm discover &lt;domain&gt;</em></strong>

<em>Example</em>

<img class="alignnone size-medium wp-image-422" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/5-300x132.png" alt="" width="300" height="132" />

<em>Common problems with this not working are normal due to problems with the /etc/resolve.conf file. Go back and check it if this fails. Use a ping command again to confirm it.</em>

<em>Join the realm</em>
<ul>
 	<li><strong>realm join &lt;domain&gt; –U &lt;domain_admin_user&gt;</strong></li>
</ul>
<em>Example</em>

<img class="alignnone size-medium wp-image-423" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/6-300x51.png" alt="" width="300" height="51" />

<em>Run the “discover” again the output should have changed as per the truncated snippet below</em>

<img class="alignnone size-medium wp-image-424" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/7-300x186.png" alt="" width="300" height="186" />

<em> Note the following changes</em>
<pre>domain-name: &lt;domain&gt;

configured: kerberos-member

server-software: active-directory</pre>
<h2><em>Install Ansible</em></h2>
<em>Now we have the server in the Active directory domain we can install Ansible. Run the following command to do a minimal install of Ansible</em>
<ul>
 	<li><strong><em>yum install Ansible</em></strong></li>
</ul>
<em>To ensure its installed execute the command</em>
<ul>
 	<li><strong><em>ansible –version</em></strong></li>
</ul>
<em>Example</em>

<img class="alignnone size-medium wp-image-425" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/8-300x87.png" alt="" width="300" height="87" />

<em>To ensure we have all the required packages to connect to the windows servers we need to add the run the following commands</em>

<em>Refer to </em>

<em>http://docs.ansible.com/ansible/latest/intro_windows.html#installing-python-kerberosefer </em>
<ul>
 	<li><strong>yum -y install python-pip</strong></li>
 	<li><strong>yum -y install python-devel krb5-devel krb5-libs krb5-workstation</strong></li>
 	<li><strong>pip install pywinrm[kerberos]</strong></li>
 	<li><strong>pip install "pywinrm&gt;=0.2.2"</strong></li>
</ul>
<img class="alignnone size-medium wp-image-426" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/pip-install-300x32.png" alt="" width="300" height="32" />

<em>if a message presents itself at the end of the installation of pywinrm stating that there is a new version of pip, feel free to install it.</em>

<img class="alignnone size-medium wp-image-427" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/pip-install-2-300x100.png" alt="" width="300" height="100" />
<h2><em>Setup the KRB5.CONF file</em></h2>
<ul>
 	<li><em>Edit the /etc/krb5.conf </em></li>
</ul>
<pre># Configuration snippets may be placed in this directory as well

includedir /etc/krb5.conf.d/

includedir /var/lib/sss/pubconf/krb5.include.d/

[logging]

 default = FILE:/var/log/krb5libs.log

 kdc = FILE:/var/log/krb5kdc.log

 admin_server = FILE:/var/log/kadmind.log

[libdefaults]

 dns_lookup_realm = false

 ticket_lifetime = 24h

 renew_lifetime = 7d

 forwardable = true

 rdns = false

# default_realm = EXAMPLE.COM

 default_ccache_name = KEYRING:persistent:%{uid}

 default_realm = TEST.INT

[realms]

TEST.INT = {

kdc = WIN-E131F7PF8PU.TEST.INT

default_domain = TEST.INT

kpasswd_server = WIN-E131F7PF8PU.test.int

admin_server = WIN-E131F7PF8PU.test.int

 }

[domain_realm]

 test.int = TEST.INT

 .test.int = TEST.INT</pre>
<h2><em>Testing Kerberos</em></h2>
<em>To test the Kerberos setting use</em>

<strong>Kinit &lt;Username&gt;@&lt;Domain&gt; </strong>

<em>Example</em>

<em>Type </em><strong>kinit winrmuser@TEST.INT</strong>

<em>Enter the Password for winrmuser@TEST.INT:</em>

<em>This will ask for users password, if you receive a credentials don’t match it’s normally due to the upper case requirement for the domain name in the realm setting!</em>

<em>Example </em>

<strong><em>kinit winrmuser@test.int</em></strong>

<em>Password for </em><a href="mailto:winrmuser@dlg-test.int"><em>winrmuser@test.int</em></a><em>:</em>
<h2><em>Testing the session</em></h2>
<em>To check for a session use</em>

<em>Klist </em>

<em>Example:</em>

<img class="alignnone size-medium wp-image-428" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/8-1-300x87.png" alt="" width="300" height="87" />
<h2><em>Setup the Ansible Hosts file</em></h2>
<em>We can now add the 2 windows 2008 servers we created at the start of the lab to the Ansible hosts, this is done by using Vi or NANO</em>
<ul>
 	<li><strong>nano /etc/Ansible/hosts</strong></li>
</ul>
<em>At the bottom of the file add the following</em>
<pre>[windows]

TEST-2K8.TEST.INT

TEST-2K8-NO-GPO.TEST.INT</pre>
<em>Example</em>

<img class="alignnone size-medium wp-image-429" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/9-300x147.png" alt="" width="300" height="147" />
<h2><em>Setup the windows.yml for winrm connectivity</em></h2>
<em> </em>

<em>Now we setup the connection file for the windows servers. This is done by creating a file named windows.yml and adding connection configuration setting.</em>

<em>Create a directory </em>

<strong>Mkdir /etc/ansible/group_vars</strong>

<em>cd into the new directory<strong> /etc/ansible/group_vars</strong></em>

<em>Create a file named <strong>windows.yml</strong></em>
<pre>#Windows.yml

# it is suggested that these be encrypted with ansible-vault:

# ansible-vault edit group_vars/windows.yml

ansible_user: winrmuser@TEST.INT

ansible_password: Passw0rdPassw0rd

ansible_port: 5986

ansible_winrm_transport: kerberos

ansible_connection: winrm

# The following is necessary for Python 2.7.9+ (or any older Python that has backported SSLContext, eg, Python 2.7.5 on RHEL7) when us$

ansible_winrm_server_cert_validation: ignore</pre>
<h1><em>Testing</em></h1>
<h2><em>Testing WinRM via a ping pong test</em></h2>
<em>Test the winrm setup using the ws-ping </em>

<strong>ansible windows -m win_ping –vvv</strong>

<em>The output will be:</em>

<em>Using /etc/ansible/ansible.cfg as config file</em>

META: ran handlers

Using module file /usr/lib/python2.7/site-packages/ansible/modules/windows/win_ping.ps1

&lt;TEST-2K8.TEST.INT&gt; ESTABLISH WINRM CONNECTION FOR USER: winrmuser@TEST.INT on PORT 5986 TO TEST-2K8.TEST.INT

Using module file /usr/lib/python2.7/site-packages/ansible/modules/windows/win_ping.ps1
<pre>&lt;TEST-2K8-NO-GPO.TEST.INT&gt; ESTABLISH WINRM CONNECTION FOR USER: winrmuser@TEST.INT on PORT 5986 TO TEST-2K8-NO-GPO.TEST.INT

EXEC (via pipeline wrapper)

EXEC (via pipeline wrapper)

TEST-2K8.TEST.INT | SUCCESS =&gt; {

    "changed": false,

    "ping": "pong"

<em>}</em>

<em>TEST-2K8-NO-GPO.TEST.INT | SUCCESS =&gt; {</em>

<em>    "changed": false,</em>

<em>    "ping": "pong"</em>

<em>}</em>

<em>META: ran handlers</em>

<em>META: ran handlers</em></pre>
<em> </em><em>If this is all working we will get PONG back in the ping, the –vvv are verbose levels.</em>

<em>Example</em>

<img class="alignnone size-medium wp-image-430" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/10-300x102.png" alt="" width="300" height="102" />
<h2><em>Gathering FACTS</em></h2>
<em>To test and prove WinRM remote execution use the command</em>

<strong>ansible all -m setup</strong>

<em>or </em>

<strong>ansible windows -m setup</strong>

<em>Example</em>

<img class="alignnone size-medium wp-image-431" src="http://blog.quantum-platforms.com/wp-content/uploads/2017/10/11-300x229.png" alt="" width="300" height="229" />
<h1>Thoughts</h1>
The configuration of the Centos server was by far the most challenging aspect of this work for me, and to be fair setting up SSH on each server may be the fastest way to get an Ansible connection working. I feel the use of WinRM and Kerberos to be a viable approach. The self-signed cert created via the PowerShell scripts can be replaced via the auto enrollment of cert from a window domain and it’s PKI, out of scope for this walk through.

It’s an exciting start for windows as it’s the beginning of automation in a mixed world.

We could extend the lab if required and build and deploy configuration management for both open systems and windows. Linked to a Git style repo we have some of the tooling in place to allow DevOps style approaches in a windows world.

It safe to say I could of done a big chunk of what Ansible can do with PowerShell and Desired state configuration management but it’s got its own draw backs in that it’s not as Linux friendly.

Achieving this Labs build and guide was not a simple google job, as I struggled to find any full “How to" guides  for this solution, it felt like most of the content is written from a Linux perspective. Therefore I was driven to complete this information as it may save you some time in the future.
<h1>Acknowledgements</h1>
Linux assistance and Realm join script were added by Iwan Bedford, a big thank you also for the assistance in the Centos server command line :)