---
title: Fighting with Jenkins
---

## Streaming output from Jenkins

Seems like Jenkins does not drop TCP connections coming for the builder web
app to stream the console output. What is even worse, it keeps a thread for
each connection, and gets really slow already around ~20 threads. (What the hell
is it doing? It should be just listening on a socket....)

Anyway, this needs to be fixed: https://github.com/r-hub/rhub-frontend/issues/8

