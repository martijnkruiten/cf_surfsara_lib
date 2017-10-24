XXXXX-XXXX
  * Skip mustache expnad if not a valid destination
  * Can now set classe in the bundle json data, ala def.json, egL
{{{
        "dhclient": {
            "classes": { 
                "RESOLV_CONF": "r24n2"
            }
        },
}}}
Will set the class `DHCLIENT_RESOLV_CONF` on host `r24n2`


18-Oct-2017
  * Added dhclient.cf service,  for now only disable resolv.conf generation.
  * Added check\_space.cf service, monitor filesystem and you can execute an command or bundle if promise has failed
  * postfix template can now handle: virtual\_mailbox\_limit  option (Lucas Slim, SURFsara)
  * library improvements, sara\_data\_autorun is inline with sara\_mustache\_autorun,. Simplified a lot of coce.
  * Added services.cf to library as alternative for autorun. All methods are protected by a class sercice name. (Dennis Stam, SURFsara)
 
