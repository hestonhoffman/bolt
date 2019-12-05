# Inventory file version 2

## Available fields

### Top level fields

The following fields are available at the top level of a version 2 inventory file. The top level of an inventory file is the group `all` and has similar fields as a `group` object. Using a version 2 inventory file requires `version: 2` at the top level.

> **Note:** Starting with Bolt 2.0 the inventory file will default to version 2 and will not require the `version` field.

| Key | Description | Type |
| --- | ----------- | ---- |
| `config` | The configuration for the `all` group. | `Hash` |
| `facts` | The facts for the `all` group. | `Hash` |
| `features` | The features for the `all` group. | `Array[String]`
| `groups` | A list of targets and groups and their associated configuration. | `Array[Group]` |
| `targets` | A list of targets and their associated configuration. | `Array[Target]` |
| `vars` | The vars for the `all` group. | `Hash` |
| `version` | The version of the inventory file.<br>**Default:** 1 | `Integer` |


### Group object

A group is a list of targets and groups and their associated configuration. Each group is a map that can contain any of the following fields. A group is **required** to have a `name` field.

| Key | Description | Type |
| --- | ----------- | ---- |
| `config` | The configuration for the group. | `Hash` |
| `facts` | The facts for the group. | `Hash` |
| `features` | The features for the group. | `Array[String]`
| `groups` | A list of groups and their associated configuration. | `Array[Group]` |
| **`name`** | The name of the group.<br> **Required.** | `String` |
| `targets` | A list of targets and their associated configuration. | `Array[Target]` |
| `vars` | The vars for the group. | `Hash` |

```yaml
version: 2
groups:
  - name: linux
    targets:
      - target1.example.com
      - target2.example.com
    config:
      transport: ssh
  - name: windows
    targets:
      - target3.example.com
      - target4.example.com
    config:
      transport: winrm
```


### Target object

A target can be specified with the string representation of the `uri` or as a hash with any of the following fields.

| Key | Description | Type |
| --- | ----------- | ---- |
| `alias` | A unique alias to refer to the target. | `String` |
| `config` | The configuration for the target. | `Hash` |
| `facts` | The facts for the target. | `Hash` |
| `features` | The features for the target. | `Array[String]`
| **`name`** | A human-readable name for a target.<br> **Required** when specifying a target using a hash. Optional if using `uri`. | `String` |
| **`uri`** | The uri of the target.<br> **Required** when specifying a target using a hash. Optional if using `name`. | `String` |
| `vars` | The vars for the target. | `Hash` |

A target can be specified with just the string representation of its `uri`.

```yaml
version: 2
targets:
  - target1.example.com
  - target2.example.com
```

A target can also be specified with a hash that requires either a `name` or `uri` key.

```yaml
version: 2
targets:
  - uri: target1.example.com
    alias: target1
    config:
      transport: ssh
  - uri: target2.example.com
    alias: target2
    config:
      transport: winrm
```


## Precedence

When searching for target configuration, the URI used to create the target is matched to the target's name. Any target-based configuration - such as `host`, `transport`, and `port` - will override group configuration values. Any target-based data - such as `alias`, `facts`, and `vars` - will be merged with group-based data.

Target values take precedence over group values and are searched first. For configuration values, the first value found for a target is used. Values are searched for in a depth first order. The first item in an array is searched first.

Inventory files are not context-aware. Any configuration or data set for a target, whether in a target definition or a group, will apply to all definitions of the target. For example, the following inventory file contains two definitions of `mytarget`. 

```yaml
version: 2
groups:
  - name: group1
    targets:
      - name: mytarget
        uri: target.example.com
        config:
          ssh:
            user: puppet
    config:
      ssh:
        host-key-check: false
  - name: group2
    targets:
      - name: mytarget
        uri: target.example.com
        config:
          ssh:
            password: bolt
```

When running Bolt commands on `group2` the target `mytarget` will have its `user`, `password`, and `host-key-checks` fields set, even though `user` and `host-key-check` were set in `group1`.

