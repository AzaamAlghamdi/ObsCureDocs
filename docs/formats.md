# File formats

The "Size" column in each table should be interpreted in bytes, unless specified
otherwise.

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

- [ITEM_TYPE](/constants/#item_type)
- [EXTRA_INFO](/constants/#extra_info)
- [DIFF_MODE](/constants/#diff_mode)

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
| 0x06  | NPC section (?)         |
| 0x07  | Description section (?) |
| 0x08  | Item section            |

TM_Generic_Content is just a union of these structures:

- TM_Item_Content

Depending on the TM_SECTION_TYPE, the content must be interpreted in different
ways. For example, if TM_SECTION_TYPE is `0x08`, then the content of the section
must be interpreted as a TM_Item_Content structure. Here are the formats of each
of the previous structures.

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