---
title: More admin tasks and dealing with Azure
date: 2016-09-20
---

## Sensu plugins on clients

OK, for the Sensu addition yesterday it turns out that two Sensu
plugins are also needed on clients:

```sh
sensu-install -p cpu-checks
sensu-install -p disk-checks
```

and then the new Sensu client is all green.

## Getting rid of a Sensu check

This is surprisingly difficult. Yesterday I switched `cranlogs.r-pkg.org` to HTTPS, after getting a new certificate for it. It seems to work fine now, I don't know what was the problem last time (https://github.com/metacran/cranlogs.app/issues/22), but it works fine today.

I also updated the Sensu checks for `cranlogs`, to check HTTPS and also the HTTP -> HTTPS redirect. It seems that to get rid of the old check, one has to restart the Sensu server and the client as well, both running on `monitor.r-hub.io` in this case.

Also, one needs to put the port number into the check. Even if the nginx redirect header does not have it: `https://cranlogs.r-pkg.org:443/downloads/daily/last-week/igraph`

## Azure disk attach/detach

This seems to a straightforwars enough operation, but it is not. Make sure that you close the browser tab, and open a new one to Azure, after each detach/attach operation, because the UI does not refresh the state of the disk. It is actually the same for the state of the virtual machine, you click on Stop, it stops (after some time), but the state is never updated, until a new browser UI window is used.

I wonder who thought this was a good idea. It only took me ~4 hours to solve this, thank you.
