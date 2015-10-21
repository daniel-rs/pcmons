pcmons
=========

Considering the lack of generic and open-source solutions for management and monitoring 
of private clouds, pcmons (Private Cloud MONitoring System) was developed. It is intended 
to be an extensible and modular monitoring system for private clouds. pcmons acts 
principally retrieving, gathering and preparing relevant information for monitoring data 
visualization and is specially on virtual machines.

Authors:

* Shirlei Chaves (shirlei@inf.ufsc.br)
* Rafael Uriarte (rafael.uriarte@inf.ufsc.br)
* Pedro Vitti (pedrovitti@gmail.com.br)

Site: https://github.com/pedrovitti/pcmons


Prerequisites
--------


PCMONS is compatible with Eucalyptus and Opennebula (IaaS platform) and Nagios (used to visualize monitoring data). 
However, its development considers easily integration with other toolkit or cloud frameworks trough the development 
of extensions.

**Prerequisites:**

    * Eucalyptus >= 1.6.1 or OpenNebula >= 3.0
    * Nagios
    * Python == 2.6.x (greater than 2.6.x may work, less than probably will not; neither is tested)
    * mysql
    * mysql-python
    * python-boto

You can use package manager to install these packages.

**Fedora:**

    $ yum install python mysql-python python-boto mysql-server

**Ubuntu/Debian:**

    $ sudo apt-get install python python-mysqld python-boto mysql-server
    

To install Eucalyptus or OpenNebula and Nagios you can follow the steps at the official documentation according to your GNU/Linux distribution.

**Eucalyptus** - [http://open.eucalyptus.com/wiki/EucalyptusInstallation_v2.0](http://open.eucalyptus.com/wiki/EucalyptusInstallation_v2.0 "http://open.eucalyptus.com/wiki/EucalyptusInstallation_v2.0") <br>
**Nagios** 	   - [http://nagios.sourceforge.net/docs/3_0/quickstart.html](http://nagios.sourceforge.net/docs/3_0/quickstart.html "http://nagios.sourceforge.net/docs/3_0/quickstart.html") <br>
**OpenNebula** - [http://opennebula.org/documentation:rel4.0](http://opennebula.org/documentation:rel4.0 "http://opennebula.org/documentation:rel4.0") <br>

Installation
--------


1. Configure an user in the MySQL database and in the cluster configuration ($PCMONS/running_vms/cluster/cluster_config.py). By default the user is 'manager' and the password is 'cloudmanager'.
 
    You can execute the command below at a mysql shell to do that:

        $ CREATE USER 'manager'@'localhost' IDENTIFIED BY 'cloudmanager';

2. Create the database and MySQL tables 

    You can run the vmmonitor_vm.sql script through a tool like phpMyAdmin (Just open a query window and copy and paste the content of the text file in there). phpMyAdmin and possibly other frontends will also allow to upload a *.sql file through the web browser. 
    
    In phpMyAdmin click on the SQL tab. If you don't have any such front end installed, you can also use the mysql shell.
    
    Execute this command and you'll be fine: 

        $ mysql <my_db_name> -u<user_name> -p<password> vmm_monitor_vm.sql

3. If you are using Eucalyptus, Change add_key.pl file by the one in the third-parties directory. Usually it's locate in /usr/share/eucalyptus folder.

4. Copy all files in the node folder ($PCMONS/running_vms/node/) to the node hosts and run it (python VM_Monitoring_Node_Plugin.py).

5. Run the other files in the cluster/cloud controller host (where Nagios is installed).


Information
--------


More information about monitoring of private clouds and pcmons architecture:
 * http://www.tede.ufsc.br/teses/PGCC0911-D.pdf (Portuguese)
 * https://projetos.inf.ufsc.br/arquivos_projetos/projeto_1285/Principal.pdf (Portuguese)
 * http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6094017&sortType%3Dasc_p_Sequence%26filter%3DAND%28p_IS_Number%3A6093994%29 (English)

Security Monitoring
--------

 pcmons was extended to monitor security metrics (SSH connections, availability, backup, encryption, ile system integrity, malware, password expiration, and vulnerability analysis) by Daniel Ricardo dos Santos (@daniel-rs). 
 
 The metrics and architectural extensions are described in the paper:
 * P.A.F. Vitti, D.R. dos Santos, C.B. Westphall, C.M. Westphall, K.M.M. Vieira, "[https://daniel-rs.github.io/files/publications/securware2014_paper.pdf](Current Issues in Cloud Computing Security and Management)" in SECURWARE 2014: The Eighth International Conference on Emerging Security Information, Systems and Technologies
 
 Each metric depends on an external security tool, so the services should be configured and run accordingly:
 * Backup - depends on rsync (e.g., rsync -avz user@domain:/home/user /home/local_user/ --log-file=/var/log/rsync.log)
 * Encryption - depends on cryptsetup (e.g., sudo apt-get install cryptsetup)
 * Integrity - depends on tripwire (e.g., tripwire --check > /var/log/tripwire.log) 
 * Malware - depends on clamav (e.g., clamscan / -l /var/log/clamav/clamav.log)
 * Vulnerability - depends on tiger (e.g., tiger -l /var/log/tiger)