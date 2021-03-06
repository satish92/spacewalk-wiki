
![Alt](images/26release.png?raw=True)
# __Spacewalk 2.6 Release Notes__



Hello everyone,

We are proudly announcing release of Spacewalk 2.6, a systems management solution.

The download locations are

  * http://yum.spacewalkproject.org/2.6/RHEL/6/
  * http://yum.spacewalkproject.org/2.6/RHEL/7/
  * http://yum.spacewalkproject.org/2.6/Fedora/23/
  * http://yum.spacewalkproject.org/2.6/Fedora/24/

with client repositories under

  * http://yum.spacewalkproject.org/2.6-client/RHEL/5/
  * http://yum.spacewalkproject.org/2.6-client/RHEL/6/
  * http://yum.spacewalkproject.org/2.6-client/RHEL/7/
  * http://yum.spacewalkproject.org/2.6-client/Fedora/23/
  * http://yum.spacewalkproject.org/2.6-client/Fedora/24/


SUSE Linux client packages can be found here
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.6/openSUSE_13.2/ 
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.6/openSUSE_Leap_42.1/ 
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.6/openSUSE_Leap_42.2/ 
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.6/openSUSE_Tumbleweed/ 
  * http://download.opensuse.org/repositories/systemsmanagement:/spacewalk:/2.6/SLE_12_SP2/ 

For fresh installations, please use steps from

  * https://fedorahosted.org/spacewalk/wiki/HowToInstall

If you plan to upgrade from older release, search no more -- the following page will guide you:

  * http://fedorahosted.org/spacewalk/wiki/HowToUpgrade
## Features & Enhancements in Spacewalk 2.6



  * Spacewalk now supported on Fedora 24
  * Spacewalk supports Fedora 24 clients
  * spacewalk-repo-sync improvements:
    * now it can sync channels with several repositories
    * it can update Kickstart Tree in a repository
    * add possibility to sync Debian/Ubuntu apt repositories
  * improved Python 2/3 compatibility for all tools  
  * New API calls:
    * system.listSuggestedReboot
    * actionchain.addErrataUpdate


The up-to-date API documentation can be found at http://www.spacewalkproject.org/documentation/api/2.6/
## Contributors



Our thanks go to the community members who contributed to this release:

 * Dario Leidi
 * Frantisek Kobzik
 * Jakub Jankowski
 * Johannes Renner
 * Marcelo Moreira de Mello
 * Mark Hlawatschek
 * Michael Calmer
 * Michael Mraka
 * Silvio Moioli

see [Contributor List](ContributorList) for all contributors list
## Some statistics



In Spacewalk 2.6, we've seen

    * 59 major bugs fixed
    * 496 changesets committed
    * 740 commits done

Github repo for commits since Spacewalk 2.5

    * [Spacewalk 2.5 to 2.6](https://github.com/spacewalkproject/spacewalk/graphs/contributors?from=2016-05-26&to=2016-11-18&type=c)
## User community, reporting issues



To reach the user community with questions and ideas, please use
mailing list spacewalk-list@redhat.com. On this list, you can of
course also discuss issues you might find when installing or using
Spacewalk, but please do not be surprised if we ask you to file a bug
at https://bugzilla.redhat.com/enter_bug.cgi?product=Spacewalk with more
details or full logs.

Thank you for using Spacewalk.