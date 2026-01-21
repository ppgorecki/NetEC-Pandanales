# NetEC - Pandanales

This dataset shows inference of WGD events from Pandanales using NetEC, a tool for inferring whole genome duplication (WGD) episodes by reconciling gene trees with species trees or networks. It uses dynamic programming to find the minimum number of duplication episodes that explain the observed gene tree duplications.

## Requirements

Download the NetEC repository:

```bash
git clone git@github.com:ppgorecki/netec.git
```

## Input Files

- **Network:** `pandanales.nwk`
- **Gene trees:** `gene_trees.nwk`

> **Note:** Files are large and the process may require 20-25GB RAM.

## Usage

### Initial run: identify fixed episodes

Identifying fixed episodes optimizes all subsequent runs.

```bash
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir fixed --fixed_episodes_only --verbose 2
```

### Discovery mode

#### Infer episodes

Run after fixed episodes are completed.

```bash
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir disco --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat fixed/fixed_episodes)"
```

#### Search for extended episodes

```bash
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir discoext --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact)" --optimize single
```

#### Extended episodes inference

Add episode at 21 (Gamma):

```bash
netec/netec.py --network network.nwk --gene_trees gene_trees.nwk --verbose 2 --out_dir discoext21 --locked_epi_support --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact) 21" --optimize single
```

Or, run jointly for node 21 (Gamma), 19 (Delta4), and both 21, 19:

```bash
parallel netec/netec.py --network network.nwk --gene_trees gene_trees.nwk --verbose 2 --out_dir discoext"{1}" --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat disco/best_wgd_nodesexact) {1}" --optimize single ::: 19 21 "19 21"
```

### Hypothesis-driven mode

There are five WGD hypotheses from Shi, T. and He, J. *Resolving phylogenetic conflicts in Pandanales: the dual roles of gene flow and whole-genome duplication.* Frontiers in Plant Science, 16:1511582, 2025.

Here, we have them at network nodes: 3 (Delta 1), 4 (Delta 2), 12 (Delta 3), 19 (Delta 4), and 28 (Delta 5).

#### Infer episodes

Run after fixed episodes are completed.

```bash
WGD="3 4 12 19 28"
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir panda --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat fixed/fixed_episodes) $WGD"
```

#### Search for extended episodes

```bash
netec/netec.py --network pandanales.nwk --gene_trees gene_trees.nwk --out_dir pandaext --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat panda/best_wgd_nodesexact)" --optimize single
```

#### Extended episodes inference

Add episode at 21 (Gamma):

```bash
netec/netec.py --network network.nwk --gene_trees gene_trees.nwk --verbose 2 --out_dir pandaext21 --locked_epi_support --extended_episodes_from_fixedepi --save_embedding --no_fixed_episodes_search --fixed_episodes "$(cat panda/best_wgd_nodesexact) 21" --optimize single
```

## Questions

For questions contact: gorecki@mimuw.edu.pl

Trees and network prepared by Agnieszka Mykowiecka.

## Funding

This work was supported by National Science Centre grant:

- #2023/51/B/ST6/02792 (networks)
