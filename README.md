# NetEC - Pandanales 

NetEC is a tool for inferring whole genome duplication (WGD) episodes by reconciling gene trees with species trees or networks. It uses dynamic programming to find the minimum number of duplication episodes that explain the observed gene tree duplications.

## Requirements

-- Netec repository 

```
git clone git@github.com:ppgorecki/netec.git
```

## Input Files 

Network - pandanales.nwk

Gene trees - gene_trees.nwk

Note that files are large and the process may require 20-25GB RAM.

### Initial run - identify fixed episodes (for furter optimization)

```
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir fixed --fixed_episodes_only --verbose 2
```

### Dicovery mode - run after fixed episodes are completed

```
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir disco --save_embedding --no_fixed_episodes_search --fixed_episodes "$( cat fixed/fixed_episodes )"
```

### Search for extended episodes 

```
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir discoext --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact)" --optimize single
```

### Extended episodes inference

Add episode at 21 (Gamma)
```
netec/netec.py --network network.nwk --gene_trees gene_trees.nwk --verbose 2 --out_dir discoext21 --locked_epi_support --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact) 21" --optimize single
```

Or, run jointly for node 21 (Gamma), 19 (Delta4) and both 21, 19. 
```
parallel netec/netec.py --network network.nwk --gene_trees gene_trees.nwk --verbose 2 --out_dir discoext"{1}" --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact) {1}" --optimize single ::: 19 21 "19 21"
```

## Questions

For questions contact: <gorecki@mimuw.edu.pl>

## Funding

This work was supported by National Science Centre grant:
- #2023/51/B/ST6/02792 (networks)
