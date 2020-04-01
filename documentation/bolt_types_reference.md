# Bolt data types

This page lists the Bolt data types and describes how to use them.

## `ApplyResult`

An [apply action](applying_manifest_blocks.md#return-value-of-apply-action)
returns an `ApplyResult`. An `ApplyResult` is part of a `ResultSet` object and
contains information about the apply action. You can access `ApplyResult`
functions with dot notation, using the syntax: `$apply_result.function`.

For example, the following plan uses an apply action to install Docker as root,
and logs a message with any errors that occurred during execution using the
`error` function:

```puppet
plan testing::test(
  TargetSpec $targets,
) {
  $results = apply($targets, _catch_errors => true, _noop => true, _run_as => root) {
   include 'docker'
}
  $results.each |$result| {
    notice($result.error)}
}
```

The following functions are available to `ApplyResult` objects. 

| Function | Type returned | Description |
|---|---|---|
| `action` | `String` | The action performed. `$apply_result.action` always returns the string `apply`. |
| `error` | `Error` | An object constructed from the `_error` field of the result's value. |
| `message` | `String` | The `_output` field of the result's value. |
| `ok` | `Boolean` | Whether the result was successful. |
| `report` | `Hash` | The Puppet report from the apply action. |
| `target` | `Target` | The target the result is from. |
| `to_data` | `Hash` | A serialized representation of `ApplyResult`. |

## `Result`

For each target that you execute an action on, Bolt returns a `Result` object and adds the `Result` to a `ResultSet` object. A `Result` object contains information about the action you executed on the target.

You can access `Result` functions with dot notation, using the syntax: `$result.function`.

For example, the following plan runs a `whoami` command and logs the targets that the command was executed on using the `target` function.

```puppet
plan testing::whoami(
  TargetSpec $targets,
) {
  $results = run_command('whoami', $targets, '_catch_errors' => true)

  $results.each |$result| {
    notice($result.target)
  }
}
```

The following functions are available to `Result` objects.

| Function | Type returned | Description |
|---|---|---|
| `[]` | `Data` | Accesses the value hash directly and returns the value for the key. This function does not use dot notation. Call the function directly on the `Result`. For example, `$result[key]`. |
| `action` | `String` | The type of result. For example, `task` or `command`. |
| `error` | `Error` | An object constructed from the `_error` field of the result's value. |
| `message` | `String` | The `_output` field of the result's value. |
| `ok` | `Boolean` | Whether the result was successful. |
| `status` | `String` | Either `success` if the result was successful or `failure`. |
| `target` | `Target` | The target the result is from. |
| `value` | `Hash` | The output or return of executing on the target. |

## `ResultSet`

For each target that you execute an action on, Bolt returns a `Result` object
and adds the `Result` to a `ResultSet` object. In the case of 
[apply actions](applying_manifest_blocks.md), Bolt returns a `ResultSet` 
with one or more `ApplyResult` objects.

You can access `ResultSet` functions with dot notation, using the syntax:
`$result_set.function`.

For example, the following plan runs a `whoami` command and logs a message
with all results in the `ResultSet` using the `results` function.

```puppet
plan testing::whoami(
  TargetSpec $targets,
) {
  $results = run_command('whoami', $targets, '_catch_errors' => true)
  
  notice($results.results)
}
```

The following functions are available to `ResultSet` objects:

| Function | Parameters (if applicable) | Type returned | Description |
|---|---|---|---|
| `[]` || `Variant[Result, ApplyResult, Array[Variant[Result, ApplyResult]]]` | The accessed results. This function does not use dot notation. Call the function directly on the `Result`. For example, `$result_set[0]`, `$result_set[0, 2]`. |
| `count` || `Integer` | The number of results in the set. |
| `empty` || `Boolean` | Whether the set is empty. |
| `error_set` || `ResultSet` | The set of failing results. |
| `filter_set(block)` | `block` | `ResultSet` | Filters a set of results by `block`. |
| `find(String $target_name)` | `String $target_name` | `Variant[Result, ApplyResult]` | Retrieves a result for a specific target. |
| `first` || `Variant[Result, ApplyResult]` | The first result in the set. Useful for unwrapping single results. |
| `names` || `Array[String]` | The names of all targets that have results in the set. |
| `ok` || `Boolean` | Whether all results were successful. Equivalent to `$result_set.error_set.empty`. |
| `results` || `Array[Variant[Result, ApplyResult]]` | All results in the set. |
| `targets` || `Array[Target]` | The list of targets that have results in the set. |
| `to_data` || `Array[Hash` | An array of serialized representations of each result in the set. |

## `Target`

The `Target` object represents a target and its specific connection options.

You can access `Target` functions using dot notation, using the syntax: `$target.function`.

For example, the following plan logs a message with each target's URI using the
`uri` function.

```puppet
plan testing::uri_check(
  TargetSpec $targets,
) {
  get_targets($targets).each | $target | {
    notice($target.uri)    
  } 
}
```

The following functions are available to `Target` objects:

| Function | Type returned | Description | Note |
|---|---|---|---|
| `config` | `Hash[String, Data]` | The inventory configuration for the target. | This function does not return default configuration values or configuration set in a `bolt.yaml` file. It only returns the configuration set in an `inventory.yaml` file or the configuration set during a plan using the `Target.new` or `set_config()` functions. |
| `facts` | `Hash[String, Data]` | The target's facts. | This function does not lookup facts for a target and only returns the facts specified in an `inventory.yaml` file or set on a target during a plan run. |
| `features` | `Array[String]` | The target's features. ||
| `host` | `String` | The target's hostname. ||
| `name` | `String` | The target's human-readable name, or its URI if a name was not given. ||
| `password` | `String` | The password to use when connecting to the target. ||
| `plugin_hooks` | `Hash[String, Data]` | The target's `plugin_hooks` [configuration options](bolt_configuration_reference.md#plugin-hooks-configuration-options). ||
| `port` | `Integer` | The target's connection port. ||
| `protocol` | `String` | The protocol used to connect to the target. | This is equivalent to the target's `transport`, except for targets using the `remote` transport. For example, a target with the URI `http://example.com` using the `remote` transport would return `http` for the `protocol`. |
| `safe_name` | `String` | The target's safe name. Equivalent to `name` if a name was given, or the target's `uri` with any password omitted. ||
| `target_alias` | `Variant[String, Array[String]]` | The target's aliases. ||
| `uri` | `String` | The target's URI. ||
| `user` | `String` | The user to connect to the target. ||
| `vars` | `Hash[String, Data]` | The target's variables. ||
