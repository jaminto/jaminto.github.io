---
layout: post
published: false
category: Development
---

Steps to set up MSMQ on AWS

Enable port 1801 on send and receive side security groups

Install msmq
 
Add second drive for MSMQ storage, configure MSMQ to use

you will need to manually create these folders on disk, and MSMQ will warn you about setting permissions properly, but the default should be fine.
Remove message storage limits


Make QMId unique per EC2 instance


Workgroup mode, though, is less drastic:

  1. Stop the MSMQ Service 
  2. Delete the HKLM\Software\Microsoft\MSMQ\Parameters\MachineCache\QMId value completely
  3. 

  4. Add a SysPrep DWORD (Under HKLM\Software\Microsoft\MSMQ\Parameters) and set it to 1
  5. 

  6. DO NOT Start the MSMQ Service

from: http://blogs.msdn.com/b/johnbreakwell/archive/2007/02/06/msmq-prefers-to-be-unique.aspx

create new local administrator account - to ensure that password ec2 stuff doesn’t screw you

Ensure computer names will be unique - Check set computer name


shutdown with sys prep - you must choose “Random” Password, if you don’t even the current password you have on THIS machine will not work:


wait for instance to enter “stopped” state in AWS console
  

NOW TAKE AMI - do the next steps through EB configuration

enable http compression through cloud front
edit existing <serverRuntime/> node in applicationHost.config to:
<serverRuntime enabled="true" frequentHitThreshold="1" frequentHitTimePeriod="00:00:20" />

added to httpcompression node:
 noCompressionForHttp10="false" noCompressionForProxies="false" 

then take AMI

Messages will not be processed if the host name does match the destination.  IE. if you send to handlers.com, msmq expects the destination to be the local computer name i.e. IP-133423.  need to modify the registry.


  1. Click Start, click Run, type regedit, and then click OK.
  2. Locate and then click the following key in the registry:

HKEY_LOCAL_MACHINE\Software\Microsoft\MSMQ\Parameters
  3. On the Edit menu, point to New, and then click DWORD Value.
  4. Type IgnoreOSNameValidation, and then press ENTER.
  5. On the Edit menu, click Modify.
  6. Type 1, and then click OK.