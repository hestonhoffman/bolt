# Bolt data types

## `ApplyResult`

An `ApplyResult` is returned from an [apply action](applying_manifest_blocks.html#)
as part of a `ResultSet` object and contains information about the apply action.

The following functions are available to `ApplyResult` objects. They are accessed with
dot notation `$apply_result.function`.

### `action`

**Returns**

`String`: The type of result (`apply`)

### `error`

**Returns**

`Error`: An object constructed from the `_error` field of the result's value.

### `message`

**Returns**

`String`: The `_output` field of the result's value.

### `ok`

**Returns**

`Boolean`: Whether the result was successful.

### `report`

**Returns**

`Hash`: The Puppet report from the apply action.

### `target`

**Returns**

`Target`: The target the result is from.

### `to_data`

**Returns**

`Hash`: A serialized representation of `ApplyResult`.


## `Result`

A `Result` is returned from an action executed on a single target and contains
information about the execution. `Result`s are aggregated to make a `ResultSet`
object.

The following functions are available to `Result` objects. They are accessed with
dot notation `$result.function`.

### `[]`

Accesses the value hash directly and returns the value for the key.

> **Note:** This function does not use dot notation and is called directly on the
  `Result`, i.e. `$result[key]`

**Returns**

`Data`: The accessed value.

### `action`

**Returns**

`String`: The type of result (`task`, `command`, etc.)

### `error`

**Returns**

`Error`: An object constructed from the `_error` field of the result's value.

### `message`

**Returns**

`String`: The `_output` field of the result's value.

### `ok`

**Returns**

`Boolean`: Whether the result was successful.

### `status`

**Returns**

`String`: Either `success` if the result was successful or `failure`.

### `target`

**Returns**

`Target`: The target the result is from.

### `value`

**Returns**

`Hash`: The output or return of executing on the target.


## `ResultSet`

A `ResultSet` is returned from an execution function and contains one or more `Result`
objects. An [apply action](applying_manifest_blocks.html#) returns a `ResultSet` with one 
or more `ApplyResult` objects.

The following functions are available to `ResultSet` objects. They are accessed with
dot notation `$result_set.function`.

### `[]`

Access a range of results or the _i_ th result in the set.

> **Note:** This function does not use dot notation and is called directly on the
  `Result`, e.g. `$result_set[0]`, `$result_set[0, 2]`

**Returns**

`Variant[Result, ApplyResult, Array[Variant[Result, ApplyResult]]]`: The accessed results.

### `count`

**Returns**

`Integer`: The number of results in the set.

### `empty`

**Returns**

`Boolean`: Whether the set is empty.

### `error_set`

**Returns**

`ResultSet`: The set of failing results.

### `filter_set(block)`

**Parameters**

- `block`

  The block to filter the set by.

**Returns**

`ResultSet`: The set of filtered results.

### `find(String $target_name)`

**Parameters**

- `String` target_name

  The string name of the target to retrive a result for.

**Returns**

`Variant[Result, ApplyResult]`: The target's result.

### `first`

**Returns**

`Variant[Result, ApplyResult]`: The first result in the set. Useful for unwrapping single results.

### `names`

**Returns**

`Array[String]`: The names of all targets that have results in the set.

### `ok`

**Returns**

`Boolean`: Whether all results were successful. Equivalent to `$result_set.error_set.empty`.

### `ok_set`

**Returns**

`ResultSet`: The set of successful results.

### `results`

**Returns**

`Array[Variant[Result, ApplyResult]]`: All results in the set.

### `targets`

**Returns**

`Array[Target]`: The list of targets that have results in the set.

### `to_data`

**Returns**

`Array[Hash`: An array of serialized representations of each result in the set.


## `Target`

The `Target` object represents a target and its specific connection options.

The following functions are available to `ResultSet` objects. They are accessed with
dot notation `$result_set.function`.

### `config`

**Returns**

`Hash[String, Data]`: The inventory configuration for the target.

> **Note:** This function does not return default configuration values or
  configuration set in a `bolt.yaml` file. It only returns the configuration set
  in an `inventory.yaml` file or the configuration set during a plan using the 
  `Target.new` or `set_config()` functions.

### `facts`

**Returns**

`Hash[String, Data]`: The target's facts.

> **Note::** This function does not lookup facts for a target and only returns
  the facts specified in an `inventory.yaml` file or set on a target during a
  plan run.

### `features`

**Returns**

`Array[String]`: The target's features.

### `host`

**Returns**

`String`: The target's hostname.

### `name`

**Returns**

`String`: The target's human-readable name, or its URI if a name was not given.

### `password`

**Returns**

`String`: The password to use when connecting to the target.

### `plugin_hooks`

**Returns**

`Hash[String, Data]`: The target's 
[`plugin_hooks` configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_reference.html#plugin-hooks-configuration-options)

### `port`

**Returns**

`Integer`: The target's connection port.

### `protocol`

**Returns**

`String`: The protocol used to connect to the target.

> **Note:** This is equivalent to the target's `transport`, except for targets
  using the `remote` transport. For example, a target with the URI 
  `http://example.com` using the `remote` transport would return `http` for
  the `protocol`.

### `safe_name`

**Returns**

`String`: The target's safe name. Equivalent to `name` if a name was given, or
the target's `uri` with any password omitted.

### `target_alias`

**Returns**

`Variant[String, Array[String]]`: The target's aliases.

### `uri`

**Returns**

`String`: The target's URI.

### `user`

**Returns**

`String`: The user to connect to the target.

### `vars`

**Returns**

`Hash[String, Data]`: The target's variables.
