---
layout: default
title: "Deprecated Settings"
---

[manifest_setting]: /references/3.7.latest/configuration.html#manifest
[modulepath_setting]: /references/3.7.latest/configuration.html#modulepath
[config_version]: /references/3.7.latest/configuration.html#configversion
[puppet.conf]: ./config_file_main.html
[environment.conf]: ./config_file_environment.html
[cli_settings]: ./config_about_settings.html#settings-can-be-set-on-the-command-line
[config file environments]: ./environments_classic.html
[directory environments]: ./environments.html
[puppetdb]: /puppetdb/latest
[inventory service]: /guides/inventory_service.html

The following Puppet settings are deprecated and will be removed in Puppet 4.0. Many of them are related to other deprecated features.


Config File Environment Settings
-----

* [`manifest`](/references/3.7.latest/configuration.html#manifest)
* [`modulepath`](/references/3.7.latest/configuration.html#modulepath)
* [`config_version`](/references/3.7.latest/configuration.html#configversion)

### Now

You can set global values for the [`manifest`][manifest_setting], [`modulepath`][modulepath_setting], and [`config_version`][config_version] settings in [puppet.conf][].

### In Puppet 4.0

The `manifest`, `modulepath`, and `config_version` settings are not allowed in [puppet.conf][]. They can be configured per-environment in [environment.conf][]. The `manifest` and `modulepath` settings can also be [specified on the command line.][cli_settings]

### Detecting and Updating

If you are using config file environments, your Puppet masters will log deprecation warnings about it.

Read the page on [config file environments][], and remove the relevant settings and sections from puppet.conf on your Puppet masters. Then, switch to using [directory environments][].

### Context

See the note about [the deprecation of config file environments.](./deprecated_misc.html#config-file-environments)


Activerecord Storeconfigs Settings
-----

* [`async_storeconfigs`](/references/3.7.latest/configuration.html#asyncstoreconfigs)
* [`dbadapter`](/references/3.7.latest/configuration.html#dbadapter)
* [`dbconnections`](/references/3.7.latest/configuration.html#dbconnections)
* [`dblocation`](/references/3.7.latest/configuration.html#dblocation)
* [`dbmigrate`](/references/3.7.latest/configuration.html#dbmigrate)
* [`dbname`](/references/3.7.latest/configuration.html#dbname)
* [`dbpassword`](/references/3.7.latest/configuration.html#dbpassword)
* [`dbport`](/references/3.7.latest/configuration.html#dbport)
* [`dbserver`](/references/3.7.latest/configuration.html#dbserver)
* [`dbsocket`](/references/3.7.latest/configuration.html#dbsocket)
* [`dbuser`](/references/3.7.latest/configuration.html#dbuser)
* [`queue_source`](/references/3.7.latest/configuration.html#queuesource)
* [`queue_type`](/references/3.7.latest/configuration.html#queuetype)
* [`rails_loglevel`](/references/3.7.latest/configuration.html#railsloglevel)
* [`railslog`](/references/3.7.latest/configuration.html#railslog)
* [`thin_storeconfigs`](/references/3.7.latest/configuration.html#thinstoreconfigs)

### Now

[ActiveRecord-based storeconfigs](./deprecated_api.html#activerecord-storeconfigs) still works, and these settings configure various parts of it.

### In Puppet 4.0

It's gone, and so are these settings. Use [PuppetDB][] instead.

### Detecting and Updating

Look for the settings in your Puppet master config files and delete them. Install [PuppetDB][].

### Context

ActiveRecord storeconfigs was an old and slow system that could do only a fraction of what PuppetDB does.


Inventory Service Settings
-----

* [`inventory_port`](/references/3.7.latest/configuration.html#inventoryport)
* [`inventory_server`](/references/3.7.latest/configuration.html#inventoryserver)
* [`inventory_terminus`](/references/3.7.latest/configuration.html#inventoryterminus)

### Now

These settings configure the deprecated [inventory service][].

### In Puppet 4.0

The inventory service is gone, as are the settings.

### Detecting and Updating

If you or your applications use the inventory service, you should switch to [PuppetDB][]. Delete these settings from puppet.conf on your Puppet master(s), install PuppetDB, and change any custom code to speak directly to PuppetDB's API.

### Context

The inventory service could do a fraction of what PuppetDB does, and with a slower and less flexible API. It's been replaced.

Other Settings
-----

### `parser`

* [`parser`](/references/3.7.latest/configuration.html#parser)

#### Now

This setting switches between the `current` (3.x) and `future` (4.0) parsers.

#### In Puppet 4.0

The parser currently known as `future` is the only available parser, and the `parser` setting is removed.

#### Detecting and Updating

If this is set in puppet.conf on your master(s), you'll need to delete it. Additionally, many people use the corresponding `--parser` option on the command line for certain tasks; if you do, you'll need to remove it from the affected commands.

### `allow_variables_with_dashes`

* [`allow_variables_with_dashes`](/references/3.7.latest/configuration.html#allowvariableswithdashes)

#### Now

This setting once permitted hyphens in variable names, but no longer works.

#### In Puppet 4.0

This setting is removed.

#### Detecting and Updating

If this is set in puppet.conf on your master(s), you'll need to delete it.

#### Context

This setting was designed to reduce the damage from a pair of bad decisions we made in the Puppet 2.7 era. Hyphenated variables cause major problems in the language.

### `catalog_format`

* [`catalog_format`](/references/3.7.latest/configuration.html#catalogformat)

#### Now

This setting is deprecated. It sets the format Puppet agent uses to cache its last catalog to disk.

#### In Puppet 4.0

This setting is gone. Use `preferred_serialization_format` instead.

#### Detecting and Updating

Search your [puppet.conf][] file on agent nodes for the name of the setting.

#### Context

This setting is replaced by the [`preferred_serialization_format`](/references/3.7.latest/configuration.html#preferredserializationformat) setting.

### `dynamicfacts`

* [`dynamicfacts`](/references/3.7.latest/configuration.html#dynamicfacts)

#### Now

This setting is not functional, but you can still set it in [puppet.conf][]. It causes a deprecation warning.

#### In Puppet 4.0

This setting is removed.

#### Detecting and Updating

If set, Puppet will log the following deprecation warning:

    The dynamicfacts setting is deprecated and will be ignored.

Remove the setting from puppet.conf.

#### Context

This setting was intended to ignore dynamic facts when deciding whether changed facts should result in a recompile, but that workflow was never implemented.

### `ignoreimport`

* [`ignoreimport`](/references/3.7.latest/configuration.html#ignoreimport)

#### Now

When `ignoreimport` is set to true, Puppet won't import additional manifest files. This can be useful when syntax checking individual manifests with `puppet parser validate`. (The setting is necessary because `import` directives are handled before parsing, unlike basically every other part of the language.)

#### In Puppet 4.0

The setting is removed.

#### Detecting and Updating

Puppet will **not** issue deprecation warnings if you're using this setting (although it will issue deprecations if you are using the `import` keyword).

To find out whether you're using it, you should:

* Check your commit hooks, continuous integration tools, and any other automated tooling that might use the `puppet parser validate` command. Look at their invocations of Puppet's commands, and see whether the `--ignoreimport` option is specified on the command line.
* Check your [puppet.conf][] file, in case it's set there. (This should almost never be the case.)

If you find any use of the `ignoreimport` setting, you can safely remove it after you have stopped using the `import` keyword in all of your manifests.

#### Context

This is part of [deprecating the `import` keyword](./deprecated_language.html#importing-manifests).

### `listen`

* [`listen`](/references/3.7.latest/configuration.html#listen)

#### Now

Setting `listen = true` will cause `puppet agent` to listen for incoming connections on port 8140.

#### In Puppet 4.0

The setting is removed.

#### Detecting and Updating

See [the note on `puppet kick`.](./deprecated_command.html#puppet-kick)

#### Context

See [the note on `puppet kick`.](./deprecated_command.html#puppet-kick)


### `manifestdir`

* [`manifestdir`](/references/3.7.latest/configuration.html#manifestdir)

#### Now

This setting is used to construct the default value of the `manifest` setting. It has no other purpose. As of 3.7.1, Puppet no longer raises an error if the directory specified doesn't exist.

#### In Puppet 4.0

The setting is gone and its directory doesn't have to exist.

#### Detecting and Updating

No action is needed to update the behavior.

#### Context

This setting didn't need to exist before, and it has even less meaning now that setting a global value for `manifest` in puppet.conf is deprecated.

### `masterlog`

* [`masterlog`](/references/3.7.latest/configuration.html#masterlog)

#### Now

This setting is not functional, but it can be set in puppet.conf.

#### In Puppet 4.0

The setting is removed.

#### Detecting and Updating

No action is needed. If you have the setting set and you upgrade, Puppet will give an error and ask you to remove it.

#### Context

This setting once could specify where the Puppet master logged, but it has not worked in some time. Syslog is the default log destination. You can configure `puppet master`s logging behavior with the `--logdest` command line option.

### `stringify_facts = true`

* [`stringify_facts`](/references/latest/configuration.html#stringifyfacts)

#### Now

This setting defaults to `true`, which disables structured facts and coerces all fact values to strings. You can enable structured facts by setting `stringify_facts = false` in [puppet.conf][] on every agent node and Puppet master.

#### In Puppet 4.0

The setting still exists, but it defaults to `false`. Structured facts are enabled by default, and custom facts can return any valid data type. If you need all facts coerced to strings, you can change the setting's value to `true`.

#### Detecting and Updating

Puppet **will not** log a deprecation warning if you rely on the default behavior of coercing all facts to strings. You can see whether you're affected by setting `stringify_facts = false` and seeing whether anything changes its behavior.

#### Context

This should only affect you if you have custom facts that naïvely return something other than a string (a bool, an array...) and you **also** have regexes or templates that don't expect structured facts and rely on that implicit coercion to string.

* [PUP-406: Deprecate stringify_fact = true](https://tickets.puppetlabs.com/browse/PUP-406)


### `templatedir`

* [`templatedir`](/references/3.7.latest/configuration.html#templatedir)

#### Now

When given a relative path, the [`template` function](/references/3.7.latest/function.html#template) will try to find templates in this directory before trying to find them in modules.

#### In Puppet 4.0

The `template` function will only search modules when given a relative path.

#### Detecting and Updating

Puppet **will not** log a deprecation warning if you have files in your `templatedir`.

To decide whether you're affected, look for the `templatedir` setting in puppet.conf on your master(s). Next, locate the default `templatedir` by running `puppet config print templatedir --section master`.

In either case, see if the directory exists, then check whether there's anything in the directory. If so, you'll need to identify any Puppet code that references those templates, move the templates into modules, and change the code that refers to them so Puppet can find them in their new homes.

#### Context

This setting and the behavior of `template()` predate Puppet's module system. Now that all templates should go in modules, it adds unnecessary complexity.

### `couchdb_url`

#### Now

This setting specifies the server to use with the CouchDB facts terminus.

#### In Puppet 4.0

The CouchDB facts terminus is removed, and this setting is disallowed.

#### Detecting and Updating

Check for this setting in [puppet.conf][] on your Puppet master(s), and remove it if it exists.

#### Context

See [the deprecation note for the CouchDB facts terminus.](./deprecated_api.html#couchdb-facts-terminus)
