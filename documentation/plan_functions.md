# Bolt functions and types

## Functions


### `add_facts`

Deep merges a hash of facts with the existing facts on a target.

> **Note:** Not available in apply block


```
add_facts(Target $target, Hash $facts)
```


**Returns**

`Target` A `Target` object.

**Parameters**

* **target** `Target` A target.
* **facts** `Hash` A hash of fact names to values that may include structured facts.



**Examples**

Adding facts to a target
```
add_facts($target, { 'os' => { 'family' => 'windows', 'name' => 'windows' } })
```


### `add_to_group`

Adds a target to specified inventory group.

> **Note:** Not available in apply block


```
add_to_group(Boltlib::TargetSpec $targets, String[1] $group)
```


**Returns**

`Any` 

**Parameters**

* **targets** `Boltlib::TargetSpec` A pattern or array of patterns identifying a set of targets.
* **group** `String[1]` The name of the group to add targets to.



**Examples**

Add new Target to group.
```
Target.new('foo@example.com', 'password' => 'secret').add_to_group('group1')
```
Add new target to group by name.
```
add_to_group('bolt:bolt@web.com', 'group1')
```
Add an array of targets to group by name.
```
add_to_group(['host1', 'group1', 'winrm://host2:54321'], 'group1')
```
Add a comma separated list list of targets to group by name.
```
add_to_group('foo,bar,baz', 'group1')
```


### `apply_prep`

Installs the `puppet-agent` package on targets if needed, then collects facts,
including any custom facts found in Bolt's modulepath. The package is
installed using either the configured plugin or the `task` plugin with the
`puppet_agent::install` task.

Agent installation will be skipped if the target includes the `puppet-agent` feature, either as a
property of its transport (PCP) or by explicitly setting it as a feature in Bolt's inventory.

> **Note:** Not available in apply block


```
apply_prep(Boltlib::TargetSpec $targets)
```


**Returns**

`Any` 

**Parameters**

* **targets** `Boltlib::TargetSpec` A pattern or array of patterns identifying a set of targets.



**Examples**

Prepare targets by name.
```
apply_prep('target1,target2')
```


### `catch_errors`

Catches errors in a given block and returns them. This will return the
output of the block if no errors are raised. Accepts an optional list of
error kinds to catch.

> **Note:** Not available in apply block


```
catch_errors(Optional[Array[String[1]]] $error_types, Callable[0, 0] &$block)
```


**Returns**

`Any` If an error is raised in the block then the error will be returned,
otherwise the result will be returned

**Parameters**

* **error_types** `Optional[Array[String[1]]]` An array of error types to catch
* **&block** `Callable[0, 0]` The block of steps to catch errors on



**Examples**

Catch errors for a block
```
catch_errors() || {
  run_command("whoami", $targets)
  run_command("adduser ryan", $targets)
}
```
Catch parse errors for a block of code
```
catch_errors(['bolt/parse-error']) || {
 run_plan('canary', $targets)
 run_plan('other_plan)
 apply($targets) || {
   notify { "Hello": }
 }
}
```


### `ctrl::do_until`

Repeat the block until it returns a truthy value. Returns the value.


```
ctrl::do_until(Optional[Hash[String[1], Any]] $options, Callable &$block)
```


**Returns**

`Any` 

**Parameters**

* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.
* **&block** `Callable` 


**Additional options**

* **limit** `Numeric` The number of times to repeat the block.


**Examples**

Run a task until it succeeds
```
ctrl::do_until() || {
  run_task('test', $target, _catch_errors => true).ok()
}
```
Run a task until it succeeds or fails 10 times
```
ctrl::do_until('limit' => 10) || {
  run_task('test', $target, _catch_errors => true).ok()
}
```


### `ctrl::sleep`

Sleeps for specified number of seconds.


```
ctrl::sleep(Numeric $period)
```


**Returns**

`Undef` 

**Parameters**

* **period** `Numeric` Time to sleep (in seconds)



**Examples**

