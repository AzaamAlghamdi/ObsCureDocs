# File formats

The "Size" column in each table should be interpreted in bytes, unless specified
otherwise.

The `Type[N]` notation represents a sequence of `N` structures of
type `Type`, but it doesn't necessarily mean that all `Type`s have the same
length. It just means that you have a `Type`, then another, and another, until
you reach `N` occurrences.

## IT
General structure of the `.it` format (everything is in MSB):

| Type       | Size              | Name  | Description                                 |
|------------|-------------------|-------|---------------------------------------------|
| IT_Item[X] | sizeof(IT_Item)*X | Items | Sequence of all pickable items in the game. |

The X represents any natural number, this format doesn't have a specific number
of minimal or maximal items. So this file is just a sequence of IT_Items.

Structure of IT_Item:

| Type       | Size | Name       | Description                                                |
|------------|------|------------|------------------------------------------------------------|
| ITEM_TYPE  | 4    | Type       | Type of item.                                              |
| uint32     | 4    | UID        | UID of item.                                               |
| EXTRA_INFO | 4    | Extra info | Specifies the document, photo, map or statuette if needed. |
| uint32     | 4    | Multiplier | Multiplier for the ammunition.                             |
| DIFF_MODE  | 4    | Diff mode  | Tells in which difficulty and mode the item shows up.      |

For more info on each type, check these links:

