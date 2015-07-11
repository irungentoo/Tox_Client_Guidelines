Clients should bootstrap agressively to at least 4 random nodes from a list every ~5 seconds until toxcore reports that it is connected.

When loading from a save, you do not need to bootstrap unless ~10 seconds after running the first tox_iterate() toxcore still doesn't report that it is connected. If that happens refer to the above strategy. 

A list of nodes can be found: https://wiki.tox.chat/users/nodes