Sleep for 5 seconds
```
ctrl::sleep(5)
```


### `facts`

Returns the facts hash for a target.


```
facts(Target $target)
```


**Returns**

`Hash[String, Data]` The target's facts.

**Parameters**

* **target** `Target` A target.



**Examples**

Getting facts
```
facts($target)
```


### `fail_plan`

Raises a `Bolt::PlanFailure` exception to signal to callers that the plan failed.

Plan authors should call this function when their plan is not successful. The
error may then be caught by another plans `run_plan` function or in Bolt itself

> **Note:** Not available in apply block


#### Fail a plan, generating an exception from the parameters.

```
fail_plan(String[1] $msg, Optional[String[1]] $kind, Optional[Hash[String[1], Any]] $details, Optional[String[1]] $issue_code)
```


**Returns**

`Any` Raises an exception.

**Parameters**

* **msg** `String[1]` An error message.
* **kind** `Optional[String[1]]` An easily matchable error kind.
* **details** `Optional[Hash[String[1], Any]]` Machine-parseable details about the error.
* **issue_code** `Optional[String[1]]` Unused.



**Examples**

Raise an exception
```
fail_plan('We goofed up', 'task-unexpected-result', { 'result' => 'null' })
```

#### Fail a plan, generating an exception from an existing Error object.

```
fail_plan(Error $error)
```


**Returns**

`Any` Raises an exception.

**Parameters**

* **error** `Error` An error object.



**Examples**

Raise an exception
```
fail_plan(Error('We goofed up', 'task-unexpected-result', { 'result' => 'null' }))
```


### `file::exists`

Check if a file exists.


```
file::exists(String $filename)
```


**Returns**

`Boolean` Whether the file exists.

**Parameters**

* **filename** `String` Absolute path or Puppet file path.



**Examples**

Check a file on disk
```
file::exists('/tmp/i_dumped_this_here')
```
check a file from the modulepath
```
file::exists('example/files/VERSION')
```


### `file::join`

Join file paths.


```
file::join(String *$paths)
```


**Returns**

`String` The joined file path.

**Parameters**

* ***paths** `String` The paths to join.



**Examples**

Join file paths
```
file::join('./path', 'to/files')
```


### `file::read`

Read a file and return its contents.


```
file::read(String $filename)
```


**Returns**

`String` The file's contents.

**Parameters**

* **filename** `String` Absolute path or Puppet file path.



**Examples**

Read a file from disk
```
file::read('/tmp/i_dumped_this_here')
```
Read a file from the modulepath
```
file::read('example/files/VERSION')
```


### `file::readable`

Check if a file is readable.


```
file::readable(String $filename)
```


**Returns**

`Boolean` Whether the file is readable.

**Parameters**

* **filename** `String` Absolute path or Puppet file path.



**Examples**

Check a file on disk
```
file::readable('/tmp/i_dumped_this_here')
```
check a file from the modulepath
```
file::readable('example/files/VERSION')
```


### `file::write`

Write a string to a file.


```
file::write(String $filename, String $content)
```


**Returns**

`Undef` 

**Parameters**

* **filename** `String` Absolute path.
* **content** `String` File content to write.



**Examples**

Write a file to disk
```
file::write('C:/Users/me/report', $apply_result.first.report)
```


### `get_resources`

Query the state of resources on a list of targets using resource definitions in Bolt's modulepath.
The results are returned as a list of hashes representing each resource.

Requires the Puppet Agent be installed on the target, which can be accomplished with apply_prep
or by directly running the `puppet_agent::install` task. In order to be able to reference types without
string quoting (for example `get_resources($target, Package)` instead of `get_resources($target, 'Package')`),
run the command `bolt puppetfile generate-types` to generate type references in `$Boldir/.resource_types`.

> **Note:** Not available in apply block


```
get_resources(Boltlib::TargetSpec $targets, Variant[String, Type[Resource], Array[Variant[String, Type[Resource]]]] $resources)
```


