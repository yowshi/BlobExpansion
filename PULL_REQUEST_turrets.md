# Add craftable placeable blob turrets

Changes against `yowshi/BlobExpansion` `master`. New content only — no existing
definitions are modified. Summary: **4 files modified, 0 new files.**


##Minor changes:

Edited descriptions of the various growing blobs to say what they're going to become. 
Edited descriptions of the various weapons to say more clearly what they do: two-tile hit, stunning, shoot+fragment, lightning+explode, etc. 

Most importantly, changed the description of "growing blob frame" to explain that it's the starting recipe. 
You've got to make a gloople to get the whole tech tree started; it's not just a vehicle frame. 

Also edited the modinfo.json to add myself as a contributor (thanks!), and changed the description to deemphasize amphibious vehicles and emphasize 
all the different stuff we're making from corpse material. 



## Major change: 
Adding a family of **placeable** (non-vehicle) blob turrets. Each is crafted as a
dormant item, set down, and fed its "fuel" to wake. They reuse the mod's
craftable blob weapons as the firing component, so they fit the existing
organic tech tree parallel to BN's electronics turrets.


## The turrets

| Item | Fires | Source weapon | Range | Fuel/ammo |
|---|---|---|---|---|
| `bot_blob_turret` (ice needler) | ice needles | `ice_plinker` | 18 | blob feed |
| `bot_blob_guard_turret` | latching teeth | `biter` | 1 | blob feed |
| `bot_blob_guard_large_turret` | saw-fangs | `horror` (blobsaw) | 2 | blob feed |
| `bot_blob_flamethrower_turret` | burning ooze stream | `torchie` (fire puffer) | 12 | gasoline |
| `bot_blob_lightning_turret` | electric arcs | `sparkie` (shock bulb) | 8 | blob feed |

Each turret is four linked objects, following BN's
`bot_turret_light` → `mon_turret_light` pattern.

---

## Modified files

### `weapons.json` — fake turret guns
Added one `*_turret_fake` GUN per turret (`blob_turret_fake`,
`blob_guard_turret_fake`, `blob_guard_large_turret_fake`,
`blob_flamethrower_turret_fake`, `blob_lightning_turret_fake`). Each is
`copy-from` its source blob weapon with a turret-appropriate `AUTO` mode, a large
internal `clip_size`, and empty `magazines`. This is the same hidden-gun trick
the base game uses (`ksub2000_turret_fake`), keeping the player's craftable
weapon stats untouched.

- The lightning weapons (`sparkie`/`voideater`) are UPS-powered with no ammo
  item, so `blob_lightning_turret_fake` sets `ups_charges: 0` and adds
  `ammo: bfeed` while keeping the `LIGHTNING` projectile effect — the turret
  burns blob feed as its power source.

### `blaze_blob_monster.json` — turret monsters
Added one `MONSTER` per turret (`mon_blob_turret`, 
`mon_blob_guard_turret`,
`mon_blob_guard_large_turret`, 
`mon_blob_flamethrower_turret`,
`mon_blob_lightning_turret`). 
All are `IMMOBILE`, `BLOB`-species, `default_faction`
`blob`, with a `gun` special attack pointing at the matching fake gun, `RETURN_FIRE`
when hit, and `starting_ammo` for their fuel.

- `death_function` is `MELT` (consistent with other blobs in the mod), which
  avoids dangling a reference to a nonexistent `broken_*` item.
- Themed flags/armor per type: the flamethrower turret is `FIREPROOF` with
  `armor_fire`; the lightning turret has `ELECTRIC_FIELD`.

### `deployable.json` — dormant turret items
Added one `TOOL` per turret (`bot_blob_*`) with a `place_monster` `use_action`
targeting the matching monster. Deploy uses the `cooking` skill rather than
`electronics`/`computer`, since you're coaxing a creature rather than wiring a
robot.

### `blob_recipes.json` — recipes
Added one recipe per turret. All are `cooking` + `blobrep` quality, `autolearn`,
consuming a `slime_turret_controls` (the organic AI), the source blob weapon, raw
gloople/slime_scrap, and the turret's fuel. The large guard also consumes an
`oozle`; the lightning turret also consumes a `glowie` (gelectrode).

---

