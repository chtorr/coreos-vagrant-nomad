# coreos-vagrant-nomad
A multi-box Vagrant setup to work with Nomad and Consul.  It runs on CoreOS, but with etcd/locksmith/flannel disabled.  This is not a production ready setup, but merely a environment for developing against Nomad and Consul.

On start it will:
* start a 3 node Consul cluster
* start a 3 node Nomad server/client cluster
    * Note: Nomad is NOT running in docker containers.  It's doable, but funky, and not particularly important to dev against its API.
* start [hashi-ui](https://github.com/jippi/hashi-ui) on each node


Please note that Consul and hashi-ui are both running in --net=host mode for ease of use.  


### Web UI's
You can access the various UI's from your host machine:

* hashi-ui: http://172.17.8.101:3000
* consu ui: http://172.17.8.101:8500/ui
* nomad ui: http://172.17.8.101:4646/ui


### Vagrant details

The Vagrantfile is mostly the standard [CoreOS-vagrant](https://github.com/coreos/coreos-vagrant) config.  It is hard coded to use `172.17.8.*`.  If you need to change this IP range you will also need to change the `-retry-join` Consul parameter in `cl.conf` to match the IP of the first server.

There is one addition, if `$GOPATH` is set it will be mounted to `/home/core/go`.  If not, `/home/core/go` will mount `../../../`.

This directory will also be mounted in as a shared folder, to `/home/core/go/share`.

### Customize CoreOS Ignition config:

This repo is using CoreOS Ignition.  If you wish to modify the startup behavior (formerly performed with Userdata), you will need to:

* install ignition config transpiler https://github.com/coreos/container-linux-config-transpiler
* update cl.conf
* `ct < cl.conf > config.ign`
* `vagrant up` (you'll need to `destroy` first if it's already running)

See https://coreos.com/ignition/docs/latest/ for more details

### Usage

* `git clone` this repo somewhere on your local machine (I put it in my GOPATH for ease of running other projects)
* `vagrant up`
* `vagrant ssh core-01`
* `nomad run <job file>` 