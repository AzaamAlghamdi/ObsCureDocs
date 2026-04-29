# Items

Items are objects scattered around the rooms of ObsCure, that can be picked up
to add them to our inventories. Some are unique objects that are usually used
for specific puzzles, like wheels or keys, while others are resources that help
us survive during the whole adventure, like medkits or ammo. Let's explain how
items work in each game.

## ObsCure
All items have a type and a UID, the type of an item answers the question "What
is it?". Is it a medkit? Is it a key? Is it a map? Is it a disc? The type will
determine what we can do with the item, like healing a character, opening a
door, picking a lock, etc. The UID answers the question "Which one is it?". It
is a unique number that identifies a specific object among all items that can be
picked up in the game.

Let's take two different medkits as an example. On the one hand, the UID of the
medkit in the room of the first queen is `0x063282`, and its type is `0x025F`,
which corresponds to the medkit type. On the other hand, the UID of the one that
can be found in the bathroom of the ground floor of the classrooms building is
`0x030181`, and its type is also `0x025F`. As you can see, both medkits have the
same type, but different unique identifiers.

For an item to appear in a room, we need to define it in two different spots:
the `allitems.it` file, and the `.tm` file of the room. Some data like the item
type or its UID appear in both files, so the information given in said files
must be coherent.

### allitems.it
This file contains information on every single item in the game. For each one of
them, it gives its type, its UID, some additional information that is only used
for specific item types, a multiplier that is only truly used for ammunition,
and the difficulties or modes in which the item can be found. Check
[this link](/formats/#it) to learn about the format used in this file.

It doesn't specify, for example, the room in which the item can be found, as
this is just a kind of catalog of items.

### .tm files
Each room has at least one `.tm` file, which gives information such as the
camera angles, the NPCs, and the items that can be picked up inside. Check
[this link](/formats/#tm) to learn about this file format.

### Hidden items
Hidden items are handled as an event, in the `.hoe` files, which store (among
other things) scripts/bytecode. Some functions are called in order to hide the
items, and they are given some [ITEM_TYPE_SHORT](/constants/#item_type_short)
and some Item UIDs as arguments so that they know which specific items are being
hidden and what their types are.

## ObsCure II
WIP