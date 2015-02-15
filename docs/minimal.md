# Minimal Initial Implementation

In the first iteration of Citadel, we need to leave out much of the planned feature
load, while still generating a game worth playing.

- No Boosts/Talents
- One dungeon
- No dungeon construction
- No First-Person view
- no zoom
- No combat highlighting
- All spell effects look similar, and are colored by the source.
- no replays
- 3 Classes called 'Tank', 'Healer', 'Damage', with two abilities each.
- Only two monsters: 'Minion' and 'Boss'
- No equipment

## What it *does* include

That's a list of features to pare off, but it is valuable to consider instead what
the game will be like at that level of implementation.

The map will be a bit boring - groups of 2-6 minions, a few boss fights, and at the end
a fight with two boss monsters at once. The lack of equipment means that there is no
progression - this is purely a proof-of-concept, validating the essential gameplay.

## User Story

A user would log onto the application, look in the runs list to see what type of character
seems to be in demand, and would then go create a character of that type (with a new unique
name). Then they would click to join one of those pending games, probably one lacking only
a single player with the role they just created a character for (let's assume Damage).

Once the group is full, and all players confirm their readiness, the instance starts up and
the players connect to it. They start out in a small empty room, and can see their first
monster (a Minion) in another room after a short hallway. After they all see what their
abilities do, the Tank charges on ahead and starts beating on the Minion with his swoard,
occasionally taunting it to make sure it stays focused on him. The healer catches up a few
seconds later, in time to keep the Tank from ever getting to low health, and the Tank manages
to kill the Minion off before the Damage characters even arrive.


