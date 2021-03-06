# __Spacewalk 1.6 Release Notes__



Hello world, 

we are happy to announce the release 1.6 of Spacewalk,
platform for management of Linux systems.

The download locations are

  * http://spacewalk.redhat.com/yum/1.6/RHEL/5/$basearch/ 
  * http://spacewalk.redhat.com/yum/1.6/RHEL/6/$basearch/ 
  * http://spacewalk.redhat.com/yum/1.6/Fedora/15/x86_64/ 
  * http://spacewalk.redhat.com/yum/1.6/Fedora/16/x86_64/ 

with client repositories under 

  * http://spacewalk.redhat.com/yum/1.6-client
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.6/openSUSE_11.4/
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/1.6/openSUSE_Factory/
  * http://miroslav.suchy.cz/spacewalk/debian

If you want to do fresh installation, use steps from

  * https://fedorahosted.org/spacewalk/wiki/HowToInstall 

If you plan to upgrade from older release, the following page
can be useful:

  * http://fedorahosted.org/spacewalk/wiki/HowToUpgrade 
## Features & Enhancements in Spacewalk 1.6



* IPv6 support: management and provisioning capabilities
* Support for Fedora 16 (server and client)
* [Kickstarting via Spacewalk Proxy with a CNAME](http://www.youtube.com/watch?v=3LPHYORuBBc)
* spacewalk-repo-sync supports --include and --exclude options
* New API calls:
   * channel.software.getRepoSyncCronExpression
   * channel.software.listChannelRepos
   * channel.software.setDetails
   * channel.software.syncRepo
   * configchannel.deleteFileRevisions
   * configchannel.getFileRevision
   * configchannel.getFileRevisions
   * kickstart.disableProfile
   * kickstart.isProfileDisabled
   * kickstart.keys.update
   * system.custominfo.updateKey
   * system.deleteGuestProfiles
   * system.deleteTagFromSnapshot
   * system.getScriptActionDetails
   * system.provisioning.snapshot.addTagToSnapshot
   * system.tagLatestSnapshot
* OSAD improvements
* Security fixes for following CVEs:
   * CVE-2011-1594
   * CVE-2011-2919
   * CVE-2011-2920
   * CVE-2011-2927
   * CVE-2011-3344
* rhn-virtualization supports RHEV 3 hosts
* spacewalk-reports supports --where-<column-id> option to filter records
* spacecmd enhancements and bugfixes
* Preliminary support for cobbler 2.2
## Contributors



Our thanks go to the community members who contributed to this release: 

 * Andy Speagle
 * Aron Parsons
 * Christian Berendt
 * Colin Coe
 * David Nutter
 * Ionuț Arțăriși
 * Johannes Renner
 * Jonathan Hoser
 * Joshua Roys
 * Luca Villa
 * Marcelo Moreira de Mello
 * Martin Osvald
 * Michael Calmer
 * Pierre Casenove
 * Satoru SATOH
 * Steve Hardy
 * Sven Mueller
 * Tasos Papaioannou
 * Uwe Gansert

https://fedorahosted.org/spacewalk/wiki/ContributorList 
## Some statistics



In Spacewalk 1.6, we've seen

    * 177  bugs solved 
    * 1025 changesets committed 
    * 1515 commits done 


Thank you for using Spacewalk! Keep the patches flowing!