# Deis

Deis is an open source PaaS that makes it easy to deploy, scale and manage containers used to host applications and services. Deis builds upon Docker and CoreOS to provide a private PaaS that is lightweight and flexible.

[![Build Status](https://travis-ci.org/opdemand/deis.png?branch=master)](https://travis-ci.org/opdemand/deis)
[![Coverage Status](https://coveralls.io/repos/opdemand/deis/badge.png?branch=master)](https://coveralls.io/r/opdemand/deis?branch=master)

![Deis Graphic](https://s3-us-west-2.amazonaws.com/deis-images/deis-graphic.png)

# New Deis
Deis has undergone several improvements recently. If you are updating
from Deis version 0.7.0 or earlier, there are several big changes you
should know about. Read the [MIGRATING.md](MIGRATING.md) document for
details.

If you need to use Deis with Chef integration, on Ubuntu 12.04 LTS, or
on DigitalOcean, you should use the
[v0.7.0 release](https://github.com/opdemand/deis/tree/v0.7.0) of Deis.

# Installation

Deis is a set of Docker containers that can be deployed anywhere including public cloud, private cloud, bare metal or your workstation. You will need [Docker](https://www.docker.io/) and [Vagrant](http://www.vagrantup.com/) to get started.

## Boot CoreOS

First, start a CoreOS virtual machine on VirtualBox. From a command prompt, `cd` to the root of the Deis project code and type:

```
vagrant up
```

Export some environment variables so you can connect to the VM using the `docker` and [`fleetctl`](https://github.com/coreos/fleet#building) clients on your workstation.

```
export DOCKER_HOST=tcp://172.17.8.100:4243
export FLEETCTL_TUNNEL=172.17.8.100
```

## Build Deis

Use `make pull` to download cached layers from the public Docker Index.  Then use `make build` to assemble all of the Deis components from Dockerfiles.  Grab some coffee while it builds the images on the CoreOS VM (it can take a while).

```
make pull
make build
```

## Run Deis

Use `make run` to start all Deis containers and attach to their log output.

```
make run
```

## Install the Deis Client
Either use `pip install deis` to install the latest [Deis Client](https://pypi.python.org/pypi/deis/), download [pre-compiled binaries](https://github.com/opdemand/deis/tree/master/client#get-started), or symlink `client/deis.py` to use your local development version.

```
ln -fs $(pwd)/client/deis.py /usr/local/bin/deis
```

## Register a User

Use the Deis Client to register a new user.

```
deis register http://local.deisapp.com:8000
deis keys:add
```

Use `deis keys:add` to add your SSH public key for `git push` access.

## Initalize a Cluster

Initalize a `dev` cluster with a list of CoreOS hosts and your CoreOS private key.

```
deis clusters:create dev local.deisapp.com --hosts=local.deisapp.com --auth=~/.vagrant.d/insecure_private_key
```

The `dev` cluster will be used as the default cluster for future `deis` commands.

# Usage

## Create an Application
Create an application on the default `dev` cluster.

```
deis create
```

Use `deis create --cluster=prod` to place the app on a different cluster.  Don't like our name-generator?  Use `deis create myappname`.

## Push
Push builds of your application from your local git repository or from a Docker Registry.  Each build creates a new release, which can be rolled back.

#### From a Git Repository
When you created the application, a git remote for Deis was added automatically.

```
git push deis master
```
This will use the Deis builder to package your application as a Docker Image and deploy it on your application's cluster.

## Configure
Configure your application with environment variables.  Each config change also creates a new release.

```
deis config:set DATABASE_URL=postgres://
```

Coming soon: Use the integrated ETCD namespace for service discovery between applications on the same cluster.

## Test
Test your application by running commands inside an ephemeral Docker container.

```
deis run make test
```

To integrate with your CI system, check the return code.

## Scale
Scale containers horizontally with ease.

```
deis scale web=8
```

## Debug
Access to aggregated logs makes it easy to troubleshoot problems with your application.

```
deis logs
```

Use `deis run` to execute one-off commands and explore the deployed container.  Coming soon: `deis attach` to jump into a live container.

## Known Issues

We have sometimes seen the VM reboot while doing `make build` against a
Vagrant virtual machine. If you see this issue using a recent version of
Vagrant and the current master version of Deis, please add to the issue
report at https://github.com/coreos/coreos-vagrant/issues/68 to help us
pin it down.

## License

Copyright 2014, OpDemand LLC

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
