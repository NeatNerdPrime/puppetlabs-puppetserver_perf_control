# puppetlabs-puppetserver_perf_control
An r10k control repo for Puppet Server (and PE) perf testing and memory analysis

## What is this repo?

This repo is an r10k control repo that we will be using to generate catalogs for
automated gatling performance testing, as well as for other memory analysis and
performance testing work.

## ~branching strategy

The `production` branch of this repo should be thought of as the mainline/HEAD for
development, and it should be assumed that it is subject to change at any time.

Therefore, for any kind of perf or memory test that we expect to be repeatable, we
should create a branch off of production at a specific point in time, and treat
these branches as immutable going forward.  Recommended branch naming convention is:

    <YYYYMMDD>_<description>

So, for example,

    20160614_memtest

At some point we may need to re-evaluate this strategy if we start piling up a huge
number of these branches, but for now this seems like the best plan to ensure that
we have a fixed reference point for reliable, repeatable test outcomes.

## How to use this repo during development

* Create a local `r10k.yaml` config file that looks something like this:

```
:cachedir: '/my/scratch/dir/perf-control-repo/cache'

:sources:
  :puppetserver-perf:
    remote: 'git@github.com:puppetlabs/puppetlabs-puppetserver_perf_control'
    basedir: '/my/puppetserver/src/dir/target/master-code-dir/environments'
```

* Install r10k as a gem
* Run something like `r10k deploy environment 20160614_memtest -v debug -c /my/scratch/dir/perf-control-repo/r10k.yamlhome/cprice/work/puppet-server/scratch/perf-control-repo/r10k.yaml`.  (You can leave out the environment name if you want to deploy all environments.)
* Spin up a vmpooler VM to use as a test agent; install puppet-agent, configure the `server` to point to your host, and then run `puppet agent -t --environment 20160614_memtest`
* Edit the `manifests/site.pp` file for the appropriate environment on your puppetserver to map a role to your vmpooler node
* Re-run the agent, lather, rinse, repeat

