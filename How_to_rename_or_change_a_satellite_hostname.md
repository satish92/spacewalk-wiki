
# **DEPRECATED, NO LONGER USED**

# How To Rename A Satellite



'''This page is outdated, new way to rename satellite hostname is on
https://fedorahosted.org/spacewalk/wiki/SpacewalkHostnameRename'''
## I want to change the hostname and/or IP address of my Satellite - how do I do this?



*READ EVERYTHING BEFORE doing anything.*

*NEVER use UPPERCASE* letters in the Satellite hostname, things just don't work!

There are various aspects of RHN Satellite that use the hostname of the Satellite server. Depending on your usage of RHN Satellite will determine what of the following steps you would need to perform. This document should be considered as a guideline of what could/should need changing when changing the hostname of a RHN Satellite. It is not recommended to do this on a production system unless you absolutely have to.

 1. Ensure you have a recent backup of Satellite
 2. Recommendation is to stop Satellite services while making the change, though not strictly needed. 

    # /usr/sbin/service rhn-satellite stop
    # /sbin/service rhn-database start
 3. Edit the /etc/hosts file for new hostname
 4. Edit the /etc/sysconfig/network file for new value of HOSTNAME=
   * Edit the /etc/sysconfig/network-scripts/ifcfg-eth0 or other network configuration files for change of IP information. 

Reboot at this stage for the hostname change to take effect at the OS level and then continue to reconfigure the Satellite itself. Again recommendation is to stop the Satellite services while doing this.

 5. Edit the /etc/rhn/rhn.conf file using configuration deployment system
   * Change static value:

    # /usr/bin/rhn-config-satellite.pl --target=/etc/sysconfig/rhn-satellite-prep/satellite-local-rules.conf \
    --option=jabberDOThostname=my-new-satellite-hostname.example.org
   * Re-deploy all configs, needed for jabberd c2s.xml, sm.xml and rhn.conf files:

    # /usr/bin/satcon-deploy-tree.pl --source=/etc/sysconfig/rhn-satellite-prep/etc --dest=/etc \
    --conf=/etc/sysconfig/rhn-satellite-prep/satellite-local-rules.conf
   * The up2date client and other client side tools that use SSL to communicate with a RHN Satellite perform zero hostname validation, they only use the CA Cert (RHN-ORG-TRUSTED-SSL-CERT) during the SSL handshake to Apaches SSL to initiate secure communication. As such, you do not *need* to re-generate SSL Certificates, though it is recommended, at least for the Apache SSL certs, so that the hostname within the certificate matches that which people use when going to the WebUI and avoiding a message saying that the hostname you entered in the browser does not match that which is on the SSL Certificate. The only exception to this rule is the usage of the RHN Push Technology where the client side osad application does indeed have a very strong dependency on the hostname within the Apache SSL certificate that it is using matching exactly the hostname of the system it is connecting to (using TLS). So if your environment is using RHN Push then the following section (section 6) will need to be done. 
 6. Generate new SSL certs and deploy for new hostname
   * Apache SSL Certs:

    # rhn-ssl-tool --gen-server
   * Install certs:
     * list:

    # rpm -qa | grep rhn-org
     * delete old apache ssl certs:

    # rpm -e rhn-org-httpd-ssl-key-pair-<old-server-name>-1.0-1
     * install new apache ssl certs:

    # rpm -Uvh /root/ssl-build/<new-server-name>/rhn-org-httpd-ssl-key-pair-<new-server-name>-1.0-1.noarch.rpm

 7. Reconfigure up2date client systems with new hostname. 

 8. Re-generate new bootstrap client-config-overrides.txt file, used by the bootstrap.sh script, with new hostname:
   * Using the rhn-bootstrap tool, example usage:

    # rhn-bootstrap --activation-keys=key1,key2 --allow-remote-commands --allow-config-actions -v
   * This will write both new bootstrap.sh and client-config-overrides.txt files within /var/www/html/pub/bootstrap/. You can either restore the previous bootstrap.sh script, renamed to bootstrap.sh.1, or use the new script by editing it to remove the "exit 1" line. 
   * Alternatively you can just directly edit the client-config-overrides.txt file and search and replace any instance of the old hostname with the new. 
 9. Monitoring is very specific to the IP address and hostname being used. 
   * If the IP address changed then you need to update the two entry's in the database to show this:

    # sqlplus spacewalk/spacewalk@xe
    SQL> update rhn_sat_node set ip = 'xxx.xxx.xxx.xxx' where ip = 'yyy.yyy.yyy.yyy';
    SQL> update rhn_sat_cluster set vip = 'xxx.xxx.xxx.xxx' where vip = 'yyy.yyy.yyy.yyy';
    SQL> commit;
    SQL> quit
     * Where xxx is the new IP address and yyy is the old IP address

   * If the hostname changed, then you need to start apache and browse to the Monitoring config and change hostname value to new hostname of Satellite. This will restart monitoring services. You should see within a couple minutes the /etc/NOCpulse.ini file change all references from old hostname to new. 
     * Login -> Monitoring -> General Config -> set "RHN Satellite hostname (FQDN)" -> Click button "Update Config"
     * 
    # grep my-new-satellite-hostname.example.org /etc/NOCPulse.ini
     * It is also recommended to perform a push of all scout configurations. <br>