**Returns**

`ResultSet` A result set with a list of hashes representing each resource.

**Parameters**

* **targets** `Boltlib::TargetSpec` A pattern or array of patterns identifying a set of targets.
* **resources** `Variant[String, Type[Resource], Array[Variant[String, Type[Resource]]]]` A resource type or instance, or an array of such.



**Examples**

Collect resource states for packages and a file
```
get_resources('target1,target2', [Package, File[/etc/puppetlabs]])
```


### `get_target`

Get a single target from inventory if it exists, otherwise create a new Target.

> **Note:** Calling `get_target('all')` returns an empty array.


```
get_target(Boltlib::TargetSpec $name)
```


**Returns**

`Target` A single target, either new or from inventory.

**Parameters**

* **name** `Boltlib::TargetSpec` A Target name.



**Examples**

Create a new Target from a URI
```
get_target('winrm://host2:54321')
```
Get an existing Target from inventory
```
get_target('existing-target')
```


### `get_targets`

Parses common ways of referring to targets and returns an array of Targets.

> **Note:** Not available in apply block


```
get_targets(Boltlib::TargetSpec $names)
```


**Returns**

`Array[Target]` A list of unique Targets resolved from any target URIs and groups.

**Parameters**

* **names** `Boltlib::TargetSpec` A pattern or array of patterns identifying a set of targets.



**Examples**

Resolve a group
```
get_targets('group1')
```
Resolve a target URI
```
get_targets('winrm://host2:54321')
```
Resolve array of groups and/or target URIs
```
get_targets(['host1', 'group1', 'winrm://host2:54321'])
```
Resolve string consisting of a comma-separated list of groups and/or target URIs
```
get_targets('host1,group1,winrm://host2:54321')
```
Run on localhost
```
get_targets('localhost')
```


### `out::message`

Output a message for the user.

This will print a message to stdout when using the human output format,
and print to stderr when using the json output format

> **Note:** Not available in apply block


```
out::message(String $message)
```


**Returns**

`Undef` 

**Parameters**

* **message** `String` The message to output.



**Examples**

Print a message
```
out::message('Something went wrong')
```


### `puppetdb_fact`

Collects facts based on a list of certnames.

If a node is not found in PuppetDB, it's included in the returned hash with an empty facts hash.
Otherwise, the node is included in the hash with a value that is a hash of its facts.


```
puppetdb_fact(Array[String] $certnames)
```


**Returns**

`Hash[String, Data]` A hash of certname to facts hash for each matched Target.

**Parameters**

* **certnames** `Array[String]` Array of certnames.



**Examples**

Get facts for nodes
```
puppetdb_fact(['app.example.com', 'db.example.com'])
```


### `puppetdb_query`

