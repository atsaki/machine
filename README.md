# Docker Machine

Machine lets you create Docker hosts on your computer, on cloud providers, and
inside your own data center. It creates servers, installs Docker on them, then
configures the Docker client to talk to them.

It works a bit like this:

```console
$ docker-machine create -d virtualbox dev
INFO[0000] Creating SSH key...
INFO[0000] Creating VirtualBox VM...
INFO[0007] Starting VirtualBox VM...
INFO[0007] Waiting for VM to start...
INFO[0041] "dev" has been created and is now the active machine.
INFO[0041] To point your Docker client at it, run this in your shell: eval "$(docker-machine env dev)"

$ docker-machine ls
NAME   ACTIVE   DRIVER       STATE     URL                         SWARM
dev    *        virtualbox   Running   tcp://192.168.99.127:2376

$ eval "$(docker-machine env dev)"

$ docker run busybox echo hello world
Unable to find image 'busybox:latest' locally
511136ea3c5a: Pull complete
df7546f9f060: Pull complete
ea13149945cb: Pull complete
4986bf8c1536: Pull complete
hello world

$ docker-machine create -d digitalocean --digitalocean-access-token=secret staging
INFO[0000] Creating SSH key...
INFO[0001] Creating Digital Ocean droplet...
INFO[0002] Waiting for SSH...
INFO[0070] Configuring Machine...
INFO[0109] "staging" has been created and is now the active machine.
INFO[0109] To point your Docker client at it, run this in your shell: eval "$(docker-machine env staging)"

$ docker-machine ls
NAME      ACTIVE   DRIVER         STATE     URL                          SWARM
dev                virtualbox     Running   tcp://192.168.99.127:2376
staging   *        digitalocean   Running   tcp://104.236.253.181:2376
```

## Installation and documentation

Full documentation [is available here](https://docs.docker.com/machine/).

### Cloudstack

Create machines on your [Apache Cloudstack IaaS](http://cloudstack.apache.org/). You need to have a valid couple of API Key and Secret Key with the required rights associated (see with your Cloudstack admin or the [docs](http://cloudstack.apache.org/docs/en-US/index.html) for more details).

You have a lot of freedom within CloudStack to define what type of OS you want to use for your docker machines. This makes a plugin like this kind of hard since you can use a wide variety of operating systems (e.g. CentOS, Ubuntu, CoreOS). So currently this driver expects a CoreOS template. Other templates may also work, but CoreOS templates are currently the only ones that are properly tested. To build a CoreOS template for your CloudStack installation, follow these simple steps:

- Download the latest CoreOS ISO and upload it to CS as an ISO for OS type `Other (64-bit)`
- Create and boot a new VM with the uploaded ISO set as it’s bootable media
- After it's booted open the console and install CoreOS to disk using the command: `sudo coreos-install -d /dev/xvda -C stable -o cloudstack`
- Once done, shutdown the newly created VM and create a template from it’s root volume. Again make sure you set the OS type to `Other (64-bit)`

Options :

 - `--cloudstack-api-url`: The API endpoint of your CloudStack environment.  Default: $CLOUDSTACK_API_URL
 - `--cloudstack-api-key`: Your CloudStack API key.  Default: CLOUDSTACK_API_KEY
 - `--cloudstack-secret-key`: Your CloudStack secret key.  Default: CLOUDSTACK_SECRET_KEY
 - `--cloudstack-no-public-ip`: Whether or not this machine is behind a public IP. Helpfull when having direct access to the IP addresses assigned by DHCP.
 - `--cloudstack-public-ip`: The public IP used to connect to the machine.
 - `--cloudstack-public-port`: The public port to open/forward to connect to Docker, if empty it matches the private port.
 - `--cloudstack-public-ssh-port`: The public port to open/forward to connect to SSH.  Default: `22`
 - `--cloudstack-private-port`: The private port for Docker to listen on.  Default: `2376`
 - `--cloudstack-source-cidr`: The source CIDR block to give access to the machine.  Default: `0.0.0.0/0`
 - `--cloudstack-explunge`: Whether or not to explunge the machine upon removal.
 - `--cloudstack-template`: The name of the template to use.
 - `--cloudstack-offering`: The name of the service offering to use.
 - `--cloudstack-network`: The network to attach to the machine. Leave empty when using a basic zone.
 - `--cloudstack-zone`: The zone in which to create the machine.

Example :

    $ machine create -d cloudstack --cloudstack-template="CoreOS 557.0.0" --cloudstack-offering="some offering" --cloudstack-public-ip="x.x.x.x" --cloudstack-zone="zone-1" dev

## Contributing

Want to hack on Machine? Please start with the [Contributing Guide](https://github.com/docker/machine/blob/master/CONTRIBUTING.md).