Login -> Monitoring -> Scout Config Push -> Select all -> Click button "Push Scout Configs"
   * Update Monitoring SSH Keys - this is done if you have any probes which need to connect to the remote client side rhnmd service to gather probe metrics. 
     * Generate new keys:

    # mkdir /home/nocpulse/.ssh/old
    # mv /home/nocpulse/.ssh/nocpulse-identity* /home/nocpulse/.ssh/old/
    # /usr/bin/ssh-keygen -q -t dsa -N '' -f /home/nocpulse/.ssh/nocpulse-identity
    # chown -R nocpulse.nocpulse /home/nocpulse
     * Insert new public key into database:
     * Note the new and old key:

    # cat /home/nocpulse/.ssh/nocpulse-identity.pub
    # cat /home/nocpulse/.ssh/old/nocpulse-identity.pub
     * Login to the database and display current (old) key information, make a note of the recid value in the output:

    # sqlplus spacewalk/spacewalk@xe
    SQL> select RECID, PUBLIC_KEY from rhn_sat_cluster;
     * Replace the old key with the new:

    SQL> update rhn_sat_cluster set public_key = 'ssh-dss [...SNIP LONG STRING..]= root@my-new-satellite-hostname.example.org' where recid = '1';
    SQL> commit;
    SQL> quit
     * The new ssh key will have to be deployed into the /opt/nocpulse/.ssh/authorized_keys file on any Monitoring client systems that have the rhnmd monitoring client daemon deployed for remote connections. A restart of the rhnmd is then also recommended. 
 10. Update the entry for hostname within the macro config system:

    # sqlplus spacewalk/spacewalk@xe
    SQL> select name, definition from rhn_config_macro where name = 'RHN_SAT_HOSTNAME';
    SQL> update rhn_config_macro set definition = 'my-new-satellite-hostname.example.org' where name = 'RHN_SAT_HOSTNAME';
    SQL> commit;
    SQL> quit
 11. restart all Satellite services (look for any errors)

    # /usr/sbin/service rhn-satellite restart

 12. Go to 'Satellite Tools' -> 'String Manager' in the UI and ensure that all strings are updated to reflect the new hostname.
 
 13. In /etc/cobbler/settings change "next_server:", "redhat_management_server:", and "server:" to point to your new FQDN.

 14. Test, test and test some more.

*Done.*

----

    > Any kickstarts will continue to point to the old satellite server. You
    > can fix this from the UI by changing distribution tree. It will be
    > correct after an update. It would be good to update this in the DB.
    >
    > Matt
    >   
    Did this bother someone? I assume you are talking about the data found under Advanced Options. This entry does nothing, and gets dynamically replaced.
    
    But, if you did want to change it, something like:
    
    SQL> update rhnkickstartcommand set arguments = '--url http://192.168.36.123/kickstart/dist/ks-rhel-i386-as-3/' where arguments = '--url http://test.redhat.com/kickstart/dist/ks-rhel-i386-as-3/';
    SQL> commit;
    
    where test.redhat.com is the old hostname and 192.168.36.123 is the new. 