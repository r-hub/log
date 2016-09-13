---
title: Fighting with Jenkins
date: 2016-07-04
---

### Streaming output from Jenkins

Seems like Jenkins does not drop TCP connections coming for the builder web app to stream the console output. What is even worse, it keeps a thread for each connection, and gets really slow already around ~20 threads. (What the hell is it doing? It should be just listening on a socket....)

Anyway, this needs to be fixed: https://github.com/r-hub/rhub-frontend/issues/8

### `cron` app dies sometimes

```
2016-07-04T10:45:10.973610931Z app[web.1]: > rhub-cron@1.0.0 start /app
2016-07-04T10:45:10.973617561Z app[web.1]: > node ./bin/scheduler
2016-07-04T10:45:10.973623287Z app[web.1]:
2016-07-04T11:42:42.620726576Z app[web.1]: /app/index.js:33
2016-07-04T11:42:42.620802676Z app[web.1]: 	jen.build.get(job.name, data.lastBuild.number, function(err, data) {
2016-07-04T11:42:42.620850863Z app[web.1]: 	                                      ^
2016-07-04T11:42:42.620885526Z app[web.1]:
2016-07-04T11:42:42.620919177Z app[web.1]: TypeError: Cannot read property 'number' of null
2016-07-04T11:42:42.620927075Z app[web.1]:     at /app/index.js:33:40
2016-07-04T11:42:42.620932178Z app[web.1]:     at next (/app/node_modules/papi/lib/client.js:292:32)
2016-07-04T11:42:42.620946196Z app[web.1]:     at Jenkins.body (/app/node_modules/jenkins/lib/middleware.js:14:3)
2016-07-04T11:42:42.620950958Z app[web.1]:     at next (/app/node_modules/papi/lib/client.js:298:10)
2016-07-04T11:42:42.620955722Z app[web.1]:     at Jenkins.<anonymous> (/app/node_modules/jenkins/lib/middleware.js:88:5)
2016-07-04T11:42:42.620960668Z app[web.1]:     at next (/app/node_modules/papi/lib/client.js:298:10)
2016-07-04T11:42:42.620965208Z app[web.1]:     at Jenkins._onResponse (/app/node_modules/jenkins/lib/jenkins.js:109:3)
2016-07-04T11:42:42.620981759Z app[web.1]:     at next (/app/node_modules/papi/lib/client.js:298:10)
2016-07-04T11:42:42.620988281Z app[web.1]:     at IncomingMessage.<anonymous> (/app/node_modules/papi/lib/client.js:598:7)
2016-07-04T11:42:42.621025915Z app[web.1]:     at emitNone (events.js:85:20)
```

https://github.com/r-hub/rhub-cron/issues/1

### Upload storage, dokku upgrade

Uploads must be moved out of the container, because if I update the web app, they are deleted. Dokku makes this easy with the new storage plugin (http://dokku.viewdocs.io/dokku/dokku-storage/), but for this I had to upgrade dokku to 0.6.4.

It all seems to work fine, except that every time I redeploy the frontend, I usually need to redeploy the backend as well....

Closes https://github.com/r-hub/rhub-frontend/issues/7

### Periodically building the docker images

For the devel R versions at least. I cannot do this on Docker Hub, because I have multiple images in a single repo (I really do not want 20 repos for 20 images.) I also cannot do it on Travis, because with 6 images I already hit the 50 minutes time limit.

So I'll do it on the Jenkins CI on Azure (cran.r-hub.io). It takes about 2 hours to build them now. I'll do them once a week (Sunday evening), and also whenever the repo with the Dockerfiles changes. I have added a hook for this. The job pushes the images to Docker Hub.

I still need to have a way to pull them to the worker nodes, I guess that'll be just another Jenkins job on the workers. This one can run every hour, if there are no new images, then it is quick. Also, just one job is enough, just need to configure it to run on all workers.

Apparently, I need the `Elastic Axis plugin` for this. I run the pulls sequentially, so that the worker nodes don't update at the same time.

### Use a single keepalive job

Instead of one per worker. Now that I have found the `Elastic Axis plugin`.

### Test builder web app

Is at http://builder-test.r-hub.io. Note the `http`, letsencrypt seems to be down, so I could not get a certificate. This has a different redirect URL, so it needs a separate GitHub application.

### Alternative email address

https://github.com/r-hub/rhub-frontend/issues/9 The test builder uses this now.
