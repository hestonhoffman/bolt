# Configuring Bolt

You can configure Bolt's options and features at a project level, a user level,
or a system-wide level.

## Project-level configuration

All configurable options in Bolt can be set at the project level. Configuration
that should only apply to the current project should be set at this level. Most
of the time, you will only need to set configuration at the project level.

Project-level configuration can be set in three locations. Bolt configuration
can be set in a `bolt-project.yaml` file, inventory configuration can be set in
an `inventory.yaml` file, and all configuration can be set in a `bolt.yaml`
file.

> **Note:** Project configuration files are loaded from the [Bolt project
> directory](bolt_project_directories.md). The default project directory is
> `~/.puppetlabs/bolt/`.

The preferred method for setting project-level configuration is to use a
combination of `bolt-project.yaml` and `inventory.yaml` files. This maintains
a clear distinction between Bolt configuration and inventory configuration.

### `bolt-project.yaml`

> **Note:** The `bolt-project.yaml` file is experimental and is subject to
> change. You can read more about Bolt projects in [Experimental
> features](experimental_features.md).

**Filepath:** `<project-directory>/bolt-project.yaml`

The project configuration file supports options that configure how Bolt
behaves, such as how many threads it can use when running commands on targets,
and configures different components of the project, such as a list of plans
and tasks that are visible to the user. Any directory containing a
`bolt-project.yaml` file is automatically considered a [Project
directory](bolt_project_directories.md).

Project configuration files take precedence over `bolt.yaml` files. If a
project directory contains both files, Bolt will only load and read
configuration from `bolt-project.yaml`.

The `bolt-project.yaml` file supports most of the options that a `bolt.yaml`
file does, with the exception of the following inventory configuration options:

- `transport`
- `docker`
- `local`
- `pcp`
- `remote`
- `ssh`
- `winrm`

You can view a full list of the available options in [Bolt configuration
options](bolt_configuration_reference.md).

### `inventory.yaml`

**Filepath:** `<project-directory>/inventory.yaml`

The inventory file is a structured data file that contains groups of targets
that you can run Bolt commands on, as well as configuration for the transports
used to connect to the targets. Most projects will include an inventory file.

Inventory configuration can be set at multiple levels in an inventory file
under a `config` option. The following options can be set under this option:

- `transport`
- `docker`
- `local`
- `pcp`
- `remote`
- `ssh`
- `winrm`

You can read more about inventory files and the available options in
[Inventory files](inventory_file_v2.md).

### `bolt.yaml`

> **Note** The project-level `bolt.yaml` file is on the path towards
> deprecation and will be removed in a future version of Bolt. Use
> `bolt-project.yaml` and `inventory.yaml` files instead.

**Filepath:** `<project-directory>/bolt.yaml`

The Bolt configuration file can be used to set all available configuration
options, including default inventory configuration options. Any directory
containing a `bolt.yaml` file is automatically considered a [Project
directory](bolt_project_directories.md).

You can view a full list of the available options in [Bolt configuration
options](bolt_configuration_reference.md).

## User-level configuration

Most configurable options in Bolt can be set at the user level. Configuration
that should apply to all projects for a particular user should be set at this
level. This might include paths to private keys, credentials for a plugin,
or default inventory configuration that is common to all of your projects.

User-level configuration can be set in two locations. Configuration that is
not project-specific can be set in a `bolt-defaults.yaml` file, while all
configuration can be set in a `bolt.yaml` file.

The preferred method for setting user-level configuration is to use a
`bolt-defaults.yaml` file. This file does not allow you to set project-specific
configuration, such as the path to an inventory file, so is less likely
to lead to errors where content from another project is loaded.

### `bolt-defaults.yaml`

**Filepath:** `~/.puppetlabs/etc/bolt/bolt-defaults.yaml`

The defaults configuration file supports most of Bolt's configuration options,
with the exception of options that are project-specific such as `inventoryfile`
and `modulepath`.

The `bolt-defaults.yaml` file takes precedence over a `bolt.yaml` file in the
same directory. If the directory contains both files, Bolt will only load and 
read configuration from `bolt-defaults.yaml`.

You can view a full list of the available options in [`bolt-defaults.yaml`
options](bolt_defaults_reference.md).

### `bolt.yaml`

> **Note** The user-level `bolt.yaml` file is deprecated and will be removed
> in a future version of Bolt. Use a `bolt-defaults.yaml` file instead.

**Filepath:** `~/.puppetlabs/etc/bolt/bolt.yaml`

The Bolt configuration file can be used to set all available configuration
options, including project-specific configuration options.

You can view a full list of the available options in [Bolt configuration
options](bolt_configuration_reference.md).

## System-wide configuration

Most configurable options in Bolt can be set at the system level. Configuration
that should apply to all users and all projects should be set at this level.
This might include configuration for connecting to an organization's Forge
proxy, the number of threads Bolt should use when connecting to targets, or
setting credentials for connecting to PuppetDB.

System-wide configuration can be set in two locations. Configuration that is
not project-specific can be set in a `bolt-defaults.yaml` file, while all
configuration can be set in a `bolt.yaml` file.

