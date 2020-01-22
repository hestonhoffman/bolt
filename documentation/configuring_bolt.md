# Configuring Bolt

The `bolt.yaml` file is Bolt's configuration file. It configures all of Bolt's options and features, including global options, transports, plugins, PuppetDB connection, and log files.

For a complete list of Bolt's settings, see the [configuration reference](bolt_configuration_reference.md).

## Configuration files

Bolt has three configuration files. If a configuration file is not located at the expected path, or is empty, it will not be loaded. Bolt's configuration files are located at the following paths, from highest precedence to lowest:

- **Project**

  `<boltdir>/bolt.yaml`

  > **Note:** The project configuration file is loaded from the [Bolt project directory](bolt_project_directories.md). The default project directory is `~/.puppetlabs/bolt/`.

- **User**

  `~/.puppetlabs/etc/bolt/bolt.yaml`

- **System-wide**

  \*nix: `/etc/puppetlabs/bolt/bolt.yaml`

  Windows: `%PROGRAMDATA%\PuppetLabs\bolt\etc\bolt.yaml`

## Configuration precedence

Bolt uses the following precedence, from highest precedence (cannot be overriden) to lowest, when merging configuration options:

  - Target URI (i.e. ssh://user:password@hostname:port) --- highest precedence
  - [Inventory file](inventory_file.md) options
  - [Command line flags](bolt_command_reference.md)
  - Project configuration file
  - User configuration file
  - System-wide configuration file
  - SSH configuration file options (e.g. `~/.ssh/config`) --- lowest precedence

## Merge strategy

When merging configuration, Bolt's strategy is to shallow merge any options that accept hashes and to overwrite any options that do not accept hashes. There are two exceptions to this strategy:

- [Transport configuration](bolt_configuration_reference.md#transport-configuration-options) (e.g. `ssh`, `winrm`) is deep-merged.

- [Plugin configuration](using_plugins.md#configuring-plugins) is shallow-merged for _each individual plugin_.

### Transport configuration

Transport configuration is deep merged.

Setting configuration in a project configuration file:

```yaml
ssh:
  user: bolt
  password: bolt
  host-key-check: false
```

Setting configuration in a user configuration file:

```yaml
ssh:
  user: puppet
  password: puppet
  private-key: ~/path/to/key/id_rsa
```

Merged configuration:

```yaml
ssh:
  user: bolt
  password: bolt
  host-key-check: false
  private-key: ~/path/to/key/id_rsa
  ...
```

### Plugin configuration

The `plugins` option accepts a hash where each key is the name of a plugin and its value is a hash of configuration options for the plugin. When merging configuration, the configuration for individual plugins is shallow merged.

> **Note:** If a plugin is configured in one file, but is not configured in a file with a higher precedence, the configuration for the plugin will still be present in the merged configuration.

Setting configuration in a project configuration file:

```yaml
plugins:
  vault:
    auth:
      method: userpass
      user: bolt
      pass: bolt
```

Setting configuration in a system-wide configuration file:

```yaml
plugins:
  aws_inventory:
    credentials: /etc/aws/credentials
  vault:
    server_url: http://example.com
    auth:
      method: token
      token: xxxx-xxxx-xxxx-xxxx
```

Merged configuration:

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
  
  Bolt runs in the context of a project directory or a `Boltdir`. This directory contains all of the configuration, code, and data loaded by Bolt.

- **[Bolt configuration options](bolt_configuration_options.md)**  

  Your Bolt configuration file can contain global and transport options.

- **[Using Bolt with Puppet Enterprise](bolt_configure_orchestrator.md)**  
  
  If you're a Puppet Enterprise (PE) customer, you can configure Bolt to use the PE orchestrator and perform actions on managed targets. Pairing PE with Bolt enables role-based access control, logging, and visual reports in the PE console.

- **[Connecting Bolt to PuppetDB](bolt_connect_puppetdb.md)**  

  Configure Bolt to connect to PuppetDB.

