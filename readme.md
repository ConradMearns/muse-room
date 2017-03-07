# Purpose
muse-room is a static application with volatile transactional databases that uses [PeerJS](http://peerjs.com) to create music playlists. It's a proof of concept for myself as well as a way to challenge myself. It is a single web app that can be delivered by an HTTP(S) request, and function without a backend. To save and store data about playlists, muse-room stores information in encrypted cookies, and updates whenever a P2P connection is made.

# How Does it Work?
Upon opening muse-room for the first time, the application will acquire a new peer id from a [PeerJS](http://peerjs.com) server. The user is prompted for a password that they will be required to remember. This password acts as a key to encrypt and decrypt cookie data, preventing it from being 'easily' read by other applications. The user now has a music room that they can send Youtube, Soundcloud, and possibly other songs to. The player will pull up embedded versions of the media from the respective host. These songs are saved as comma separated values (CSV) according to the users peer ID. Songs can be upvoted and downvoted, which changes when and how frequently they play.

Others can join the room using the peer ID, which for now will require typing the value. Once the peer ID is entered into the application (for joining a room), the new client requests the playlist. The two clients now have the same peer id associated with the same playlist in their data cookie and are synched.

Because the connection can only be synched when all clients are connected, data can misalign across devices because there is no centralized server running 24/7 to broker data. Instead, each client holds a copy that they can modify when they please. Upon reconnecting with peers, the playlist is hashed and sent to all connected peers. Peers handle the data individually, so conflicts are resolved by connection status (but mostly through upvotes and downvotes). Conflicts can be avoided all together if at least one device is running the application, as P2P actions will be sent across the network whenever possible.

# Concerns
Cookies are always concerning. This application is more of a proof of concept more than anything else though, and because I plan on developing it as a web app, cookies just make sense. Of course, this also means that the app stores playlists per device as cookies are not synched over browsers. Security is the biggest concern, but this project is unattractive in the quest for interconnectivity between many devices. The solution is just to add your other devices to the room.  

It's difficult to have true peer to peer networks, as [PeerJS](http://peerjs.com) uses a server to broker symbolic P2P connections.

# Project status
Lists and things to see where the project is currently.

## Breakdown of Connection Flow
1. Look for cookie
  - No cookie -> Initialize new PeerJS id and cookie
2. Ask for encryption key (user input)
  - Used for encrypting new cookies and decrypting existing cookies
3. Process cookie decrypted information
4. Show available playlists
  - device playlist is selected -> 5.
  - Alternate playlist is selected -> 6.
5. Load playlist and begin play cycle
  - Disconnect -> 4.
6. Attempt to connect to playlists owner id
  - Fail to connect -> 7.
  - Connected -> 5.
7. Change peer ID to match playlist's
  - -> 5.

## Cookie Data
- Device's peer id
- CSV of song locations (according to peer id's)
  - Title (Can be changed by user)
  - Source (Youtube, soundcloud, etc)
  - Upvote count per song
  - Downvote count per song
