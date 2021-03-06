# Audit Reviewing in Spacewalk

## Introduction: What? Why?




This capability was added to Spacewalk to make the lives of Information Assurance teams easier on Linux.  See [http://roysjosh.blogspot.com/2012/07/basic-audit-re-viewing-in-spacewalk.html](http://roysjosh.blogspot.com/2012/07/basic-audit-re-viewing-in-spacewalk.html) for a short description and some screenshots.
## Basic setup



  * Install Spacewalk 0.6 or above.
  * In /etc/rhn/rhn.conf set following and then restart tomcat:

    web.audit.logdir = /var/satellite/systemlogs
  * Download aup.c [aup.c](https://github.com/spacewalkproject/spacewalk/blob/master/contrib/aup.c)
  * Install audit-libs and audit-libs-devel
  * Compile aup: `gcc -O2 -s -o /path/to/put/aup aup.c -lauparse`
  * `cd /var/satellite/systemlogs/ ; mkdir host1{,/audit} host2{,/audit} localhost{,/audit}`
  * `ausearch -r -ts $START -te $END | /path/to/aup > localhost/audit/audit-$T1-$T2.parsed`
    * $T1 and $T2 are unix timestamps describing interval of audits in file.
    * Filename "audit-$T1-$T2.parsed" is mandatory.
    * `export START="07/26/2009 00:00:00"`
    * `export END="08/01/2009 23:59:59"`
    * `export T1=`date -d "$START" +%s``
    * `export T2=`date -d "$END" +%s``
  * Or, put something like the following in a cronjob (and use pubkey ssh authentication):

    ausearch -r -ts `date +"%D %T" -d "$(date -d yesterday +"%D")"` \
    -te `date +"%D %T" -d "$(date -d now +"%D") - 1 seconds"` | /path/to/aup | \
    ssh spacewalk 'cat > /var/satellite/systemlogs/$OURHOSTNAME/audit/audit-`date +"%s" -d "$(date -d yesterday +"%D")"`-`date +"%s" -d "$(date -d now +"%D") - 1 seconds"`.parsed'
  * Or, use rsync to sync the entire /var/log over to /var/satellite/systemlogs/$OURHOSTNAME/
  * Make sure that `/var/satellite/systemlogs/*/audit/*` are readable by `tomcat` user all the way up from `/`, e.g.:

    namei -m /var/satellite/systemlogs/spacewalk/audit
