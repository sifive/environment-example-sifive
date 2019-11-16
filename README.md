# Overview
This package can be used to create a wake `runner` that will set environment
variables for jobs from JSON values based on the resources they request.

# Resources
Resources describe a tool that is requested by a job. They are strings.
This package expects resources to have the format `{vendor}/{tool}/{version}`.
If there is no vendor then the format is `{tool}/{version}`.

# Usage
This package defines the global function `makeJSONEnvironmentRunner` that takes
a `JSONEnvironmentRunnerPlan` and returns a `Runner`. This also defines
`semverMatches`, which is a semver matching function using this
[python package](https://pypi.org/project/semver/).

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
