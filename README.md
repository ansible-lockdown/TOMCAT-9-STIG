Tomcat 9 STIG
=========

This role will apply the STIG security standard to your Tomcat 9 instance. 

Requirements
------------

Tomcat - 9
RHEL - 7 or 8
CentOS - 7 or 8
Ubuntu - 16.04, 18.04, or 20.04

Tomcat installed and running (or able to be in a running state). Required packages that are not included in the role are listed below
  - gcc - This should be installed by default but confirm it is installed and up to date. 
  - Latest APR version - The APR software is located on the Apache website, http://apache.spd.co.il//apr/. Find the latest version (apr-1.7.0.tar.gz for example)
  - Latest APR-Util - The APR-Util software is located on the Apache website, http://apache.spd.co.il//apr/. Find the latest version (apr-util-1.6.1.tar.gz for example)
  - OpenSSL - The FIPS capable OpenSSL package needs to be installed.
  - Latest JDK - This should be in included with your Tomcat package in $CATALINA_HOME/bin folder in tomcat-native.tar.gz. Run tar that file and manually ./configure, make, and make install. The ./configure needs a number of options set, it is advised to research before running. 

Role Variables
--------------

All variables are defined in the defaults/main.yml file. The top portion of the file controls main role related variables. These include toggles to enable/disable entire categories or individual tasks. Other variables are the non-privileged UID start point, automate package upgrades, etc. Other variables are for specific controls and are designed to take local system/site data. The goal with this setup is the end user will not have to modify the task itself to take on specific site settings. 


| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `tcat_cat1_patch` | `true` | Correct CAT I findings        |
| `tcat_cat2_patch` | `true`  | Correct CAT II findings       |
| `tcat_cat3_patch` | `true`  | Correct CAT III findings      |
| `tcat_as_######` | [see defaults/main.yml](./defaults/main.yml)  | Individual variables to enable/disable each STIG ID. |
| `tcat_skip_for_travis` | `false` | Skip travis related items. |
| `tcat_system_is_chroot` | `false` | tweak role to run in a chroot, such as in kickstart %post script. |
| `tcat_automate_install` | `true` | This toggle will install the needed packages for your web server. |
| `tcat_automate_package_upgrades` | `true` | There are tasks that ask if you to be on the latest version. This toggle will upgrade to the latest packages. |
| `tcat_disruption_high` | true | Toggle to run/skip disruptive tasks . |
| `tcat_non_privileged_uid` | 1000 | The value where non-privileged UID's start. |
| `catalina_home_dir` | var | The home folder for the tomcat install, value found by task in prelim.yml. |
| `tcat_default_storepass` | password1 | Default keystore password. Should be moved to vaul. |
| `tcat_jndi_realm.connection_url` | ldaps://localhost:686 | LDAP server address. |
| `tcat_jndi_realm.user_pattern` | uid={0},ou=people,dc=myunit,dc=mil | JNDI Realm user pattern. |
| `tcat_jndi_realm.role_base` | ou=groups,dc=myunit,dc=mil | JNDI Realm role base. |
| `tcat_jndi_realm.role_name` | cn | JNDI Realm role name. |
| `tcat_jndi_realm.role_search` | (uniqueMember={0}) | JNDI Realm role search. |
| `tcat_access_log_valve.prefix` | localhost_access_log | AccessLogValve prefix. |
| `tcat_access_log_valve.suffix` | .txt | AccessLogVavle suffix. |
| `tcat_access_log_valve.patther` | h %l %u %t &quot;%r&quot; %s %b" | AccessLogValve pattern. |
| `tcat_routable_ip_proxy` | Toggle if your proxy is using a routable IP address or an RFC 1918 Class B address space. |
| `tcat_remoteipvalve_proxy_addresses` | 172.16.0.10|172.16.0.11 | The internal proxy address if you are using the routable IP or RFC 1918 address space. |
| `tcat_manager_host_manager_removed` | false | Disables config injection that is not needed if manager and host-manager applications have been removed. |
| `tcat_jmx_management` | false | JMX management in use. |
| `tcat_jmxremote_host` | 192.168.0.150 | JMX Management IP address. |
| `tcat_remote_cidr_valve` | false | RemoteAddrValve in use. |
| `tcat_remoteaddrevalve_value` | 127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1 | RemoteAddrValve values. |
| `tcat_connection_proxy_address` | 127.0.0.1 | List of IP addresses that are used in connectors that are behind proxies. |
| `tcat_lockoutrealm_failurecount` | 5 | The number of failed attempts before lockout. |
| `tcat_lockoutrealm_lockouttime` | 600 | The duration of the lockout. |
| `tcat_clustering_enabled` | true | Set teh simpleTcpCluster in server.xml. |
| `tcat_big_access_log_valve` | list | List of AccessLogValve items for nested loop. |
| `tcat_max_active_sessions` | 10 | The number of simultaneous active sessions allowed. |
| `tcat_privileged_context_need` | false | Toggle the need for the privileged attribute in context.xml. |
| `tcat_listen_address` | 192.168.0.145 | The address the connector interface will listen on. |

Dependencies
------------


A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Role Structure
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

- defaults
    - main.yml - This file contains all of the variables used within the role. The variables are set as dummy/default values and can be changed directly in main.yml or via the extra vars section in Tower. 

- files
    - This folder is not currently used, but is for files that would be copied to hosts via a task

- handlers
    - main.yml - This folder is for handlers, which are tasks that run after the role. Currently we have one handler that will restart Tomcat after the role has been run and the handler was notified by a task. 

- meta
    - This folder is not used, but is for defining metadata for a role

- tasks
    - main.yml - This is the file that determines which categories to run based on the tcat_catX_patch variable.
    - prelim.yml - This is the file that runs any preliminary tasks needed.
    - fix-cat1.yml - This contains all of the tasks needed to perform Category 1 controls
    - fix-cat2.yml - This contains all of the tasks needed to perform Category 2 controls
    - fix-cat3.yml - This contains all of the tasks needed to perform Category 3 controls

- templates
    - 401_template.jsp.j2 - This is a template file for the 401 error messages. This needs to be customized to your local site
    - 403_template.jsp.j2 - This is a template file for the 403 error messages. This needs to be customized to your local site
    - 404_template.jsp.j2 - This is a template file for the 404 error messages. This needs to be customized to your locatl site

- tests
    - This folder is not used

- vars
    - This folder is not used, but would be used for extra variables needed for the role. We use defaults/main.yml for variables in our role since that has the lowest precedence and will be easily overwritten if needed (i.e. Tower). 

- site.yml - This is the main .yml file for the role and tells Ansible to play the role within its own directory. 
