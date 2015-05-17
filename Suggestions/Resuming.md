# File Resuming

When deciding if you should automagicly resume a incoming existing file transfer, It's probably best to resume the
transfer without user interaction if the data related to the transfer is still in memory. In this case it was likely a
short network stall causing a disconnect, and it's likely to be the least interrupting this way. If you have information
about the incoming file, but you got that information from long term storage, (e.g. from the hard disk) you may want to
prompt the user before resuming. Tox file transfers use all of the available bandwidth and may cause bottlenecks that
would inconvenience users, if they're friend comes online at an inopportune time. But as always, it's up to however you
wish to handle file transfers.
