# File Resuming

When deciding if you should automagicly resume a incoming existing file transfer, It's probably best to resume the
transfer without user interaction if the data related to the transfer is still in memory. In this case it was likely a
short network stall causing a disconnect, and it's likely to be the least interrupting this way. If you have information
about the incoming file, but you got that information from long term storage, (e.g. from the hard disk) you may want to
prompt the user before resuming. Tox file transfers use all of the available bandwidth and may cause bottlenecks that
would inconvenience users, if they're friend comes online at an inopportune time. But as always, it's up to however you
wish to handle file transfers.

## Client quirks
The following is a (likely non-exhaustive) list of clients that do something strange.

### uTox
uTox uses a different TOX_FILE_KIND for resumed transfers. As of notsecure/uTox@e6c346b4dd006b01293678ab0f38f91282298af9
`TOX_FILE_KIND_EXISTING` is defined as 3. This may change if Toxcore adopts this scheme, or if Toxcore adopts a different
one that conflicts. Currently uTox will not try to resume an existing transfer if `TOX_FILE_KIND` is set to
`TOX_FILE_KIND_DATA` and will instead just treat it as new.
