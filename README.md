# HSP70 Molecular Docking - CADD
Molecular Dynamics Simulation profiles for Hsp70-Curcumin complex using GROMACS.


## 🧬 Project Workflow & Methodology

The project follows a comprehensive computer-aided drug design (CADD) and molecular dynamics (MD) simulation pipeline to evaluate the stability of the Hsp70-Curcumin complex:

### 1. Structure Preparation & Target Selection
* **Target Protein:** Human Heat Shock Protein 70 (HSP70 / HSPA1A) retrieved from the PDB database.
* **Ligand Preparation:** Curcumin structure retrieved, optimized, and parameterized using appropriate force fields to generate proper topology profiles.

### 2. Molecular Docking (CADD)
* Rigid and flexible docking simulations executed to identify the optimal binding pose, lowest binding energy ($kcal/mol$), and key amino acid interactions within the HSP70 nucleotide-binding domain (NBD) or substrate-binding domain (SBD).

### 3. Molecular Dynamics (MD) Simulation Setup (GROMACS)
* **System Solvation:** The protein-ligand complex is centered in a cubic box and solvated with explicit water models (e.g., SPC/E or TIP3P).
* **Charge Neutralization:** Counter-ions ($Na^+$ / $Cl^-$) added to neutralize the net charge of the system.
* **Energy Minimization:** Conducted using the Steepest Descent algorithm to remove steric clashes and optimize structural geometry.
* **Equilibration:** 
  * **NVT Ensemble:** Constant Number of particles, Volume, and Temperature to stabilize system temperature.
  * **NPT Ensemble:** Constant Number of particles, Pressure, and Temperature to stabilize system density.

### 4. Production MD Run
* A production run of **25 ns** ($12,500,000$ steps) is executed on Linux/WSL environment to capture the dynamic behavior and conformational changes of the complex.

### 5. Post-Simulation Trajectory Analysis (Upcoming)
* Evaluation of structural stability and flexibility via:
  * Root Mean Square Deviation (RMSD)
  * Root Mean Square Fluctuation (RMSF)
  * Radius of Gyration (Rg)
  * Hydrogen Bond (H-bonds) profiling over the simulation trajectory.
