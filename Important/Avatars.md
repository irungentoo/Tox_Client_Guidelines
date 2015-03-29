Avatars are PNG images with a maximum size of (2^16) bytes (client enforced).

Currently avatars are sent as a normal file transfer with kind avatar. Every
time a friend goes online you must send them a new avatar transfer. The file_id 
will be set to the hash of the avatar (If no avatar is set, the size of the 
avatar will be set to zero and the file_id will be set to NULL). If the avatar
transfer is canceled by the friend, the client will assume the friend already
has the avatar and will not try to send it again (unless the friend goes 
offline and back online).

If the avatar is changed a new avatar transfer will be started to all online 
friends.

If the client receives a new file transfer with type avatar, he will first 
check if the size is 0. If the size is 0 he will cancel the file transfer and 
remove the avatar of the friend if it was set. If the size is non zero, the 
client will check if the hash of the current avatar he has for the friend 
matches the file_id for the incoming avatar transfer. If it matches, the 
client will cancel the transfer. If it doesn't, the client will accept the 
transfer.


It is desirable that the user avatar and the cached friends avatars could be
shared among different Tox clients in the same system. This not only makes 
switching from one client to another easier, but also minimizes the need of 
data transfers, as avatars already downloaded by other clients can be reused.

  - Avatars are stored in a directory called "avatars" and named
    as "xxxxx.png", where "xxxxx" is the complete public key (but not friend
    address!) encoded as an uppercase hexadecimal string and "png" is the
    extension for the PNG avatar. As new image formats may be used in the
    future, clients should ensure no other file "xxxxx.*" exists. No file
    should be kept for a user who has no avatar.

  - The client's own avatar is not special and is stored like any other. This
    is partially for simplicity, and partially in anticipation of profiles.

  - The avatar should be stored as its received, before any modifications by
    the client for display purposes.


Example for Linux and other Unix systems, assuming an user called "gildor":

    Tox data directory: /home/gildor/.config/tox/
    Tox data file:      /home/gildor/.config/tox/tox_save
    Avatar data dir:    /home/gildor/.config/tox/avatars/
    Gildor's avatar:    /home/gildor/.config/tox/avatars/446F4E6F744D6564646C65496E546865416666616972734F6657697A61726473.png
    Elrond's avatar:    /home/gildor/.config/tox/avatars/43656C65627269616E20646F6E277420546F782E426164206D656D6F72696573.png
    Elladan's avatar:   /home/gildor/.config/tox/avatars/49486174655768656E48756D616E735468696E6B49416D4D7942726F74686572.png
    Elrohir's avatar    /home/gildor/.config/tox/avatars/726568746F7242794D6D41496B6E696854736E616D75486E6568576574614849.png