Makes a query to {https://puppet.com/docs/puppetdb/latest/index.html puppetdb}
using Bolt's PuppetDB client.


```
puppetdb_query(Variant[String, Array[Data]] $query)
```


**Returns**

`Array[Data]` Results of the PuppetDB query.

**Parameters**

* **query** `Variant[String, Array[Data]]` A PQL query.
[`https://puppet.com/docs/puppetdb/latest/api/query/tutorial-pql.html Learn more about Puppet's query language, PQL`](#https://puppet.com/docs/puppetdb/latest/api/query/tutorial-pql.html Learn more about Puppet's query language, PQL)



**Examples**

Request certnames for all nodes
```
puppetdb_query('nodes[certname] {}')
```


### `remove_from_group`

Removes a target from the specified inventory group.

The target is removed from all child groups and all parent groups where the target has
not been explicitly defined. A target cannot be removed from the `all` group.

> **Note:** Not available in apply block


```
remove_from_group(Boltlib::TargetSpec $target, String[1] $group)
```


**Returns**

`Any` 

**Parameters**

* **target** `Boltlib::TargetSpec` A pattern identifying a single target.
* **group** `String[1]` The name of the group to remove the target from.



**Examples**

Remove Target from group.
```
remove_from_group('foo@example.com', 'group1')
```
Remove failing Targets from the rest of a plan
```
$result = run_command(uptime, my_group, '_catch_errors' => true)
$result.error_set.targets.each |$t| { remove_from_group($t, my_group) }
run_command(next_command, my_group) # does not target the failing nodes.
```


### `resolve_references`

Evaluates all `_plugin` references in a hash and returns the resolved reference data.


```
resolve_references(Data $references)
```


**Returns**

`Data` A hash of resolved reference data.

**Parameters**

* **references** `Data` A hash of reference data to resolve.



**Examples**

Resolve a hash of reference data
```
$references = {
  "targets" => [
    "_plugin" => "terraform",
    "dir" => "path/to/terraform/project",
    "resource_type" => "aws_instance.web",
    "uri" => "public_ip"
  ]
}

resolve_references($references)
```


### `run_command`

Runs a command on the given set of targets and returns the result from each command execution.
This function does nothing if the list of targets is empty.

> **Note:** Not available in apply block


#### Run a command.

```
run_command(String[1] $command, Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **command** `String[1]` A command to run on target.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a command on targets
```
run_command('hostname', $targets, '_catch_errors' => true)
```

#### Run a command, logging the provided description.

```
run_command(String[1] $command, Boltlib::TargetSpec $targets, String $description, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **command** `String[1]` A command to run on target.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **description** `String` A description to be output when calling this function.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a command on targets
```
run_command('hostname', $targets, 'Get hostname')
```


### `run_plan`

Runs the `plan` referenced by its name. A plan is autoloaded from `$MODULEROOT/plans`.

> **Note:** Not available in apply block


#### Run a plan

```
run_plan(String $plan_name, Optional[Hash] $args)
```


**Returns**

`Boltlib::PlanResult` The result of running the plan. Undef if plan does not explicitly return results.

**Parameters**

* **plan_name** `String` The plan to run.
* **args** `Optional[Hash]` A hash of arguments to the plan. Can also include additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a plan
```
run_plan('canary', 'command' => 'false', 'targets' => $targets, '_catch_errors' => true)
```

#### Run a plan, specifying `$nodes` or `$targets` as a positional argument.

> **Note:** When running a plan with both a `$nodes` and `$targets` parameter, and using the second
positional argument, the plan will fail.

```
run_plan(String $plan_name, Boltlib::TargetSpec $targets, Optional[Hash] $args)
```


**Returns**

`Boltlib::PlanResult` The result of running the plan. Undef if plan does not explicitly return results.

**Parameters**

* **plan_name** `String` The plan to run.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **args** `Optional[Hash]` A hash of arguments to the plan. Can also include additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a plan
```
run_plan('canary', $targets, 'command' => 'false')
```


### `run_script`

Uploads the given script to the given set of targets and returns the result of having each target execute the script.
This function does nothing if the list of targets is empty.

> **Note:** Not available in apply block


#### Run a script.

```
run_script(String[1] $script, Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **script** `String[1]` Path to a script to run on target. May be an absolute path or a modulename/filename selector for a
file in $MODULEROOT/files.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **arguments** `Array[String]` An array of arguments to be passed to the script.
* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a local script on Linux targets as 'root'
```
run_script('/var/tmp/myscript', $targets, '_run_as' => 'root')
```
Run a module-provided script with arguments
```
run_script('iis/setup.ps1', $target, 'arguments' => ['/u', 'Administrator'])
```

#### Run a script, logging the provided description.

```
run_script(String[1] $script, Boltlib::TargetSpec $targets, String $description, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **script** `String[1]` Path to a script to run on target. May be an absolute path or a modulename/filename selector for a
file in $MODULEROOT/files.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **description** `String` A description to be output when calling this function.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **arguments** `Array[String]` An array of arguments to be passed to the script.
* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Run a script
```
run_script('/var/tmp/myscript', $targets, 'Downloading my application')
```


### `run_task`

Runs a given instance of a `Task` on the given set of targets and returns the result from each.
This function does nothing if the list of targets is empty.

> **Note:** Not available in apply block


#### Run a task.

```
run_task(String[1] $task_name, Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $args)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **task_name** `String[1]` The task to run.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **args** `Optional[Hash[String[1], Any]]` A hash of arguments to the task. Can also include additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.
* **_noop** `Boolean` Run the task in noop mode if available.


**Examples**

Run a task as root
```
run_task('facts', $targets, '_run_as' => 'root')
```

#### Run a task, logging the provided description.

```
run_task(String[1] $task_name, Boltlib::TargetSpec $targets, Optional[String] $description, Optional[Hash[String[1], Any]] $args)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **task_name** `String[1]` The task to run.
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **description** `Optional[String]` A description to be output when calling this function.
* **args** `Optional[Hash[String[1], Any]]` A hash of arguments to the task. Can also include additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.
* **_noop** `Boolean` Run the task in noop mode if available.


**Examples**

Run a task
```
run_task('facts', $targets, 'Gather OS facts')
```


### `set_config`

Set configuration options on a target.

> **Note:** Not available in apply block

> **Note:** Only compatible with inventory v2


```
set_config(Target $target, Variant[String, Array[String]] $key_or_key_path, Any $value)
```


**Returns**

`Target` The Target with the updated config

**Parameters**

* **target** `Target` The Target object to configure. See [`get_targets`](#get_targets).
* **key_or_key_path** `Variant[String, Array[String]]` The configuration setting to update.
* **value** `Any` The configuration value



**Examples**

Set the transport for a target
```
set_config($target, 'transport', 'ssh')
```
Set the ssh password
```
set_config($target, ['ssh', 'password'], 'secret')
```
Overwrite ssh config
```
set_config($target, 'ssh', { user => 'me', password => 'secret' })
```


### `set_feature`

Sets a particular feature to present on a target.

Features are used to determine what implementation of a task should be run.
Supported features are:
- `powershell`
- `shell`
- `puppet-agent`

> **Note:** Not available in apply block


```
set_feature(Target $target, String $feature, Optional[Boolean] $value)
```


**Returns**

`Target` The target with the updated feature

**Parameters**

* **target** `Target` The Target object to add features to. See [`get_targets`](#get_targets).
* **feature** `String` The string identifying the feature.
* **value** `Optional[Boolean]` Whether the feature is supported.



**Examples**

Add the `puppet-agent` feature to a target
```
set_feature($target, 'puppet-agent', true)
```


### `set_var`

Sets a variable `{ key => value }` for a target.

> **Note:** Not available in apply block


```
set_var(Target $target, String $key, Data $value)
```


**Returns**

`Target` The target with the updated feature

**Parameters**

* **target** `Target` The Target object to set the variable for. See [`get_targets`](#get_targets).
* **key** `String` The key for the variable.
* **value** `Data` The value of the variable.



**Examples**

Set a variable on a target
```
$target.set_var('ephemeral', true)
```


### `system::env`

Get an environment variable.


```
system::env(String $name)
```


**Returns**

`Optional[String]` The environment variable's value.

**Parameters**

* **name** `String` Environment variable name.



**Examples**

Get the USER environment variable
```
system::env('USER')
```


### `upload_file`

Uploads the given file or directory to the given set of targets and returns the result from each upload.
This function does nothing if the list of targets is empty.

> **Note:** Not available in apply block


#### Upload a file or directory.

```
upload_file(String[1] $source, String[1] $destination, Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **source** `String[1]` A source path, either an absolute path or a modulename/filename selector for a
file or directory in $MODULEROOT/files.
* **destination** `String[1]` An absolute path on the target(s).
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Upload a local file to Linux targets and change owner to 'root'
```
upload_file('/var/tmp/payload.tgz', '/tmp/payload.tgz', $targets, '_run_as' => 'root')
```
Upload a module file to a Windows target
```
upload_file('postgres/default.conf', 'C:/ProgramData/postgres/default.conf', $target)
```

#### Upload a file or directory, logging the provided description.

```
upload_file(String[1] $source, String[1] $destination, Boltlib::TargetSpec $targets, String $description, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **source** `String[1]` A source path, either an absolute path or a modulename/filename selector for a
file or directory in $MODULEROOT/files.
* **destination** `String[1]` An absolute path on the target(s).
* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **description** `String` A description to be output when calling this function.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Upload a file
```
upload_file('/var/tmp/payload.tgz', '/tmp/payload.tgz', $targets, 'Uploading payload to unpack')
```


### `vars`

Returns a hash of the 'vars' (variables) assigned to a target.

Vars can be assigned through the inventory file or `set_var` function.
Plan authors can call this function on a target to get the variable hash
for that target.


```
vars(Target $target)
```


**Returns**

`Hash[String, Data]` A hash of the 'vars' (variables) assigned to a target.

**Parameters**

* **target** `Target` The Target object to get variables from. See [`get_targets`](#get_targets).



**Examples**

Get vars for a target
```
$target.vars
```


### `wait_until_available`

Wait until all targets accept connections.

> **Note:** Not available in apply block


```
wait_until_available(Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target. Successful results have no value.

**Parameters**

* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **options** `Optional[Hash[String[1], Any]]` A hash of additional options.


**Additional options**

* **description** `String` A description for logging. (default: 'wait until available')
* **wait_time** `Numeric` The time to wait. (default: 120)
* **retry_interval** `Numeric` The interval to wait before retrying. (default: 1)
* **_catch_errors** `Boolean` Whether to catch raised errors.


**Examples**

Wait for targets
```
wait_until_available($targets, wait_time => 300)
```


### `without_default_logging`

Define a block where default logging is suppressed.

Messages for actions within this block will be logged at `info` level instead
of `notice`, so they will not be seen normally but will still be present
when `verbose` logging is requested.

> **Note:** Not available in apply block


```
without_default_logging(Callable[0, 0] &$block)
```


**Returns**

`Undef` 

**Parameters**

* **&block** `Callable[0, 0]` The block where action logging is suppressed.



**Examples**

Suppress default logging for a series of functions
```
without_default_logging() || {
  notice("Deploying on ${nodes}")
  get_targets($targets).each |$target| {
    run_task(deploy, $target)
  }
}
```


### `write_file`

Write contents to a file on the given set of targets.

> **Note:** Not available in apply block


```
write_file(String $content, String[1] $destination, Boltlib::TargetSpec $targets, Optional[Hash[String[1], Any]] $options)
```


**Returns**

`ResultSet` A list of results, one entry per target.

**Parameters**

* **targets** `Boltlib::TargetSpec` A pattern identifying zero or more targets. See [`get_targets`](#get_targets) for accepted patterns.
* **content** `String` File content to write.
* **destination** `String[1]` An absolute path on the target(s).
* **options** `Optional[Hash[String[1], Any]]` 


**Additional options**

* **_catch_errors** `Boolean` Whether to catch raised errors.
* **_run_as** `String` User to run as using privilege escalation.


**Examples**

Write a file to a target
```
$content = 'Hello, world!'
write_file($targets, $content, '/Users/me/hello.txt')
```



## Types

### `ApplyResult`

An `ApplyResult` is returned from an [apply action](https://puppet.com/docs/bolt/latest/applying_manifest_blocks.html#)
as part of a `ResultSet` object and contains information about the apply action.

The following functions are available to `ApplyResult` objects. They are accessed with
dot notation `$apply_result.function`.

- `action`

  Returns a `String` representation of the result type (`apply`).

- `error`

  Returns an `Error` object constructed from the `_error` in the value.

- `message`

  Returns the `_output` key from the value.

- `ok`

  Returns a `Boolean` indicating whether the `ApplyResult` was successful.

- `report`

  Returns a `Hash` containing the Puppet report from the application.

- `target`

  Returns the `Target` object that the `ApplyResult` is from.

- `to_data`

  Returns a `Hash` representation of `ApplyResult`.

### `Result`

A `Result` is returned from an action executed on a `Target` as part of a `ResultSet`
object and contains information about the execution.

The following functions are available to `Result` objects. They are accessed with
dot notation `$result.function`.

- `[]`

  Accesses the value hash directly and returns the value for the key.

  > **Note:** This function does not use dot notation and is called directly on the
    `Result`, i.e. `$result[key]`

- `action`

  Returns a `String` representation of the result type (`task`, `command`, etc.)

- `error`

  Returns an `Error` object contructed from the `_error` in the value.

- `message`

  Returns the `_output` key from the value.

- `ok`

  Returns `Boolean` indicating whether the `Result` was successful.

- `status`

  Returns `success` if the `Result` was successful and `failure` otherwise.

- `target`

  Returns the `Target` object that the `Result` is from.

- `value`

  Returns a `Hash` containing the value of the `Result`.

### `ResultSet`

A `ResultSet` is returned from an execution function and contains one or more `Result`
objects. An [apply action](https://puppet.com/docs/bolt/latest/applying_manifest_blocks.html#)
returns a `ResultSet` with one or more `ApplyResult` objects.

The following functions are available to `ResultSet` objects. They are accessed with
dot notation `$result_set.function`.

- `[]`

  Access a range of results or the _i_ th result in the set.

  > **Note:** This function does not use dot notation and is called directly on the
    `Result`, e.g. `$result_set[0]`, `$result_set[0, 2]`

- `count`

  Returns an `Integer` count of the number of results in the set.

- `empty`

  Returns a `Boolean` indicated whether the set is empty.

- `error_set`

  Returns a `ResultSet` that contains only the results that failed.

- `filter_set(block)`

  Filters the set with the given block and returns a new `ResultSet` object.

- `find(String $target_name)`

  Returns the `Result` for the specified `Target`.

- `first`

  Returns the first `Result` in the set. Useful for unwrapping single results.

- `names`

  Returns an `Array` of `String` names of all targets in the set.

- `ok`

  Returns a `Boolean` indicating whether all results were successful. Equivalent
  to `$result_set.error_set.empty`.

- `ok_set`

  Returns a `ResultSet` that contains only the results that were successful.

- `results`

  Returns an array of `Result` or `ApplyResult` objects.

- `targets`

  Returns an `Array` of `Target` objects from all results in the set.

- `to_data`

  Returns an `Array` of `Hash` representations of each result.

### `Target`

The `Target` object represents a target and its specific connection options.

The following functions are available to `ResultSet` objects. They are accessed with
dot notation `$result_set.function`.

- `config`

  Returns a `Hash` of target-specific configuration.

  > **Note:** This function does not return default configuration values or
    configuration set in a `bolt.yaml` file.

- `facts`

  Returns a `Hash` for the target's facts.

  > **Note::** This function does not lookup facts for a target and only returns
    the facts specified in an `inventory.yaml` file or set on a target during a
    plan run.

- `features`

  Returns an `Array` of `Strings` listing the target's features.

- `host`

  Returns a `String` for the target's host.

- `name`

  Returns a `String` for the target's human-readable name.

- `password`

  Returns a `String` for the target's password.

- `plugin_hooks`

  Returns a `Hash` for the target's 
  [`plugin_hooks` configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_reference.html#plugin-hooks-configuration-options)

- `port`

  Returns an `Integer` for the target's port.

- `protocol`

  Returns a `String` for the protocol used to connect to the target.

- `safe_name`

  Returns a `String` for the target's safe name. The safe name is the target's `name`
  if one is given, or its `uri` with any `password` omitted.

- `target_alias`

  Returns an `Array` of `String` aliases for the target.

- `uri`

  Returns a `String` for the target's URI.

- `user`

  Returns a `String` for the target's user.

- `vars`

  Returns a `Hash` for the target's vars.
