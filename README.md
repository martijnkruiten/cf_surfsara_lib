# SURFsara CFEngine library for mustache/json templates 

At SURFsara we have developed a general library to generate files from templates. In our setup we can easily
specify the default values and override them in other json file(s) or via def.cf/json.

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

### MPF installation

1. Copy the contents of masterfiles into your masterfiles or equivalent repository
1. Enable autorun, if you have not do so already, by adding this class to your ```def.json``` file.
```
{
   "classes" :
   {
    "services_autorun" : "any"
   }
}
```

### Own framework

1. cp masterfiles/lib/surfsara <masterfiles>/lib/surfsara
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


## cf-agent command line options

The SURFsara CFEngine library also checks for some classes:
 * The copy of the json/mustache file(s) can be skipped by `-DMUSTACHE_SKIP_COPY`. So you can change the 
   files localy for testing.
 * To debug the mustache setup: `-DDEBUG_MUSTACHE` (all service bundles)
 * To debug mustache for a service bundle, eg *-DDEBUG_ntp*

