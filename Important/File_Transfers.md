# File Transfers
The ability to send and receive files is a required feature for any useful instant messaging client. Toxcore will handle
all of your data transfer needs. All you have to do is provide the data, and any helpful information to pass along to
your friend.

At the very least, you'll need to give Toxcore
 * The friend you want to send to
 * The kind of data you want to send.
 * The size of data you plan to send.
 * A name for the file you're sending, along with the length of the name string (don't include the NULL term in the
 length, but do include one!)
Optionally, you can also provide a file_id, and an int pointer if you'd like an error code if something goes wrong. The
API function looks like this `tox_file_send(tox, friend_number, TOX_FILE_KIND_DATA, file_size, file_id, filename,
filename_length, error);`

If the file transfer completes, the client may delete all stored info about the file. A file completes when the receiver
has received it completely as such there is no need to try resuming completed files so any other info can be deleted, at
the clients discretion. Toxcore will make one final callback to the chuck request function with a length of 0. At that
point Toxcore considers the file completed, and purges any information about the file transfer and will reuse the file
transfer number.

## File Resuming
Good clients support file resuming. Great clients do so even across client/core restarts!

Toxcore doesn't keep data that might have expired. So File transfers for a specific friend are purged from core whenever
that friend goes offline/disconnects. Toxcore also makes no attempt to save file transfer info in the core data file.
This means that it's the sole responsibility of the client to index, save, and restore file transfer information.
Clients must take care of resuming file transfers using the same `file_id` parameter and make use of the `seek api` to
avoid duplicating transfered data. If you don't want to create your own file_id, Toxcore will generate one for you that you can
retrieve with `tox_file_get_file_id(tox, friend_number, file_number, new_id, error);`. You're always welcome to supply a
file_id yourself, but the general recommendation is to let Toxcore generate one for you. (You can do this by setting
file_id to NULL).

When you're ready to resume a broken/interrupted transfer, you'll need to start the transfer from the original sending
client. The sending client will save the file_id (tox_file_get_file_id()) along with file data/location, and any other
useful information that your client may need to restart the file. Again, this information must be saved in long term by
the client, because Toxcore won't save anything for you.

If the client is closed and reopened it will reload all the saved file info from disk.

The expectation of clients is that every time a friend comes online, the client will restart any pending transfers.
Restarting a transfer is the same as starting a new transfer with the exception of the `file_id` parameter. For resumed
transfers it **MUST NOT BE NULL**! Again Toxcore has no information about this file and will generate a NEW and UNIQUE
`file_id` one that will not match the original. If the `file_id` changes the receiving client will have no means of
differentiating it from a new transfer.

Complient clients will first check if it has any incomplete files with the same `file_id`. If it does, it will open the
incomplete file and look at how much data was received already, and use
`tox_file_seek(tox, friend_number, file_number, size_transferred, error)` with the 4th parameter equal to the number of
bytes already received. It will then call
`tox_file_control(tox, friend_number, file_number, TOX_FILE_CONTROL_ACCEPT, error)` to accept the transfer. It's up to
your client if you want to prompt the user to RE-accept the transfer, or just resume the transfer automatically for the
user. See also [Resuming Suggestions](Suggestions/Resuming.md).

When a receiving file completes, all stored info related to the file transfer can, and should be deleted, (not the file
transfer data it self, only the data associated with transferring that data). Doing so will prevent future conflicts
new/different transfers.
