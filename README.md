# SURFsara CFEngine library for mustache/json templates 

At SURFsara we have developed a general library to generate files from templates. In our setup we can easily
specify the default values and override them in other json file(s) or via def.cf/json. The goal is to set
up an global  repository for mustache templates.

For all senarios the mustache/json file(s) will be copied to the local node directory:
 * The json and template file(s) are copied from the policy hub: `templates/$(bundle_name)`
 * The copies are placed in: `$(def.node_template_dir)/$(bundle_name)`
 * The following json must always be present and will always be copied: *default.json*
 * Extra json file(s) can be specified in *def.cf/json*: `$(bundle_name)_json_files` 
 * Scripts can generate json file(s) on a host/node. The json file must be copied into: 
    * `$(def.node_template_dir)/$(bundle_name)`
    * The generated file(s) are specified in def.cf/json: `$(bundle_name)_local_generated_json_files`
 * You can override values via *def.json*, Note: This one always wins.

Both senarios will be described in the subsection below. For both senarios you can specifiy multiple 
json files. The files will be merged and the last one wins if the same variable name is used,eg:  
 * a.json defines: `a : 1`
 * b.json defines: `a : 2`

If the order is `{ "b.json", "a.json" }` the value of *a* would be *1*

## Installation 

For now some copy actions are required. I will make an autotools setup. there are two options
 * Include it in the Master Policy Framework (MPF)
 * Include it in your own framework


### def.node\_template\_dir
 
The  `def.node_template_dir` variable is set in `lib/surfsara/def.cf`, but can also be set
set in `def.json`. The *def.json* wins, eg:
```
vars:
{
   "node_template_dir" : "/etc/node_status/templates"
}
```

### CF-serverd shortcut configuration for cfengine version less then 3.10.1

For older versions you have to manually add the `shorcut templates` to `controls/cf_serverd.cf`
```
      "$(sys.workdir)/templates"
      handle => "server_access_grant_access_templates",
      shortcut => "templates",
      comment => "Grant access to templates directory",
      admit => { @(def.acl) };
```

### MPF installation

1. Login on your policy server.
1. Copy the contents of masterfiles into your masterfiles or equivalent repository
1. Copy the `examples/templates` directory to `$(sys.workdir)/templates`: `cp -a examples/templates $(sys.workdir)/templates`
1. Enable autorun, if you have not done it, by adding this class to your ```def.json``` file
```
{
   "classes" :
   {
    "services_autorun" : "any"
   }
}
```

### Own framework

1. Login on your policy server.
1. cp -a masterfiles/lib/surfsara `<masterfiles>/lib/surfsara`
1. cp -a examples/templates $(sys.workdir)/templates
1. include `/lib/surfsara/stdlib.cf` in your inputs
```
body common control
{
    inputs => {
        ...
        "lib/surfsara/stdlib.cf",
        ...
    };
}
```
See above to add `templates shortcut` to cf-serverd.

## Usage

There are several template setups for different services included with inline documentation. These setups are
used in prodduction at SURFsara.
 1. examples/services/autorun/check_space.cf
 1. examples/services/autorun/dhclient.cf
 1. examples/services/autorun/ntp.cf
 1. examples/services/autorun/postfix.cf
 1. examples/services/autorun/resolv.cf
 1. examples/services/autorun/tcpwrappers.cf
 1. examples/services/autorun/sara_user_consume_resources.cf
 1. examples/services/autorun/singularity.cf
 1. examples/services/autorun/ssh.cf
 1. examples/services/autorun/tripwire.cf
 1. examples/services/autorun/yum.cf

To enable the template on your system:
 * MPF: copy a setup to the `masterfiles/services/autorun` directory
 * Own Framework:
   * copy a setup to  your masterfiles directory
   * add the files to your `inputs` statement
   * Activate the bundle
     * Via the meta tags:
        1. autorun
        1. `template_<bundle_name>`, eg: bundle\_ntp
     * usebundle:
        1. ntp_autorun()
        1. tcpwrappers_autorun()

### def.json

In this file you can override settings for the templates. When the json data is merged. This one wins, eg:
```
"vars": {
    "ntp" : {
        "server": [ "<your_ip_server1>", "<your_ip_server2>" ]
    }
}
```

You can also specify a json setup file:
```
"vars": {
    "tcpwrapper_json_files": [ "allow_ssh.json", "allow_http.json" ]
}
```

### lib/surfsara/def.cf


You can also override settings in this file, eg:
 * One variable:
```
vars:
    "ntp" data => parsejson( '{ "server" : [ "<your_ip_server1>" ] }' );
```
 * json file:
  ```
vars:
    "tcpwrapper_json_files" slist =>  { "allow_ssh.json", "allow_http.json" };
    "tripwire_json_files" slist =>  { "systemd.json" };
 ```


IF you definied your own `def.cf`and do want to use the one include in this framework you can set the following class:
 * `SURFSARA_SKIP_DEF_CF_INCLUDE` 


## cf-agent command line options

The SURFsara CFEngine library also checks for some classes:
 * To test local mustache/json changes, the copy of the json/mustache file(s) from the policy server can be skipped by:
  * `-DMUSTACHE_SKIP_COPY`:  Skip copying of the mustache files
  * `-DJSON_SKIP_COPY`: Skip copying of the json files
 * To debug the mustache setup: `-DDEBUG_MUSTACHE` (all service bundles)
 * To debug mustache for a service bundle, eg `-DDEBUG_ntp`

