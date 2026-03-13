# Open-channel block of human TRPV6 by polyamine spermine

This repository contains molecular dynamics (MD) simulation data presented in the article:

> Neuberger, A., Veretenenko, I. I., Shalygin, A., Trofimov, Y. A., Gudermann, T., Chubanov, V., Efremov, R. G., & Sobolevsky, A. I. (2026). Open-channel block of human TRPV6 by polyamine spermine. Nature Communications

# Overview
* This project explores spermine binding poses within hTRPV6_Open (PDB ID: 9CUJ): Pose 1 at the intracellular entrance near D580; Pose 2 in the central cavity; and Pose 3 in the selectivity filter with D542/T539. 

* Simulations use the CHARMM36m force field, the TIP3P water model and the CGenFF model for spermine (+4e charge). The multi-site CAM model is used for Ca<sup>2+</sup> [1].

* Cylindrical constraints (10 Å radius, 10<sup>3</sup> kJ/mol/Å<sup>2</sup>) confine spermine near the pore axis.

* Equilibration includes 5×10<sup>4</sup> steps of steepest descent minimization, heating from 5 to 310 K during a 200-ps MD and progressive release of restraints (10 ns of MD run with fixed positions of the protein atoms, 10 ns of MD with fixed positions of the protein backbone, 50 ns of MD with fixed positions of the protein Cα atoms to permit membrane relaxation). Spermine heavy atoms were restrained during all equilibration steps.

* Production runs: 500–1500 ns per replica; no transmembrane potential was applied.

# MD Simulation Setups
| Setup# | Number of spermine molecules | Spermine starting position | Ca2+ starting position | Spermine finishing position | Simulation time (ns) |
|--------|------------------------------|----------------------------|------------------------|-----------------------------|----------------------|
| SETUP1 | 1                            | bulk water                 | no                     | 4×Pose 3<br>1×Pose 1        | 3×500<br>2×1000      |
| SETUP2 | 1                            | bulk water                 | SF                     | 3×Pose 1                    | 3×1000               |
| SETUP3 | 1                            | Pose 2                     | SF                     | 3×Pose 3                    | 3×500                |

# Key observations:

* Pose 3 (SF) dominates in Ca<sup>2+</sup>-free setups, matching cryo-EM density.
* Ca<sup>2+</sup> in SF blocks progression to Pose 3 unless spermine starts in Pose 2.
* Mutants: T539V similar to WT; D580R prevents pore entry.
* Analysis: H-bonds (D-A < 3.5 Å, D-H-A 150–210°), MD-averaged density maps, hydrophobicity/electrostatics maps via MST tool.


# Repository Structure
```
TRPV6-SPM/
├── SETUP1/  
    ├── REPLICA1/     
        ├── SETUP1/    
            ├──system.top #system topology
            ├──grps.ndx #system index file
            ├──pr3.gro #fully equilibrated system, initial configuration for production MD-run
            ├──md.mdp # 
            ├──md.tpr #.tpr file for MD-run
            ├──md.gro # final coordinates of the system after MD-run
            ├──state_pdb/ #coordinates of Protein, Spermine and ions during MD-run with Δt=100000 ps
                ├──state_t_0.pdb
                ├──state_t_100000.pdb
                ├──...

```

# Requirements
* Software: GROMACS 2024.4, PyMOL
* Hardware: GPU cluster (e.g., ~160×160×120 Å³ box)

# Simulation command example (production MD-run):
```bash
gmx grompp -f md -c pr3.gro -p system.top -o md.tpr -n grps.ndx -maxwarn 2

gmx mdrun -v -deffnm md -gpu_id 0 -nt 24 -pin on -pinoffset 0 -pinstride 1 -nb gpu -bonded gpu -pme gpu -pmefft gpu -update gpu -cpi -cpo -cpt 15
```

# References
1. `CAM model` Zhang, A., Yu, H., Liu, C. et al. The Ca2+ permeation mechanism of the ryanodine receptor revealed by a multi-site ion model. Nat Commun 11, 922 (2020). https://doi.org/10.1038/s41467-020-14573-w
