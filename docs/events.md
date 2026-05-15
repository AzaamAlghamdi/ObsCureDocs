# Events

Events are things that can happen in rooms, but not necessarily in
all rooms. 

## ObsCure

In ObsCure, events are defined in `.hoe` files, because HOE is the AI engine
made by Hydravision Entertainment. Things like cutscene triggers, hidden items,
and even monsters, are considered events and therefore are defined in `.hoe`
files.

### Monsters

In order to have a monster in a room, the `.hoe` file of the room needs a BH
event (for example, "hommbh", which is the BH event of the Treeman or Homme
Arbre), a Main event (for example, "hommmain", but I think it doesn't need to be
called like that), and at least one HOE_Instance chunk. The two events give
general information on Treemen, for example all their AI behaviour is defined
via the scripts in the events. The HOE_Instance gives information on one
specific monster (or "instance" of the monster), like the coordinates along the
X, Y and Z axes. So, if you want three Treemen in a room, you need a BH section,
a Main section, and three HOE_Instances (one instance per Treeman).

The exact format of these structures is unknown, but you can check what has
already been reversed in the [formats section](formats.md/#hoe-obscure).

### Hidden items

Hidden items are items that can only be picked up under certain circumstances.
For example, you can only pick up the energy drinks in the room of the house
and the iron door (in the gardens, room a003) if you have completed the prologue
of the game with Kenny. And you can only pick up the medkit on the seat of the
sick bay if you free Kenny from the cell first.

In order to hide the items, the scripts in the `.hoe` files call some functions
that make them invisible and unpickable, and they give them at least two
arguments: the UID of the item, and the ITEM_TYPE_SHORT, by making reference
to the HOE_Vars.