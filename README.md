# Overview
This package can be added to your workspaces to provide job runners
that can fulfill resources requested by plans.

# Resources
Resources describe a tool that is requested by a job. They are strings
that have the format `{vendor}/{tool}/{version}`. If there is no vendor
then the format is `{tool}/{version}`.

# Runners
This package provides a runner that sets the `PATH` of wake
jobs to the value of the `WAKE_PATH` environment variable if it is set
by the user.

This package also provides a runner that will provide the riscv-tools
resource if the user has set the `RISCV` environment variable.
