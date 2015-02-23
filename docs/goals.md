# Goals

This document describes a bunch of minor versions, goals on the way to building a
working game. They are grouped based on the technology they'll help/require me to
understand in order to accomplish them.

## Websockets

First thing's first - we're building a websocket-based game, and communication
protocols are fundamental.

### Single Socket (0.0.1)

We need a static page showing a single number, and a websocket handler that just
sends the numbers from 1 -> 10 in a loop (waiting one second each time). The page
should load and just start showing numbers.

### Multiple sockets+ (0.0.2)

Now we need two copies of the previous step on a single page, and each count
should start at 1. This will require us to manage goroutines and websockets on the
server end.

### More State (0.0.3)

This iteration has the server sending down a 5x5 grid of data (25 numbers) and two
positions (two numbers each), like `[1,2,3,4,5,...,1] (2,3) (1,1)`. The client page
will display the grid in a table, and will highlight the first position with red
and the second position with blue. Each iteration, both points will move in a random
direction, whil staying inside the grid.

### Commands (0.0.4)

On this iteration, the static page needs to send commands to the server over its
sockets. Each grid will have four buttons below it for 'up', 'down', 'left', 'right'.
Upon clicking any of those buttons, a corresponding 'command' will be sent to the
server via that grid's websocket. Commands look like `BLUE: go up`, `RED: go left`.
The server will attempt the moves sent before the current iteration, and will include
in its downstream to each player whether any of their attempted moves failed last
loop.

