XXXXX-XXXX
  * Added installation script for MPF: `mpf_installation`
  * Skip mustache expand if not a valid destination
  * Can now set classes in the bundle json data, ala def.json, eg
```
        "dhclient": {
            "classes": { 
                "RESOLV_CONF": "r24n2"
            }
        },
```
Will set the class `DHCLIENT_RESOLV_CONF` on host `r24n2`
 * Use standard cfengine `remote_dcp` bundle instead of `sara_hash_no_perms_cp`

 * ssh changes:
    * added a new json attribute for ssh bundle. `copy_files`
```
"ssh": {
    "copy_files": {
        "ssh_host_dsa_key": { "source": "cf_bundles_dir/ssh/doornode", "mode": "0600", "owner": "root", "group": "root", "restart": "yes" },
        "ssh_host_dsa_key.pub": { "source": "cf_bundles_dir/ssh/doornode", "mode": "0644", "owner": "root", "group": "root", "restart": "yes" }
    }
},
```
    * Some ssh options are deprecated.  If you want to include this options in `sshd_config` you must set the class `SSH_USE_DEPRICATED_OPTIONS",
      it is default enabled for debian_7 and centos. 
```
vars:
    "ssh" data => parsejson( '{ "classes": { "USE_DEPRICATED_OPTIONS" : "any" }  }' );

or

classes:
    "SSH_USE_DEPRICATED_OPTIONS" expression => "any";
```

    * ssh options added: 
```
    "X11Forwarding": "yes",
    "X11UseLocalhost": "yes
```

 * Added functionallity to enable `virtual_alias_maps` entry in postfi main.cf. The following example will copy the mustache template
file from `templates/postfix/ldap_aliases_map.mustache` and expand it with the specified inline json data:
```
"classes" : {
    "VIRTUAL_MAPS": [ "mta.example.com" ],:
},
"virtual_alias_maps": {
    "ldap_aliases_map.mustache" : {
        "delimiter": ":",
        "dest": "/etc/postfix/virtual_alias_maps.cf",
        "protocol": "ldap",
        "data": {
            "bind" : true,
            "bind_options" : {
                "dn" : "<your_dn>",
                "pw" : "<your_bind_password>"
            },
            "port": "636",
            "query_filter" : "(uid=%s)",
            "result_attribute" : "mail",
            "search_base" : "ou=Users,dc=example,dc=com",
            "server": "ldaps://ldap.example.com"
        }
    }
}

18-Oct-2017
  * Added dhclient.cf service,  for now only disable resolv.conf generation.
  * Added check\_space.cf service, monitor filesystem and you can execute an command or bundle if promise has failed
  * postfix template can now handle: virtual\_mailbox\_limit  option (Lucas Slim, SURFsara)
  * library improvements, sara\_data\_autorun is inline with sara\_mustache\_autorun,. Simplified a lot of coce.
  * Added services.cf to library as alternative for autorun. All methods are protected by a class sercice name. (Dennis Stam, SURFsara)
 
