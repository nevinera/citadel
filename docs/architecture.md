# Architecture

There are roughly four components to this game:

1. A Rails server that serves the assets, provides authentication, and allows visibility
   into the data (equipment, achievments, etc). It uses mysql as the data store, and serves
   a bunch of normal rails pages for admin areas and one single-page js app for players.

2. A chat server - we'll use a simple pubsub server for managing chat through websockets; I
   can either implement it myself in Go, or use one of the many existing solutions to the
   problem. It will be hosted on a different port from the rails application, and will be
   used purely for passing messages to js clients for the various chats and logs.

3. An instance server - this is a Go process that gets spun up for each dungeon instance
   that is occupied. It will listen on some port on the same system, and the client will be
   told by the rails application where it is, and what nonce to use to authenticate into it.
   This server communicates purely via websockets - one downstream socket for receiving
   instance state and state-deltas, and one upstream socket for sending unit commands up to
   the server. The server dumps its 'state' between ticks every 10 seconds when not in combat,
   allowing for crash recovery of an in-progress run.

4. A single-page javascript application that functions as the game client, including all of
   the rendering logic and the interface controls. Not sure what framework to use here,
   but I'm not sure it matters - all of the complex interface elements will be rendered into
   a canvas anyway.

## Limitations

The go process needs to be able to access the mysql db to see what equipment the player
owns (the player can be wearing anything that he owns that can be worn by his class).

It needs to be able to write achievement information and new loot into the db as well -
I'm a little concerned about this shared-db approach to managing data. Another possibility
is to have it perform the writes by using the rails application as a json api on localhost?

I am not terribly proficient with javascript, and the bulk of the complexity of the game will
need to be implemented in that layer. Hopefully I'll learn something!

## Angle of Approach

I'm going to start by building the rails application, of course - minimal models to support
the MVP:

- `Player` - represents a user that can log in to play
- `Hero` - one of the avatars of a player - this is the thing that can be a wizard or priest.
- `Dungeon` - encodes the geometry of the dungeon, unit information, and the role reqs
- `Instance` - an instance *of* a dungeon, and represents a go process.
- `ChatMessage` - while we use pubsub to send messages in realtime, we also persist them
  so that you get a context to the chat you see when you log in. Retained for a day or so.

Once you can log in and at least view all of those resources, I'll add the ability to create
heroes, sign up players, and spawn instances of dungeons (not that they'll do anything).

Next comes the chat server/service, and functioning chat with websockets.

Then we write the framework on which to hang the UI - non functional, with with boxes for
all of the screen elements we intend to be updated regularly.

Then we build the instance server, except it sends stub messages consisting of all commands
issued in the last 5 seconds (the delta messages just contain the deltas).

Next we implement dungeons as a data model, and figure out how to load it into the instance
process. The instance server now serves that as its state (all deltas are empty).

Next we implement the commands as a syntax and have the server receive them.

Then we implement a few basic commands: "move to (x,y)" and "stop now" seem like good choices.

