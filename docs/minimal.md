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

### Authentication

TODO

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

- `auth`: The path to an authentication text file as described earlier.
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
