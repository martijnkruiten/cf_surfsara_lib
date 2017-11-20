XXXXX-XXXX
  * Skip mustache expand if not a valid destination
  * Can now set classe in the bundle json data, ala def.json, egL
{{{
        "dhclient": {
            "classes": { 
                "RESOLV_CONF": "r24n2"
            }
        },
}}}
Will set the class `DHCLIENT_RESOLV_CONF` on host `r24n2`
 * Use standard cfengine `remote_dcp` bundle instead of `sara_hash_no_perms_cp`
 * added a new json attribute for ssh bundle. `copy_files`
{{{
"ssh": {
    "copy_files": {
        "ssh_host_dsa_key": { "source": "cf_bundles_dir/ssh/doornode", "mode": "0600", "owner": "root", "group": "root", "restart": "yes" },
        "ssh_host_dsa_key.pub": { "source": "cf_bundles_dir/ssh/doornode", "mode": "0644", "owner": "root", "group": "root", "restart": "yes" }
    }
},
}}}

18-Oct-2017
  * Added dhclient.cf service,  for now only disable resolv.conf generation.
  * Added check\_space.cf service, monitor filesystem and you can execute an command or bundle if promise has failed
  * postfix template can now handle: virtual\_mailbox\_limit  option (Lucas Slim, SURFsara)
  * library improvements, sara\_data\_autorun is inline with sara\_mustache\_autorun,. Simplified a lot of coce.
  * Added services.cf to library as alternative for autorun. All methods are protected by a class sercice name. (Dennis Stam, SURFsara)
 
