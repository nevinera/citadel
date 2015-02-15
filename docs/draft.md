## Global Screen

- General chat
- Party chat
- Currently selected dungeon details
- available parties list
- new-party screen

This screen is for putting together a group, picking a dungeon level,
and finding players to join your run.

## Hero Screen

- Current/Available gear
- Armor sets, and filtering by source
- Stats (including current and lowest ilvl)
- Ability descriptions
- Hotbar construction
- Hotkey definitions

## Official levels

There is one set of 'official' levels - all gear is tagged with
the dungeon that sourced it, and levels can require gear that is sourced
from particular other levels, allowing there to be multiple progression
chains set up (and allowing the official dungeons to exclude gear from
fake lootparty dungeons).

Users can *create* dungeons as well - the gear from them won't be eligible
in my progression, but they can form their own progression, or simply
allow the official gear in theirs and create 'challenge' dungeons for
their friends and the public. Sufficiently well-developed chains of
dungeons can be annointed into an official campaign later.

## No levelling

Gear progression is all - dungeons may require details about your gear:

- has a specific item on
- has average ilvl > x
- has min ilvl > x

They may also require some other details of your character:

- has completed dungeon Yxx
- has gained achievement Q from dungeon Z

## Achievements

Levels have achievements - 'complete in < 15m', 'complete with no gear',
'complete without any deaths', 'complete with only two heroes', etc.

These achievements are specific to the dungeon - there are no higher level
achievements that span dungeons, like "do all of the x dungeons". Most
dungeons should have some hard-mode achievements that are fairly difficult
to acquire, and they should pretty much all have one like 'without dying'.

## Guilds

Community is the building block of the user experience - while the game itself will
function like a guild for a while (until there are a few thousand players), it is
preferable for players to be able to form their own tribes within which to organize
events and sessions. There is nothing formal about a guild except that it can have
achievements and gets its own chat. Guild achievements are progression - they represent
a given dungeon having been completed with *only* players from that guild present.

When hardmode achievements get added, they should be present here too.

## Graphics

The graphics on the map are abstract line drawings, with symbols
to represent characters (drawn symbols, no sigils as in roguelikes).

Character symbols have a systematic way of indicating facing (an arc on
the front side with a slight point). The walls and features of the map
are continuous lines.

The map has several levels of zoom, ranging from '5 character widths',
to 'I can see most of the map'. These can be adjusted on the fly, and the
map has some push-and-hold hotkeys that hide all friendlies, all foes,
and all units but yourself, to make it easier to rapidly figure out what's
going on.

## Dungeon Screen

Aside from the map, which takes up a good chunk in the center of the
screen, players can see (arranged around the map):

- unit frames for party, self, target
- chat box (with a tab for general)
- two rows of hotkeys
- a combat log
- Buffs and Debuffs on yourself
- Buffs and Debuffs on your target

## Monster AI

Some basic sets of behaviors will be defined for monsters, which will
allow them to act basically like normal mobs in an mmo act - attack any
player that comes within X distance of them, follow players to a max
distance and then leash, basic threat-table based on damage with added
threat for some/all tank abilities.

Other capabilities will be conveniently usable as well - triggers like
"use ability X once when health crosses 20%", "below 15% hp, ignore threat",
etc. A basic scripting solution will also be present eventually, but that's
a stretch goal.

## Classes

There are to be dozens of classes, each satisfying exactly one role:

- Warrior
- Wizard
- Cleric
- Ninja
- Bard
- Sorcerer
- Warlock
- Ranger
- Frost Elementalist
- Fire Elementalist
- etc.

Initially we will implement three 'stub' classes, called "Tank", "Healer", and "Damage" -
these classes will have very limited abilities that are intended to make the game workable
and testable without requiring any design - Healer will have a spammable ability that heals
all allies to full health, for example.

## Abilities

Abilities are the activatable skills players have available to use. They come in several forms

- affects self
- affects friendlies
- affects target
- affects region (cone, circle, line)

They have a color assigned to them, and they generally display as an animation. The ability
itself takes zero time to go off (instantaneous at the time of execution); the animations
should be very short to reflect that. It is probably best to keep the animations to clear
flashes, color washes, and moving edges; many of them may be going off at once. Additionally,
all ability effects must be *dimmable* - I intend that allies' abilities should be at least
optionally dimmed out so that they don't distract overly from enemy abilities and your own.

Many abilities will require line-of-sight (LOS) - if LOS is not present when the ability
executes, then it will fizzle, costing no resources and having no effect.

## Floating Combat Text

While full fct would obscure the display unbearably, we do need some indicators of who is
taking damage and who is receiving heals. A green or red halo around a unit indicates that
they have gained/lost life in the last 1 second, and it will be 1-3 pixels thick, depending
on how significant a chunk of life was added/removed. Optionally only your own dealt damage
and received healing will produce halos.

## Replay Mode

Since we are tracking full positional state and combat logs over time, we can allow for a
'replay mode' - it should not be unreasonably complicated to allow a player to download the
full run to local storage and step through it like a video. Obviously not a feature to be
implemented at launch ;-)

## Gear

Gear is fully controlled by its level - there are no 'epics'. Gear *can* be specialized to
a class, and types of armor are limited to subsets of the classes - a Wizard cannot wear plate,
and a Knight may not use a Robe. Some classes are limited as to which weapon types they can use
as well; wands are not for Rogues, swords are not for Priests, and so on.

Gear is itemized by the dungeon that specifies the gear - each item is only found in one
dungeon, though another item may have the same stats. (The names are not required to be unique,
but the official ones all will be. The stats about the weapon mention what dungeon it was
found in). Itemization may be partly or entirely random for a given item - if the item is
underspecified for its level, that means that the game will add random stats appropriate to
its target group to use the rest of its itemization points up.

Gear sets are intended; the set bonus(es) might be stat bonuses. Gear may also have
class-specific ability-affecting text on it, and the set bonuses for the 'tier' gear will all
affect class abilities in some way (but always by affecting some numeric quantity in some
ability).

## Boosts

Each class has five sets of three boosts to choose from; each set of three boosts should
contain three conceptually related choices that emphasize different playstyles. These can
be changed at any time when not in combat ("select boost 2 on rank 4" is a command that can
be issued).

