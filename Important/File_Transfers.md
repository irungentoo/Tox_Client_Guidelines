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

## File Resuming
Good clients support file resuming. Great clients do so even across client/core restarts!

Toxcore doesn't keep data that might have expired. So File transfers for a specific friend are purged from core whenever
that friend goes offline/disconnects. Toxcore also makes no attempt to save file transfer info in the core data file.
This means that it's the sole responsibility of the client to index, save, and restore file transfer information.
Clients must take care of resuming file transfers using the same `file_id` parameter and make use of the `seek api` to
avoid duplicating data. If you don't want to create your own file_id, Toxcore will generate one for you that you can
retrieve with `tox_file_get_file_id(tox, friend_number, file_number, new_id, error);`. You're always welcome to supply a
file_id yourself, but the general recommendation is to let Toxcore generate one for you. You can do this by setting
file_id to NULL.

When you're ready to resume a broken/interrupted transfer, you'll need to start the transfer from the original sending
client. The sending client will save the file_id (tox_file_get_file_id()) along with file data/location, and any other
useful information that your client may need to restart the file. Again, this information must be saved in long term by
the client, because Toxcore won't save anything for you.

If the file transfer completes, the client should delete all stored info about
the file. A file completes when the receiver has received it completely as such
there is no need to try resuming completed files so the info can be deleted.

If the client is closed and reopened it will reload all the saved file info
from disk.

Every time a friend goes online, the client will resend all saved sending files
associated to the friend. This is done by creating new sending files with the
saved file_id as a parameter.

Every time a new file request is accepted by the client it will save the file
information that is needed to resume the received file (file_id, file location,
etc..).

Every time a file request is received by the client it will check if it has any
incomplete received files with that file_id. If it does, it will open the
incomplete file and look at how much data was received already. It will then
use: tox_file_seek() with the position parameter corresponding to the number of
bytes already received. It will then call tox_file_control() with
TOX_FILE_CONTROL_RESUME to accept the transfer. If no incomplete files with
that file_id is found, it will be treated like a new transfer.

When a receiving file completes, all stored info related to the file should be
deleted.
