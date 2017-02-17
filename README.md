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
 * Incldue it in your own framework


### def.node\_template\_dir
 
The  `def.node_template_dir` variable is set in `lib/surfsara/def.cf`, but can also be set
set in `def.json`. The *def.json* wins, eg:
```
vars:
{
   "node_template_dir" : "/etc/node_status/templates"
}
```

### CF-serverd shortcut configuration

If pull request [864](https://github.com/cfengine/masterfiles/pull/864) is applied you do not have to
change anything else you have to add the `shorcut templates` to `controls/cf\_serverd.cf`
```
      "$(sys.workdir)/templates"
      handle => "server_access_grant_access_templates",
      shortcut => "templates",
      comment => "Grant access to templates directory",
      admit => { @(def.acl) };
```

### Template examples

Copy the `examaples/templates` directory to your policy hub:
 * `cp -a examples/templates $(sys.workdir)/templates`

### MPF installation

1. Login on your policy server.
1. Copy the contents of masterfiles into your masterfiles or equivalent repository
1. Copy the `examples/templates' directory to `$(sys.workdir)/templates`: `cp -a exammples/templates $(sys.workdir)/templates`
1. Enable autorun, if you have not do so already, by adding this class to your ```def.json``` file
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
1. cp -a exammples/templates $(sys.workdir)/templates`
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


## cf-agent command line options

The SURFsara CFEngine library also checks for some classes:
 * The copy of the json/mustache file(s) can be skipped by `-DMUSTACHE\_SKIP\_COPY`. So you can change the 
   files localy for testing.
 * To debug the mustache setup: `-DDEBUG\_MUSTACHE` (all service bundles)
 * To debug mustache for a service bundle, eg *-DDEBUG_ntp*

