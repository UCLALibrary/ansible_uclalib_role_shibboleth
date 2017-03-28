# UCLALib Ansible Role: Shibboleth Install
Ansible role to install the Shibboleth SP on Library RHEL Systems

# Dependencies/Considerations

### UCLA Campus IAM Support
Setting-up a new Shibboleth SP involves working with the UCLA IAM team to register your application.

To provision a working Shibboleth SP application at UCLA, reference the following documentation:
- [IAM UCLA Shibboleth Set-up Docs](https://spaces.ais.ucla.edu/x/3A03AQ)
- [UCLA Library Specific Shibboleth Implementation](https://docs.library.ucla.edu/x/XIDSB)

### Attribute Map
Shibboleth requires a properly configured `attribute-map.xml` file. This role installs the default attribute-map file provided by UCLA IAM. As a part of the initial installation/configuration of Shibboleth, you will need to manually edit this file to include the attributes you requested, and that were approved/released, for your application. Common attributes released for UCLA applications can be found here: [List of Most Common Data Attributes](https://spaces.ais.ucla.edu/x/EoFSAw).

### Attribute Policy
Shibboleth requires an `attribute-policy.xml` file. This role installs the default attribute-policy file provided by UCLA IAM. Under most circumstances, the default should be sufficient and no manual editing is necessary. However contact the UCLA IAM team for clarification.

### Apache HTTP Config
You must have a working Apache HTTPD environment on the server. This role installs a shibboleth apache config file to `/etc/httpd/conf.d/shib.conf`.

Since Shibboleth works with Apache HTTPD, your virtual host should contain a `Location` stanza with the appropriate Shibboleth configuration parameters. For example:
```
<Location /path >
  AuthType Shibboleth
  ShibRequireSession On
  ShibUseHeaders On
  require shibboleth
</Location>
```

If your SP is using an ApplicationOverride, meaning your `shibboleth2.xml` file contains:
```
<ApplicationOverride id="app-name" entityID="https://entityidname.library.ucla.edu/shibboleth-sp"/>
```

Then your `Location` stanza would like this:
```
<Location /path >
  AuthType Shibboleth
  ShibRequireSession On
  ShibRequestSetting applicationId app-name
  ShibUseHeaders On
  require shibboleth
</Location>
```

# Role variables
* URL to the Shibboleth Yum Repository configuration file
```
shibboleth_yumrepo_url: http://download.opensuse.org/repositories/security:/shibboleth/RHEL_6/security:shibboleth.repo
```
* Shibboleth Entity ID
```
shib_entity_id: http://website.library.ucla.edu/shibboleth-sp
```
* Shibboleth Home URL
```
shib_home_url: http://website.library.ucla.edu
```

# Example Playbook

In your play you will want to set your own values for `shib_entity_id` and `shib_home_url`.

```
---

- name: uclalib_shibinstall.yml
  become: yes
  become_method: sudo
  hosts: test
  vars:
    shib_entity_id: http://librarysite.library.ucla.edu/shibboleth-sp
    shib_home_url: http://librarysite.library.ucla.edu

  roles:
    - { role: uclalib_role_shibboleth }
```