- [ITEM_TYPE](constants.md/#item_type)
- [EXTRA_INFO](constants.md/#extra_info)
- [DIFF_MODE](constants.md/#diff_mode)

## TM
General structure of the `.tm` format (everything is in MSB):

| Type          | Size                 | Name     | Description                  |
|---------------|----------------------|----------|------------------------------|
| uint32        | 4                    | ???      | ??? usually 0x05 or 0x04     |
| TM_Section[X] | sizeof(TM_Section)*X | Sections | All sections of the TM file. |

Structure of TM_Section:

| Type               | Size  | Name         | Description                                            |
|--------------------|-------|--------------|--------------------------------------------------------|
| TM_SECTION_TYPE    | 4     | Section type | Depending on this type, the content will be different. |
| uint32             | 4     | Size         | Size 'L' of the content, including these 4 bytes.      |
| TM_Generic_Content | L - 4 | Content      | Content of the section.                                |

TM_SECTION_TYPE can have the following values:

| Value | Meaning                 |
|-------|-------------------------|
| 0x04  | Door coordinates (?)    |
| 0x06  | NPC section (?)         |
| 0x07  | Description section (?) |
| 0x08  | Item section            |

TM_Generic_Content is just a union of these structures:

- TM_Coord_Content
- TM_Item_Content

Depending on the TM_SECTION_TYPE, the content must be interpreted in different
ways. For example, if TM_SECTION_TYPE is `0x08`, then the content of the section
must be interpreted as a TM_Item_Content structure. Here are the formats of each
of the previous structures.

### TM_Coord_Content:
| Type   | Size | Name    | Description                              |
|--------|------|---------|------------------------------------------|
| ???    | 4    | ???     | ???                                      |
| ???    | 4    | ???     | ???                                      |
| uint32 | 4    | X pos 1 | Coordinate along the X axis of player.   |
| uint32 | 4    | Y pos 1 | Coordinate along the Y axis of player.   |
| uint32 | 4    | Z pos 1 | Coordinate along the Z axis of player.   |
| float  | 4    | ???     | ???                                      |
| float  | 4    | ???     | ???                                      |
| ???    | 4    | ???     | ??? 0x00 00 00 00 ?                      |
| uint32 | 4    | X pos 2 | Coordinate along the X axis of teammate. |
| uint32 | 4    | Y pos 2 | Coordinate along the Y axis of teammate. |
| uint32 | 4    | Z pos 2 | Coordinate along the Z axis of teammate. |
| float  | 4    | ???     | ???                                      |
| float  | 4    | ???     | ???                                      |
| ???    | 4    | ???     | ??? 0x00 00 00 00 ?                      |

These TM_Coord_Content seem to hold info on the position and rotation of both
characters (player and teammate, or Player 1 and Player 2) when they enter the
room through a specific door. The first 2 fields probably specify the door.

### TM_Item_Content:

| Type      | Size | Name             | Description                                                                  |
|-----------|------|------------------|------------------------------------------------------------------------------|
| ITEM_TYPE | 4    | Type             | The type of the item. Same as in `allitems.it`.                              |
| uint32    | 4    | UID              | The UID of the item. Same as in `allitems.it`.                               |
| float     | 4    | X pos            | The position along the X axis.                                               |
| float     | 4    | Y pos            | The position along the Y axis.                                               |
| float     | 4    | X pos            | The position along the Z axis.                                               |
| float[9]  | 36   | Rotation         | The rotation of the item, a rotation matrix encoded as an array of 9 floats. |
| uint32    | 4    | Item info length | The length of the following uint8 array. The value is L2.                    |
| uint8[L2] | L2   | Item info        | Gives information like the multiplier, the DIFF_MODE and the EXTRA_INFO.     |

The Item info is an array of printable characters (like a string that doesn't
end with `0x00`), it can take several shapes and each has a slighly different
meaning. This array shows a bunch of numbers (sometimes letters) separated by
slashes, for example: "1/1/7", "1/1", or "1/B". When there are three
numbers/letters, the first one is the multiplier in decimal, and the third one
is the diff_mode in hexadecimal. When there are two, the first one is probably
the multiplier (which is always "1"), and the second one is the extra_info, but
left padded with zeros to occupy 8 characters (because it represents a 4 bytes
integer) ONLY WHEN the item corresponds to a document, photo or statuette (for
example, "1/00020834"), not with maps. With maps we only have the letter itself,
like for example "1/C". When it's just one number, it's usually 1 and it
probably represents the multiplier, the diff_mode is probably "7" by default and
the extra_info "1" (which corresponds to an EXTRA_INFO of `0x00000000` in the
`allitems.it` file) by default.

More research on this last field is needed.

## SAV (ObsCure)

General structure of the `.sav` files of the first game (everything is in LSB):

| Type           | Size | Name         | Description                             |
|----------------|------|--------------|-----------------------------------------|
| SAV_Header     | 22   | Header       | Gives general info on the game.         |
| SAV_Items      |      | ItemsAndInfo | Sequence of picked up items.            |
| SAV_Characters |      | Characters   | Information on each playable character. |
| SAV_Progress   |      | Progress     | Progress in the game and in each room.  |

### SAV_Header

| Type          | Size | Name           | Description                                                     |
|---------------|------|----------------|-----------------------------------------------------------------|
| uint32        | 4    | CRC32          | CRC32 checksum of the rest of the contents of the file.         |
| uint8         | 1    | ???            | ??? 0x06                                                        |
| uint32        | 4    | Index          | Index of the savefile. It's 'N' in "gameN.sav".                 |
| ROOM_ID_SHORT | 1    | Room           | Room where the game was saved.                                  |
| uint32        | 4    | Play time      | Duration of the gameplay in milliseconds.                       |
| uint8         | 1    | Times saved    | Number of times the player saved the game.                      |
| ???           | 2    | ???            | ???                                                             |
| DIFF_MODE     | 1    | Diff mode      | Difficulty and mode of the game.                                |
| ???           | 2    | ???            | ??? Usually 0x6400                                              |
| uint16        | 2    | Length of rest | Length of SAV_Items + SAV_Characters (excluding these 2 bytes). |

For more info on the ROOM_ID_SHORT type, check
[this link](constants.md/#room_id-and-room_id_short).

### SAV_Items

| Type              | Size | Name                | Description                                                                     |
|-------------------|------|---------------------|---------------------------------------------------------------------------------|
| uint16            | 2    | Length of SAV_Items | Length of this SAV_Items section (including these 2 bytes).                     |
| uint8             | 1    | Inventory capacity  | Let's call the value 'I'. I * sizeof(SAV_ItemOrAmmo) = Length of SAV_Items - 3. |
| SAV_ItemOrAmmo[I] | I*9  | Items               | Sequence of items, the inventory itself.                                        |

Sav_ItemOrAmmo is the union of the following types:

- SAV_Item
- SAV_Ammo

SAV_Item:

| Type       | Size | Name       | Description                                    |
|------------|------|------------|------------------------------------------------|
| uint32     | 4    | Item UID   | Unique identifier for the item.                |
| uint8      | 1    | Number     | How many examples of this item the player has. |
| EXTRA_INFO | 4    | Extra info | Extra info on the item.                        |

SAV_Ammo:

| Type      | Size | Name      | Description                                                      |
|-----------|------|-----------|------------------------------------------------------------------|
| AMMO_TYPE | 1    | Ammo type | `0x06` for shotgun and `0x07` for handgun.                       |
| uint32    | 4    | ???       | 0x00 00 00 XX where 0xXX is the least valuable byte of Quantity. |
| uint32    | 4    | Quantity  | Quantity of ammunition of said type.                             |

Despite being "Quantity" in LSB (like everything in SAV files), "???" is in MSB.
So the first 3 bytes are zeros and the 4th byte is this `0xXX` described above.

At this point you might wonder how the game knows if the SAV_ItemOrAmmo is a
SAV_Item or a SAV_Ammo. The game checks the first byte, if it's a `0x06` or a
`0x07`, then it's a SAV_Ammo, otherwise it's a SAV_Item. That's because there
is no item UID in the game ending with `0x06` or `0x07` (note how I wrote
"ending" instead of "starting" because it's in LSB).

### SAV_Characters

| Type             | Size | Name       | Description                             |
|------------------|------|------------|-----------------------------------------|
| SAV_Character[5] |      | Characters | An array of 5 SAV_Character structures. |

In this sequence of 5 SAV_Character, the order of the characters is Stan, Josh,
Kenny, Ashley and Shannon.

SAV_Character:

| Type          | Size | Name                    | Description                                                     |
|---------------|------|-------------------------|-----------------------------------------------------------------|
| uint16        | 2    | Length of SAV_Character | Length of SAV_Character (including these 2 bytes).              |
| uint8         | 1    | Capacity                | Weapons inventory capacity. Let's call it 'W'.                  |
| uint32        | 4    | Door                    | Represents the door next to which the character is standing.    |
| uint8         | 1    | Room                    | Room where the character is located.                            |
| bool          | 1    | IsTeammate?             | ??? `0x01` if this character is the teammate, `0x00` otherwise. |
| float         | 4    | X pos?                  | Coordinate of the character along the X axis.                   |
| float         | 4    | Y pos?                  | Coordinate of the character along the Y axis.                   |
| float         | 4    | Z pos?                  | Coordinate of the character along the Z axis.                   |
| uint8         | 1    | Rotation?               | Rotation of the character.                                      |
| uint32        | 4    | Current weapon UID      | The Item UID of the weapon the character is holding.            |
| ???           | 29   | ???                     | ???                                                             |
| float         | 4    | Health                  | Health of the character.                                        |
| SAV_Weapon[W] | W*9  | Weapons                 | Weapons inventory of the character.                             |

SAV_Weapon:

| Type            | Size | Name            | Description                                                      |
|-----------------|------|-----------------|------------------------------------------------------------------|
| uint32          | 4    | Weapon item UID | Unique identifier of the weapon (which is also a pickable item). |
| uint8           | 1    | Quantity        | Quantity of said weapon (always `0x01`).                         |
| Ammo_Flash_Info | 4    | AmmoFlashlight  | Ammo in the chamber of the weapon and attached flashlight.       |

Ammo_Flash_Info has a weird structure, let's imagine this as a uint32 in its
MSB notation (even though it's stored in LSB in the `.sav` file):
`0xXX XX XY YY`, the least valuable byte is `0xYY`, the next one is `0xXY`, then
the two `0xXX`. `0xXX XX X` stores information on the flashlight that is
attached to the gun, while `0xY YY` stores the ammunition the gun holds in its
chamber. Usually the game only uses 8bits (or even 4bits, since it's usually a
number between 0 and 15), so the least valuable byte `0xYY`, but it actually
reads the 12bits `0xY YY`, so you can actually have up until 16^3 - 1 bullets in
the chamber if you modify the `.sav` file manually.

### SAV_Progress

| Type                 | Size | Name   | Description                                                                |
|----------------------|------|--------|----------------------------------------------------------------------------|
| SAV_Progress_Chunk[] |      | Chunks | Each of these generic chunks give information on the progress in the game. |

SAV_Progress_Chunk:

| Type              | Size | Name    | Description                                                       |
|-------------------|------|---------|-------------------------------------------------------------------|
| uint16            | 2    | Length  | Length of the chunk (excluding these 2 bytes). Let's call it 'C'. |
| SAV_Chunk_Content | C    | Content | The content of the chunk, which can have different structures.    |

The first 3 SAV_Progress_Chunks give general information on the progress in the game, like,
for example, the drawings in the map. While the other chunks give information
on each specific room. This structure is a union of the following
structures:

- SAV_Room_Chunk_Content

Structure of the SAV_Room_Chunk_Content:

| Type          | Size | Name | Description                                                                |
|---------------|------|------|----------------------------------------------------------------------------|
| ROOM_ID_SHORT | 1    | Room | The chunk gives information on this room.                                  |
| ???           | 1    | ???  | ??? Gives information on the kinematics that have been played in the room? |
| ???           | 1    | ???  | ??? 0x00?                                                                  |
| ???           | ?    | ???  | ???                                                                        |

## HOE (ObsCure)

Here's the general structure of HOE files (everything is in MSB):

| Type        | Size | Name   | Description                                      |
|-------------|------|--------|--------------------------------------------------|
| float?      | 4    | ???    | ???                                              |
| HOE_Chunk[] | ?    | Chunks | A sequence of chunks, there are different types. |
| uint32      | 4    | End    | `0x00 00 00 04` indicating the end of the file.  |

The HOE_Chunk structure is the following:

| Type                | Size | Name       | Description                                                         |
|---------------------|------|------------|---------------------------------------------------------------------|
| HOE_CHUNK_TYPE      | 4    | Chunk type | Specifies the type of chunk, and how its content must be intepreted |
| HOE_Content         | ?    | Content    | The content of the chunk.                                           |

HOE_CHUNK_TYPE can have the following values:

| Value | Meaning                 |
|-------|-------------------------|
| 0x02  | Collisions              |
| 0x03  | Imported functions ???  |
| 0x05  | Event definition        |
| 0x06  | Event Instances         |

Chunks of the same type are always contiguous, and the order in which the
types of chunks are found in the file is the following: `0x03` (if there is
any), `0x02`, `0x05`, and finally `0x06`.


HOE_Content is just the union of the following structures:

- HOE_Collision
- HOE_Imports
- HOE_Event
- HOE_Instance

Depending on the chunk type, the content must be interpreted as one of the
following structures.

### HOE_Collision

This structure defines the collisions of the room. Thanks to it, characters
don't pass through tables, walls, etc. Here's its format:

| Type   | Size | Name            | Description                                                    |
|--------|------|-----------------|----------------------------------------------------------------|
| uint32 | 4    | Length of chunk | Length of the chunk, excluding these 4 bytes. Let's call it L. |
| ???    | L    | ???             | ??? Unknown format                                             |

### HOE_Imports

This structure seems to mention the name of some functions. Here's its format:

| Type   | Size | Name            | Description                                                    |
|--------|------|-----------------|----------------------------------------------------------------|
| uint32 | 4    | Length of chunk | Length of the chunk, excluding these 4 bytes. Let's call it L. |
| ???    | L    | ???             | ??? Unknown format                                             |

### HOE_Event

There's still a lot of research to do, especially regarding the scripting
language, but this is more or less the structure of the event definitions (most
of the time):

| Type       | Size | Name              | Description                                                 |
|------------|------|-------------------|-------------------------------------------------------------|
| float      | 4    | ???               | Always 4.0??                                                |
| LString    | ?    | Name              | Name of the event.                                          |
| ???        | ?    | ???               | ???                                                         |
| uint32     | 4    | Number of strings | Number of following strings, let's call it N.               |
| LString[N] | ?*N  | Strings?          | ???                                                         |
| uint32     | 4    | Number of vars    | Number of following variables, let's call it V.             |
| HOE_Var[V] | 8*V  | Vars              | Variables used in HOE scripts.                              |
| uint32     | 4    | Number of -1s     | Number of following `0xFF FF FF FF`??? Let's call it M.     |
| uint32[M]  | 4*M  | Minus ones???     | An array of `0xFF FF FF FF`s???                             |
| ???        | ???  | Script            | This is a script in some bytecode language. Unknown format. |

For more information on LStrings, check [this link](constants.md/#lstring).
The scripting language includes a lot of strings that seem to be function names,
it also includes what seems to be a bunch of OP codes, like `0x65`, `0xC9`,
`0xD0`, etc. It also includes sometimes 32bits integers that represent a
HOE_Var, to be precise they are the index of a specific HOE_Var in the `Vars`
array. The scripts sometimes end with `0x00 00 00 07`.

The HOE_Var structure has the following format:

| Type         | Size | Name  | Description            |
|--------------|------|-------|------------------------|
| HOE_VAR_TYPE | 4    | Type  | The type of the value. |
| uint32/float | 4    | Value | The value.             |

HOE_VAR_TYPE dictates how the value must be intepreted. It can have the
following values:

| Value | Meaning           |
|-------|-------------------|
| 0x01  | Unsigned Integer  |
| 0x02  | Float             |

### HOE_Instance

| Type   | Size | Name                     | Description                                                    |
|--------|------|--------------------------|----------------------------------------------------------------|
| uint8  | 1    | ???                      | ???                                                            |
| uint32 | 4    | Length of Instance chunk | The length of the rest of this chunk, including these 4 bytes. |
| ???    | ???  | Unknown content          | ???                                                            |

Among other things, in the "Unknown content" you can find the coordinates of
monsters (when the HOE_Instance represents a monster, which is not always the
case) in the form of an array of 3 floats (X, Y and Z), usually after the
"gIsAttacking" string.