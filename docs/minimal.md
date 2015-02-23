# Architecture

We are going to design the simplest architecture that will work for now,
so we can get to the meat of building the game.

There is not central chat room, and the server will host only a single game -
configuring that game is part of setting up the server.

We'll be using websocketd as our server software - it has some killer features
for our approach:

1. Can server static files from a directory on http
2. Serves websockets by spawning a process from the command line
3. Socket handling process is a pure cli stream processor

This makes development *much* simpler - the game and its logic will all be implemented
in a single c (or go) program that reads commands from stdin (each command specifies
who sent it) and writes game state out to stdout.

## Components

- websocketd to serve
- static site including the js to display game state and receive commands
- player program (one per connected player) that websocketd forks off
- game program (single process) which the player programs communicate with that
  tracks/interprets/computes game state.
- atomic-tailer that listens to game program and keeps a file atomically updated
  with the current game state, so that player programs can just read it out at
  regular intervals.

### Authentication

There is a directory that contains a tokens file containing K lines, each of which
has nothing but a token in it, and also possibly containing files named using the
tokens. When citp is spun up for a new socket connection, it will expect to receive
a `token` parameter containing some token in that file. If the token is not listed
in the tokens file, then it will close the connection and terminate; otherwise it
will remember the token and touch the file for that token with every up-message.

The static site will have basic-auth enabled,

### Static Client Site

This will probably be the most difficult for me to build. We will probably want to use
some kind of static site generator, so I can do the actual building in slim, but the
bulk of the code here will be about

- receiving and constructing commands
- constructing the command messages for the socket
- parsing the game state from the socket
- rendering the game state onto the screen
- animating transitions from state to state smoothly

The rendering will be onto html5 canvas - there probably will not be a lot of styling
involved in this iteration at all.

### Player Program

This program will be forked off by websocketd once per connection. It contains no
game logic - it's purely about managing communication with the game program itself.
To that end, it will select among the websocket up stream and the game-state fifo,
writing any command message received from the socket into the commands fifo (up to
a limit) and writing any game state messages received into the socket down-stream.

It will actually have some additional functionality for debugging purposes - it can
be started with the paths to five files, into which it will log everything it reads,
writes, and does: log, command-recv, state-send.

This program should be fast, but could otherwise be almost anything. For now we'll
assume I'm writing it in C. It will be called 'citp', and its options are:

- `auth`: The path to an authentication directory as described earlier.
- `log`: This is where to log warnings, errors, etc. STDERR by default.
- `input-log`, `output-log`: Passing these causes citp to log the relevant stream
  into the supplied path.

### Game Program

This process will be running ahead of time, started at the same time as websocketd.
It is a pure stream processor on an time-locked loop. Every N milliseconds it will:

1. resolve all movement and actions from the previous frame, building an instantaneous
   game state.
2. For each ai unit, decide on an action to take, issue the appropriate commands
3. read all of the commands from the fifo and integrate that list.
4. turn all of those commands into behaviors that will definitely be ocurring between
   this frame and next.
5. construct a dynamic game state including the static game state and the behaviors
6. write that state out as a single line

I hope that I can make this process fast enough that the time between frames is
dominated by waiting - if that is not so then we may need to introduce some additional
steps to sync up iterations better. In particular, if the AI phase is too slow, we may
move that to the end of the loop, and allow the monsters to react one frame slower than
players do.

### Atomic Tailer

This process listens to the fifo or stream coming out of citd, and either (a) keeps a
single file atomically updated to the most recent line of output from citd, or
(b) writes new files with a systematic numbering scheme, and cleans them up after some
number of generations.

The point of the latter (more complicated) approach is that it allows for synchronization
between citp and citd on the state stream - instead of sending out state every N ms,
even if citd hasn't finished building it (or sending out state whenever citd finished
building), we can write state into a file that will stick around until well after citp
should have read and used it, and allows citp to decide when exactly that will happen.

## Message Representation

Because `websocketd` uses newlines to separate messages in both directions, we'll need
to construct a message representation that:

- uses no newlines
- has a consistent (or at least predictable) size
- does not require a state-based parser (like json)


### Command Representation

TODO

### State Representation

TODO
