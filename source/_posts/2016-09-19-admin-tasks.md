---
title: Some regular admin tasks to start the day
date: 2016-09-19
---

Yuuuhooooo! Working on r-hub again!

## OS updates

First of all, I ran `apt-get update; apt-get upgrade` etc. on the various hosts:
* `r-hub.io`
* `ci.r-hub.io`
* Linux builder 1
* Linux builder 2
* `cran.r-hub.io`
* `monitor.r-hub.io`
* `install-github.me`
* `jenkins.r-pkg.org`
* `seer.r-pkg.org`
* `crandb.r-pkg.org`
* `docs.r-pkg.org`
* `www.r-pkg.org`

AWS changed the IP address of `ci.r-hub.io`, because I stopped it. Hmmm, it really should not do that. I had to change it in the DNS.

## Running out of disk

The disk of `cran-r-hub.io` was filling up. This is the machine that builds the Docker images for the builders, so it needs more disk as we are adding more images. It is easy to resize the disk on Azure, just:
* shut down the machine,
* *stop it* from the Azure web portal (wait for it...),
* resize the disk on the Azure web portal,
* start the machine.
Resized to 100GiB now, this should be enough for a lot
of images.

## SSL certificates

Next, I updated the SSL certificates for
* `ci.r-hub.io`
* `cranlogs.r-pkg.org`

## Monitoring

I added monitoring to the following machine:
* `cran.r-hub.io`

The following are monitored:
* whether the machine is up,
* disk space,
* cpu load

For Linux machines, I had to run this. Add repos and install packages first:
```sh
wget -q https://sensu.global.ssl.fastly.net/apt/pubkey.gpg -O- | sudo apt-key add -
echo "deb https://sensu.global.ssl.fastly.net/apt sensu main" | sudo tee /etc/apt/sources.list.d/sensu.list
sudo apt-get update
sudo apt-get install sensu
```

Put the following into `/etc/sensu/conf.d/rabbitmq.json`:
```json
{
  "rabbitmq": {
    "ssl": {
      "cert_chain_file": "/etc/sensu/ssl/cert.pem",
      "private_key_file": "/etc/sensu/ssl/key.pem"
    },
    "host": "monitor.r-hub.io",
    "port": 5671,
    "vhost": "/sensu",
    "user": "sensu",
    "password": "<omitted>"
  }
}
```

Copy the Sensu certificate and the private key from the monitoring server to `/etc/sensu/ssl/cert.pem` and `/etc/sensu/ssl/key.pem`:
```sh
scp ubuntu@monitor.r-hub.io:/etc/sensu/ssl/cert.pem .
scp ubuntu@monitor.r-hub.io:/etc/sensu/ssl/key.pem .
...
```
Make sure they are owned by `sensu:sensu`.

Put this into `/etc/sensu/conf.d/client.json`:
```
{
  "client": {
    "name": "cran.r-hub.io",
    "address": "13.88.23.239",
    "subscriptions": [ "common" ]
  }
}
```

Enable sensu on boot, start the client:
```sh
sudo update-rc.d sensu-client defaults
sudo /etc/init.d/sensu-client start
```

Enable the client in the AWS firewall, in the `sensu dashboard` security group.
