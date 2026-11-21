# A1: What is this molecule, really?

Assignment A1 for PharmSci 175/275. Students work with the common ways a
molecule gets represented — SMILES, SDF, PDB — break those representations on
purpose, watch what the breakage does to calculated properties and filter
verdicts, and then run a small fingerprint similarity search in which the
difference shows up as a difference in the science.

## Contents

| File | What it is |
|---|---|
| [`handout.md`](handout.md) | The assignment handout: background, tasks, submission instructions |
| [`data/search_pool.csv`](data/) | 534 compounds — 54 confirmed HIV-1 integrase binders and 480 fragments tested against the same target that did not bind. Columns: `name`, `smiles`, `is_active` |
| [`data/library_chembl_sample.csv`](data/) | 2,000 bioactive compounds sampled from ChEMBL. Columns: `chembl_id`, `pref_name`, `smiles` |
| [`data/approved_drugs_chembl.csv`](data/) | 3,311 approved small-molecule drugs from ChEMBL. Same columns |
| [`data/3NF8_ligand.pdb`](data/) | The ligand extracted from PDB entry 3NF8, a crystal structure of HIV-1 integrase with an inhibitor bound |

## Data provenance

### `search_pool.csv` — SAMPL4 HIV-1 integrase

Derived from the **SAMPL4** blind prediction challenge. The actives are
Avexa-series HIV-1 integrase inhibitors; the non-binders are Maybridge
fragments that were tested against the same target and did not bind.

- **Source files:** `actual_w_maybridge.txt` and `final_clean_binders.txt`
  from [MobleyLab/drug-computing](https://github.com/MobleyLab/drug-computing),
  `uci-pharmsci/assignments/3D_structure_shape/`.
- **Challenge paper:** Mobley, D. L. et al. Blind prediction of HIV integrase
  binding from the SAMPL4 challenge. *J. Comput.-Aided Mol. Des.* **28**,
  327–345 (2014).
  [doi:10.1007/s10822-014-9723-5](https://doi.org/10.1007/s10822-014-9723-5)

**Curation applied.** The two source files cannot be joined on their name
columns as distributed: the binder list carries `_0`/`_1` suffixes that the
compound list does not, and it contains one compound twice as two
protonation/tautomer variants. Suffixes were stripped and the duplicate
collapsed. Of the 131 Avexa-series compounds in the source list, 49 are
confirmed binders; the remaining 82 have **unconfirmed** status and were
**dropped** rather than labelled inactive, since the source data do not
support calling them non-binders. Five confirmed binders are not
Avexa-series. The result is 54 actives and 480 Maybridge non-binders, with an
explicit `is_active` column. Actives and non-binders were verified disjoint.
SMILES were canonicalized with RDKit 2024.03.5.

### ChEMBL files

Retrieved 2026-11-21 from the ChEMBL web API
(`https://www.ebi.ac.uk/chembl/api/data/molecule.json`), release
**ChEMBL_37**.

- `approved_drugs_chembl.csv`: `max_phase=4` **and**
  `molecule_type="Small molecule"` — 3,475 records returned, 3,311 of which
  carry a canonical SMILES. The restriction to small molecules is
  deliberate: an unrestricted `max_phase=4` query also returns peptides and
  biologics, which makes a Rule-of-Five exercise meaningless.
- `library_chembl_sample.csv`: 2,000 unique small molecules drawn from 20
  random offsets across the 1,920,259-record small-molecule set, with
  `random.seed(20270120)`. Approved-drug IDs were excluded so that the two
  files are independent.

Both ChEMBL files are left **exactly as ChEMBL records them**: no
deduplication, no standardization, no salt stripping, no property filtering.
Dealing with the data as it actually comes is part of the assignment.

Note that these are compounds drawn from the **medicinal-chemistry
literature** — this is not a vendor screening catalogue, and should not be
described as one.

**Licence:** the two ChEMBL files are **CC BY-SA 3.0**, which attaches to
those data files. Cite as: Zdrazil, B. et al. The ChEMBL Database in 2023.
*Nucleic Acids Res.* **52**, D1180–D1192 (2024).

### `3NF8_ligand.pdb`

The ligand from [PDB entry 3NF8](https://www.rcsb.org/structure/3NF8),
HIV-1 integrase with an inhibitor bound, via
[MobleyLab/drug-computing](https://github.com/MobleyLab/drug-computing).
PDB data are in the public domain.

The file is distributed **unmodified**, including the information that the
PDB format does not carry. Recovering a usable molecule from it is part of
the assignment.