The command `bolt inventory show -t <targets> --detail` provides a quick way to view the resolved configuration and data for a target or group of targets. Running the command `bolt inventory show -t group2 --detail` shows the resolved configuration and data for `mytarget`, which includes configuration set in `group1`.

```json
{
  "targets": [
    {
      "name": "mytarget",
      "uri": "target.example.com",
      "config": {
        "ssh": {
          "host-key-check": false,
          "password": "bolt",
          "user": "puppet"
        },
        "transport": "ssh"
      }
    }
  ]
}
```


## Plugins

Plugins can be used to dynamically load information into the inventory file. Plugins either ship with Bolt or are installed as Puppet modules that have the same name as the plugin. The plugin framework is based on a set of plugin hooks that are implemented by plugin authors and called by Bolt.

Read more about the different types of plugins and configuring plugins on the [plugins reference page](#).

> **Note:** Plugins are only available in version 2 inventory files.

### Bundled plugins

Bolt ships with several plugins.

| Plugin | Description | Docs |
| ------ | ----------- | ---- |
| `aws_inventory` | Generate targets from AWS EC2 instances. | [Docs](https://forge.puppet.com/puppetlabs/aws_inventory) |
| `azure_inventory` | Generate targets from Azure VMs and VM scale sets. | [Docs](https://forge.puppet.com/puppetlabs/azure_inventory) |
| `pkcs7` | Use encrypted values for sensitive data. | [Docs](using_plugins.html#pkcs7) |
| `prompt` | Prompt users to enter sensitive configuration information instead of storing it in a file. | [Docs](using_plugins.html#prompt) |
| `puppetdb` | Query PuppetDB for a group of targets. | [Docs](using_plugins.html#puppetdb) |
| `task` | Use a task to load targets, configuration, or other data. | [Docs](using_plugins.html#task) |
| `terraform` | Generate targets from local and remote Terraform state files. | [Docs](https://forge.puppet.com/puppetlabs/terraform) |
| `vault` | Set values by accessing secrets from a Key/Value engine on a Hashicorp Vault server. | [Docs](https://forge.puppet.com/puppetlabs/vault) |
| `yaml` | Compose multiple YAML files into a single file. | [Docs](https://forge.puppet.com/puppetlabs/yaml) |


## Migrating from version 1

To maintain compatibility with future versions of Bolt, a Version 1 inventory file should be migrated to Version 2. This process can be completed manually by chaning the names of some keys in the inventory or automatically using a Bolt command.

### Automatic migration

To automatically migrate a Version 1 inventory file to version 2, use the `bolt project migrate` command. Bolt will locate the inventory file for the current Bolt project and migrate it in place. Specific projects and inventory files can be specified using the `--boltdir` and `--inventoryfile` options.

> **Note:** The `bolt project migrate` command will modify an inventory file in place and does not preserve comments or formatting. Before using the command make sure to backup the inventory file.


### Manual migration

To manually migrate a Version 1 inventory file, begin by adding `version: 2` at the top level. Next, change all instances of `nodes` keys to `targets` keys.

`nodes` => `targets`

Lastly, change any instance of a `name` key in a `Target` object to a `uri` key.

`name` => `uri`

#### Version 1 inventory file

```yaml
groups:
  - name: linux
    nodes:
      - name: target1.example.com
        alias: target1
      - name: target2.example.com
        alias: target2
```

#### Version 2 inventory file

```yaml
version: 2
groups:
  - name: linux
    targets:
      - uri: target1.example.com
        alias: target1
      - uri: target2.example.com
        alias: target2
```


## Examples

### Basic inventory file

The following inventory file contains a basic hierarchy of groups and targets. As with all inventory files, it has a top level group named `all`, which refers to all targets in the inventory. The `all` group has two subgroups named `linux` and `windows`.

The `linux` group lists its targets and sets the default transport for the targets to the SSH protocol.

The `windows` group lists its targets and sets the default transport for the targets to the WinRM protocol.

```yaml
version: 2
groups:
  - name: linux
    targets:
      - target1.example.com
      - target2.example.com
    config:
      transport: ssh
  - name: windows
    targets:
      - uri: target3.example.com
        alias: windows1
      - uri: target4.example.com
        alias: windows2
    config:
      transport: winrm
```

### Detailed inventory file

The following inventory file contains a more detailed hierarchy of groups and targets. As with all inventory files, it has a top level group named `all`, which refers to all targets in the inventory. The `all` group has two subgroups named `ssh_nodes` and `win_nodes`. 

The `ssh_nodes` group has two subgroups - `webservers` and `memcached` - and sets the default transport for targets in the group to the SSH protocol. It also specifies a few configuration options for the SSH transport. Each of the subgroups lists the targets in the group and the `memcached` group has additional SSH transport configuration for its targets.

The `win_nodes` group also has two subgroups - `domaincontrollers` and `testservers` - and sets the default transport for targets in the group to the WinRM protocol. It also specifies a few configuration options for the WinRM transport. Each of the subgroups lists the targets in the group and the `testservers` group has additional WinRM transport configuration for its targets.

```yaml
version: 2
groups:
  - name: ssh_nodes
    groups:
      - name: webservers
        targets:
          - 192.168.100.179
          - 192.168.100.180
          - 192.168.100.181
      - name: memcached
        targets:
          - 192.168.101.50
          - 192.168.101.60
        config:
          ssh:
            user: root
    config:
      transport: ssh
      ssh:
        user: centos
        private-key: ~/.ssh/id_rsa
        host-key-check: false
  - name: win_nodes
    groups:
      - name: domaincontrollers
        targets:
          - 192.168.110.10
          - 192.168.110.20
      - name: testservers
        targets:
          - 172.16.219.20
          - 172.16.219.30
        config:
          winrm:
            realm: MYDOMAIN
            ssl: false
    config:
      transport: winrm
      winrm:
        user: DOMAIN\opsaccount
        password: S3cretP@ssword
        ssl: true
```

### Using plugins

The following inventory file uses several bundled plugins.

* The `yaml` plugin is used to compose `aws_inventory.yaml` and `azure_inventory.yaml` into a single inventory file loaded by Bolt.
* The `aws_inventory` plugin is used to generate targets from AWS EC2 instances.
* The `azure_inventory` plugin is used to generate targets from Azure VMs.
* The `vault` plugin is used to load a secret from a Hashicorp Vault server and use it as a password.
* The `prompt` plugin is used to configure the `vault` plugin in a configuration file and prompt for the user's Vault password.

The `inventory.yaml` file.

```yaml
version: 2
groups:
  - _plugin: yaml
    filepath: inventory/aws_inventory.yaml
  - _plugin: yaml
    filepath: inventory/azure_inventory.yaml
```

The `aws_inventory.yaml` file.

```yaml
name: aws
targets:
  - _plugin: aws_inventory
    region: us-west-1
    target_mapping:
      uri: public_ip_address
config:
  transport: ssh
  ssh:
    user: ec2-user
    password:
      _plugin: vault
      path: secrets/aws
      field: password
```

The `azure_inventory.yaml` file.

```yaml
name: azure
targets:
  - _plugin: azure_inventory
    location: westus
config:
  transport: winrm
  winrm:
    user: Administrator
    password:
      _plugin: vault
      path: secrets/azure
      field: password
```

The `bolt.yaml` configuration file.

```yaml
plugins:
  vault:
    server_url: https://127.0.0.1:8200
    cacert: /path/to/cert/cacert.pem
    auth:
      method: userpass
      user: developer
      pass:
        _plugin: prompt
        message: Enter your Vault password
```

To verify that plugin references are resolved correctly and to view the targets and values loaded, use the command `bolt inventory show -t all --detail`.
