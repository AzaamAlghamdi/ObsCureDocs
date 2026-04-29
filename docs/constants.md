# Constants and identifiers

## ObsCure

### ITEM_TYPE
Item types are encoded as uint32. Every value is left padded with zeros. Here's
the list of all types:
```c
typedef enum
{
    NO_ITEM_ID = 0x0000,
    WEAK_FLASHLIGHT = 0X00C9,
    BAT = 0x00CA,
    OLD_PISTOL = 0x00CB,
    LASER = 0x00CC,
    SHOTGUN = 0x00CE,
    METAL_BAR = 0x00D1,
    LIGHT_GRENADE = 0x00D2,
    GUN_WITH_FLASHLIGHT = 0x00D3,
    EMPTY_SLOT_ = 0x00D4,
    YELLOW_FLASHLIGHT = 0x00D5,
    POWERFUL_FLASHLIGHT = 0x00D6,
    AUTOMATIC_PISTOL = 0x00D7,
    REVOLVER = 0x00D8,
    DOUBLE_BARREL_SHOTGUN = 0x00D9,
    ALUMINIUM_BAT = 0x00DA,
    LASER_GUN = 0x00DC,
    SHOTGUN_AMMO = 0x0130,
    HANDGUN_AMMO = 0x0132,
    STATUETTE = 0x0191,
    SAFE = 0x0192,
    HOOK = 0x0194,
    FUSES = 0x0195,
    WOOD_PLANK = 0x0197,
    NEEDLE = 0x0199,
    SCREWDRIVER = 0x019A,
    PIECE_OF_PAPER = 0x019B,
    LEVER = 0x019C,
    FILM = 0x01F5,
    PHOTO = 0x01F6,
    DOCUMENT = 0x01F7,
    MAP = 0x01F8,
    BASEMENT_PLAN = 0x01F9,
    KEY = 0x0259,
    DISC = 0x025A,
    TAPE = 0x025B,
    PLIERS = 0x025C,
    WHEEL = 0x025E,
    MEDKIT = 0x025F,
    CELLPHONE = 0x0260,
    VIDEO_TAPE = 0x0261,
    REEL_OF_WIRE = 0x0263,
    ENERGY_DRINK = 0x0264,
} item_type;
```

### EXTRA_INFO
Gives more information on documents, photos, maps and statuettes, encoded as
uint32. Note that for maps we have letters, that's because the game uses their
ASCII codes. All values are left padded with zeros.
```c
typedef enum
{
    STATUETTE_PRAYING = 0x01,
    STATUETTE_BROKEN = 0x02,
    STATUETTE_CROSS = 0x03,
    STATUETTE_HANDS_BACK = 0x04,
    PIECE_OF_PAPER_DOC = 0x020601,
    BUILDING_OF_SCHOOL = 0x020800,
    LEONARD_STATE = 0x020801,
    LETTER_FROM_WALT_KERRIDAN = 0x020802,
    THE_MORTIFILIA = 0x020803,
    SUBJECT_22 = 0x020804,
    SHOM_TRANSFORMATION_PHOTOS = 0x020805,
    CENSORED_NEWSPAPER = 0x020806,
    BASEMENT_PHOTOS = 0x020807,
    PRESS_CLIPPINGS = 0x020808,
    FIND_THE_SAFE = 0x020809,
    TYPES_OF_MUTATION = 0x020810,
    LETTER_FROM_WICKSON = 0x020811,
    LETTER_TO_WICKSON = 0x020812,
    BEAUTY_CLUB_ = 0x020813,
    BASKETBALL_PROGRAMMEE_ = 0x020814,
    PHOTO_TWINS = 0x020817,
    EXPEDITION_DOCUMENTS = 0x020819,
    EXPEDITION_PLAQUE = 0x020820,
    NECROLOGY = 0x020821,
    ALAN_DIARY = 0x020834,
    EXPULSION_COMMUNITY = 0x020836,
    ADMINISTRATION_MAP = 'B',
    CLASSROOMS_MAP = 'C',
    LIBRARY_MAP = 'D',
    REFECTORY_MAP = 'E',
    AMPHITHEATRE_MAP = 'F',
    DORMITORY_MAP = 'I'
} extra_info;
```

### DIFF_MODE
The DIFF_MODE is a value encoded as a uint32 (left padded with zeros) that tells
us whether an item is present in specific difficulties and modes. Modes are "New
Game" and "Special Mode". The DIFF_MODE is an integer where each one of the 4
least valuable bits are flags. Here are the flags and the weights of their bits:

- 0: Easy difficulty
- 1: Normal difficulty
- 2: Hard difficulty
- 3: Special Mode

If a difficulty flag is set, the item shows up in that difficulty. As for the
Special Mode flag, the only item that has it set to 1 is the laser gun, and its
DIFF_MODE is `8`, so only the Special Mode flag is set, even though the gun
appears in all difficulties (need to do more tests on this).

More examples of DIFF_MODEs:

- `0x7`: `0b111`, the item is shown in all difficulties
- `0x4`: `0b100`, the item only shows up in Hard difficulty
- `0x3`: `0b011`, the item shows up in Easy and Normal

### ITEM_TYPE_SHORT

This is just another way to refer to each ITEM_TYPE, they are encoded as uint32
even though they only take one byte, but they are left padded with zeros. They
are used hide some items, for example.

| ITEM_TYPE_SHORT | Item |
|----|--------------|
| 7A | Shotgun ammo |
| 7B | Handgun ammo |
| 66 | Bad quality flashlight |
| 67 | Yellow flashlight      |
| 68 | Powerful flashlight    |
| CC | Laser                  |
| 69 | Yellow bat             |
| 6A | Metal bar              |
| 6B | Aluminium bat          |
| 6C | Old pistol             |
| 6D | Automatic pistol       |
| 6E | Revolver               |
| 6F | Laser gun              |
| 70 | Laser                  |
| 71 | Shotgun                |
| 72 | Double barrel shotgun  |
| 73 | Light grenade ?        |
| 74 | Gun with flashlight    |
| A2 | Disc          |
| A3 | Sticky tape   |
| A6 | First aid kit |
| AB | Energy drink  |
| 83 | Statuette                       |
| 84 | Safe (unused)                   |
| 85 | Hook (looks weird maybe?)       |
| 86 | Box of fuses and fuse (unused?) |
| 87 | Wood plank                      |
| 88 | Paper cup                       |
| 89 | Compass Needle                  |
| 8A | Screwdriver                     |
| 8B | White image (?)                 |
| 8C | Lever (looks weird maybe?)      |
| 97 | “Filmrolle”                     |
| 9B | Diary? (unused)                 |
| A1 | Nothing (no blue box)           |
| A4 | Pliers                          |
| A5 | Wheel                           |
| A8 | Video tape                      |
| AA | Reel of wire                    |