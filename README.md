# Overview
This package can be used to create a wake `runner` that will set environment
variables for jobs from JSON values based on the resources they request. A
default runner is also included with this package.

# Resources
Resources describe a tool that is requested by a job. They are strings.
This package expects resources to have the format `{vendor}/{tool}/{version}`.
If there is no vendor then the format is `{tool}/{version}`.

# Usage
To use this you need to create a JSON file that maps resources to a JSON object
representing the environment variables you want to set when that resource is
requested. The JSON should be a series of nested objects where the keys correspond
to the `{vendor}/{tool}/{version}` or `{tool}/{version}` format above. `version`
keys should be exact versions, not version match expressions.
```json
{
  "google": {
    "protobuf": {
      "3.5.1": {
        "PATH": "/protobuf/bin"
      }
    }
  },
  "verilator": {
    "4.008": {
      "PATH": "/verilator/bin",
      "VERILATOR_ROOT": "/verilator"
    }
  }
}
```

Then you need to `publish` a list of `Path`s to your JSON files to `environmentJSONs`.
```wake
publish environmentJSONs = source "path/to/environment.json", Nil
```

Now when a wake job requests the resource `google/protobuf/>=3.5.0`,
`/protobuf/bin` will be added to the `PATH` environment variable for that job.

The `version` portion of the resource string can be an exact version or version match
expression. A version match is determined by the `semverMatches` wake function.
`semverMatches` uses the [python semver](https://github.com/python-semver/python-semver/blob/master/docs/usage.rst#comparing-versions-through-an-expression)
(see link for match expression documentation) package to check whether the version matches.


# Making your own runner
This package defines the global function `makeJSONEnvironmentRunner` that takes
a `JSONEnvironmentRunnerPlan` and returns a `Runner`. You can use this to make
your own runner if you would like to use a different version matching function
or a different set of JSONs. This also defines `semverMatches`, which is the
python semver matching function described above.

Example:
```wake
publish runner = (
  def name = "myRunner"
  def scoreFn baseScore = baseScore +. 2.0
  def semverMatchFn = semverMatches
  def jvalues = subscribe myRunnerJValues
  def baseRunner = defaultRunner
  makeJSONEnvironmentRunnerPlan name scoreFn semverMatchFn jvalues baseRunner
  | makeJSONEnvironmentRunner
), Nil
```