The preferred method for setting user-level configuration is to use a
`bolt-defaults.yaml` file. This file does not allow you to set project-specific
configuration, such as the path to an inventory file, so is less likely
to lead to errors where content from another project is loaded.

### `bolt-defaults.yaml`

**\*nix Filepath:** `/etc/puppetlabs/bolt/bolt-defaults.yaml`

**Windows Filepath:** `%PROGRAMDATA%\PuppetLabs\bolt\etc\bolt-defaults.yaml`

The defaults configuration file supports most of Bolt's configuration options,
with the exception of options that are project-specific such as `inventoryfile`
and `modulepath`.

The `bolt-defaults.yaml` file takes precedence over a `bolt.yaml` file in the
same directory. If the directory contains both files, Bolt will only load and 
read configuration from `bolt-defaults.yaml`.

You can view a full list of the available options in [`bolt-defaults.yaml`
options](bolt_defaults_reference.md).

### `bolt.yaml`

> **Note** The system-wide `bolt.yaml` file is deprecated and will be removed
> in a future version of Bolt. Use a `bolt-defaults.yaml` file instead.

**\*nix Filepath:** `/etc/puppetlabs/bolt/bolt.yaml`

**Windows Filepath:** `%PROGRAMDATA%\PuppetLabs\bolt\etc\bolt.yaml`

The Bolt configuration file can be used to set all available configuration
options, including project-specific configuration options.

You can view a full list of the available options in [Bolt configuration
options](bolt_configuration_reference.md).

## Configuration precedence

Bolt uses the following precedence when interpolating configuration settings,
from highest precedence to lowest:

  - Target URI (i.e. ssh://user:password@hostname:port)
  - [Inventory file](inventory_file_v2.md) options
  - [Command line flags](bolt_command_reference.md)
  - Project-level configuration file
  - User-level configuration file
  - System-wide configuration file
  - SSH configuration file options (e.g. `~/.ssh/config`)

## Merge strategy

When merging configurations, Bolt's strategy is to shallow merge any options
that accept hashes and to overwrite any options that do not accept hashes. There
are two exceptions to this strategy:

- [Transport configuration](bolt_transports_reference.md) is deep-merged.

- [Plugin configuration](using_plugins.md#configuring-plugins) is shallow-merged
  for _each individual plugin_.

### Transport configuration merge strategy

Transport configuration is deep merged. 

For example, given this SSH configuration in an inventory file:

```yaml
# ~/.puppetlabs/bolt/inventory.yaml
config:
  ssh:
    user: bolt
    password: bolt
    host-key-check: false
```

And this this SSH configuration in a user configuration file:

```yaml
# ~/.puppetlabs/etc/bolt/bolt-defaults.yaml
inventory-config:
  ssh:
    user: puppet
    password: puppet
    private-key: ~/path/to/key/id_rsa
```
The merged Bolt configuration would look like this:

```yaml
ssh:
  user: bolt
  password: bolt
  host-key-check: false
  private-key: ~/path/to/key/id_rsa
```

### Plugin configuration merge strategy

The `plugins` option accepts a hash where each key is the name of a plugin and
its value is a hash of configuration options for the plugin. When merging
configurations, the configuration for individual plugins is shallow merged.

> **Note:** If a plugin is configured in one file, but is not configured in a
> file with a higher precedence, the configuration for the plugin will still be
> present in the merged configuration.

For example, given this plugin configuration in a project configuration file:

```yaml
# ~/.puppetlabs/bolt/bolt-project.yaml
plugins:
  vault:
    auth:
      method: userpass
      user: bolt
      pass: bolt
```

And this plugin configuration in a system-wide configuration file:

```yaml
# /etc/puppetlabs/bolt/bolt-defaults.yaml
plugins:
  aws_inventory:
    credentials: /etc/aws/credentials
  vault:
    server_url: http://example.com
    auth:
      method: token
      token: xxxx-xxxx-xxxx-xxxx
```

The merged Bolt configuration would look like this:

```yaml
plugins:
  aws_inventory:
    credentials: /etc/aws/credentials
  vault:
    server_url: 'http://example.com'
    auth:
      method: userpass
      user: bolt
      pass: bolt
```

## Additional documentation

- **[Project directories](bolt_project_directories.md#)**  

  Bolt runs in the context of a project directory or a `Boltdir`. This directory
  contains all of the configuration, code, and data loaded by Bolt.

- **[Bolt configuration options](bolt_configuration_reference.md)**  

  Your Bolt configuration file can contain global and transport options.

- **[Using Bolt with Puppet Enterprise](bolt_configure_orchestrator.md)**  

  If you're a Puppet Enterprise (PE) customer, you can configure Bolt to use the
  PE orchestrator and perform actions on managed targets. Pairing PE with Bolt
  enables role-based access control, logging, and visual reports in the PE
  console.

- **[Connecting Bolt to PuppetDB](bolt_connect_puppetdb.md)**  

  Configure Bolt to connect to PuppetDB.
