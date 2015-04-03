Clients must support file resuming even across client restarts.

File transfers for a specific friend are purged from core as soon as that 
friend goes offline. They are also not saved in the core data file. This means 
that clients must take care of resuming file transfers using the file_id 
parameter and seek function.

A client sending a file will create a new file transfer (it is recommended that 
the file_id parameter is set to NULL so that a random file_id is generated for 
the new transfer but the client may choose to generate one itself).

The sending client will save the file_id (tox_file_get_file_id()) along with 
file location and any other information that is needed to resend the file. This 
information must be saved in long term memory (like the tox save) so that its 
contents can be loaded if the client suddenly closes.

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
