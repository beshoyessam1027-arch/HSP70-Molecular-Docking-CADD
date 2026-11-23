# Hsp70–Curcumin: Docking and 25 ns GROMACS MD (in silico)

Computational study of **curcumin** binding to the nucleotide-binding domain (NBD) of human **Hsp70 (HSPA1A)**: ADME screening, AutoDock Vina docking, and a single 25 ns all-atom MD run in GROMACS.

> **Status:** computational prediction only. No experimental validation. The results below describe what was simulated; they are not evidence that curcumin inhibits Hsp70.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23085117.svg)](https://doi.org/10.5281/zenodo.23085117)

- Preprint (Zenodo): https://doi.org/10.5281/zenodo.23085117
- Manuscript PDF in this repo: [Hsp70_Curcumin_Manuscript.pdf](./Hsp70_Curcumin_Manuscript.pdf)

---

## Background

Hsp70 is a molecular chaperone overexpressed in many cancers and is linked to tumor cell survival and resistance to apoptosis. The ATP-binding NBD is one of the sites considered for small-molecule inhibition. This project asks a narrow question: does docked curcumin stay near its docked site during a short MD run?

---

## Repository contents

| Path | What it is |
| :--- | :--- |
| `topol.top` | Protein topology from `gmx pdb2gmx` (GROMACS 2023.3), with ligand, TIP3P water and ion includes |
| `posre.itp` | Protein position restraints |
| `curcumin_ligand.itp` | Curcumin topology used in `topol.top` (GAFF atom types, net charge 0) |
| `ligand/` | Curcumin structure files and acpype output in several formats (AMBER, GROMACS, CHARMM, CNS), plus `em.mdp` and `md.mdp` |
| `figures/` | Docking, pocket, interaction and trajectory-animation figures |
| `Protein_Stability_MD_Analysis.png` | Protein RMSD, RMSF and Rg |
| `Ligand_Interaction_MD_Analysis.png` | Ligand RMSD and protein–ligand H-bonds |
| `Hsp70_Curcumin_Manuscript.pdf` | Two-page preprint |

Trajectories and run files (`*.xtc`, `*.trr`, `*.edr`, `*.cpt`, `*.tpr`, `*.log`) are excluded by `.gitignore`.

---

## Methods (as reflected in the files in this repo)

### Target and pocket
- Structure: PDB `7GYI`, reported resolution 1.90 Å. ⚠️ *TODO(confirm): entry, chain and construct (the RMSF plot shows ~390 residues, consistent with an NBD construct).*
- Pocket prediction: P2RANK (PrankWeb), pocket 1 in the ATP-binding site.

### ADME / toxicity
- SwissADME: curcumin passes Lipinski's Rule of Five.
- ADMETlab 3.0: hERG probability 0.437, hepatotoxicity probability 0.429. These are mid-range values and are not a clear "low risk" call.

### Docking
- AutoDock Vina, grid centered on the predicted ATP-site pocket. Reported best affinity: **−7.132 kcal/mol**.
- ⚠️ *TODO: add receptor/ligand PDBQT files, Vina config (center, box size, exhaustiveness, seed), the output poses and the log.*
- ⚠️ *TODO: only curcumin and thymoquinone are described as screened. State the real number of compounds, or call this a two-compound comparison.*
- Interactions (PLIP): hydrogen bonds reported with Glu-269 and "Lys-1296". ⚠️ *TODO: residue 1296 does not exist in a ~390-residue NBD. Replace with the PDB-chain numbering and the matching residue in the simulated topology.*

### Ligand parameterization
- Tool: acpype v2022.7.21, GAFF atom types, written to `curcumin_ligand.itp` (47 atoms, net charge 0).
- Tautomer: **diketo** form (central sp3 CH2 between two C=O). Curcumin is mostly enol in solution, so this is a modeling choice and a limitation.
- ⚠️ *TODO(confirm): partial-charge method (acpype default is AM1-BCC).*

### System setup
- Protein: `gmx pdb2gmx -f 7GYI_fixed.pdb -o 7GYI_processed.gro -p topol.top -ignh`
- Force field: **AMBER99SB-ILDN** (protein), **GAFF** (ligand), **TIP3P** water.
- Solvent and ions: 42,900 water molecules and 1 Cl⁻ (charge neutralization only).
- ⚠️ *TODO: box type and size (cubic vs octahedron) and the solvation/ion commands.*

### Simulation parameters (`ligand/md.mdp`)
- 2 fs time step, 12,500,000 steps = **25 ns**; H-bond constraints; PME electrostatics.
- V-rescale thermostat, single `System` group, reference temperature **300 K**.
- **No pressure coupling** in this file (the production run is NVT as written).
- Frames saved every 10 ps.
- ⚠️ *TODO: this file sits in `ligand/` and refers to an acpype test topology. Confirm it is the file actually used for production, and add the real `ions.mdp`, `nvt.mdp` and `npt.mdp` with equilibration lengths.*

---

## Results (read from the plots)

### Protein
- Backbone RMSD rises to ~0.25 nm in the first nanosecond, varies between ~0.25 and ~0.45 nm from 5 to 19 ns (peak near 14 ns), and sits around 0.25–0.30 nm over the last ~6 ns.
- Rg falls from ~2.3 nm at the start to ~2.20–2.23 nm in the last ~5 ns.
- RMSF is below ~0.3 nm for most residues; the N- and C-terminal residues move much more (up to ~1.0 and ~1.45 nm).

### Ligand
- Ligand RMSD stays near 0.1–0.3 nm until ~12 ns, then moves to ~0.4–0.75 nm for the rest of the run. The ligand has left its starting pose; whether it stays in the pocket is **not shown by this plot alone**.
- Protein–ligand H-bonds: typically 1–2 (occasionally 3) during the first ~6.5 ns, then mostly 0–1 with intermittent 1–2 windows (around 14–16 ns and 19–21 ns). They are not continuous over the 25 ns run.

---

## Limitations

- One 25 ns trajectory, no replicas, no apo or reference-ligand control. Differences in protein stability cannot be attributed to curcumin.
- Docking scores around −7 kcal/mol are modest, and the docking protocol was not validated (no redocking of a known ligand, no known-inhibitor comparison).
- Curcumin is a frequent hitter in biochemical assays (PAINS-type behavior), so a computational hit needs experimental support.
- MD of this length does not give binding kinetics or free energies.

## Next steps

1. Check pose retention (ligand–pocket distance and contact occupancy) after the ligand RMSD jump.
2. Validate docking (redock a co-crystal ligand, compare with a known Hsp70 NBD inhibitor).
3. Repeat the MD with replicas and an apo control; then estimate binding free energy (e.g., MM-GBSA).

## Software

GROMACS 2023.3, AutoDock Vina 1.2.x, acpype 2022.7.21, OpenBabel 3.1.x, PyMOL, R (ggplot2/base plotting), SwissADME, ADMETlab 3.0, PrankWeb (P2RANK), PLIP. Run on Ubuntu 22.04 under WSL2.

## Citation

Beshoy Essam Saber. *In Silico Evaluation and 25 ns Molecular Dynamics Simulation of Human Hsp70–Curcumin Complex.* Zenodo, https://doi.org/10.5281/zenodo.23085117

## License

⚠️ *TODO: add a LICENSE file (e.g., MIT for code and scripts).*
