# Products of the SFG 2025 Workshop on Taxonomic Names

## DWCa export of Open Tree's taxonomy.
  * The tar archives at https://files.opentreeoflife.org/dwca/ were produced by running [this gist](https://gist.github.com/mtholder/37d063b6bbc31c9ddf70bcdc15b46be2). This version of OTT lacks our synonyms information. And names that are thought to not be appropriate have been filtered. This was done by removing taxa with any of the following flags: `major_rank_conflict`, `major_rank_conflict_inherited`, `environmental`, `viral`, `barren`, `not_otu`, `hidden`, `was_container`, `inconsistent`, `hybrid`, `merged`  See https://github.com/OpenTreeOfLife/reference-taxonomy/blob/master/doc/taxon-flags.md
  * Prereq: [this other gist](https://gist.github.com/mtholder/f733a75fe1616af1841ded62aadb4f04) to export to dwca and conversion to sqlite using https://github.com/sfborg/from-dwca
  * Also produced at this hackathon this branch of otcetera https://github.com/OpenTreeOfLife/otcetera/tree/dwca-dump

## Comparison of OTT and CoLXR draft

  * Used https://github.com/sfborg/sf to make a sqlite db of name matches between OTT and a draft of col-xr in under an hour (comparing 3.9 million names to 9 million names)

## Tape convo

See [Taxonomic data on tape.md](./Taxonomic data on tape.md)
