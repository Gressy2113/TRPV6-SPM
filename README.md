# Open-channel block of human TRPV6 by polyamine spermine

This repository contains molecular dynamics (MD) simulation data presented in the article:

> Neuberger, A., Veretenenko, I. I., Shalygin, A., Trofimov, Y. A., Gudermann, T., Chubanov, V., Efremov, R. G., & Sobolevsky, A. I. (2026). Open-channel block of human TRPV6 by polyamine spermine. Nature Communications

# Overview
This project explores spermine binding poses within hTRPV6<sub>Open</sub> (PDB ID: 9CUJ) ion-conducting pore: 
* Pose 1 at the intracellular entrance near residues D580 
* Pose 2 in the central cavity 
* Pose 3 in the selectivity filter near D542/T539

MD simulations were carried out using the CHARMM36 force field [1], the TIP3P water model [2]. The CHARMM General Force Field (CGenFF) was used to generate the spermine topology (+4e charge) [3]. The multi-site CAM model was used for Ca<sup>2+</sup> [4].

CHARMM General Force Field (CGenFF)95,96 was used to generate the spermine topology with a charge of +4.


Cylindrical constraints (10 Å radius, 10<sup>3</sup> kJ/mol/Å<sup>2</sup>) prevented spermine from moving away from the pore axis.

Equilibration steps includes 5×10<sup>4</sup> steps of steepest descent minimization, heating from 5 to 310 K during a 200-ps MD and progressive release of restraints (10 ns of MD run with fixed positions of the protein atoms, 10 ns of MD with fixed positions of the protein backbone, 50 ns of MD with fixed positions of the protein Cα atoms to permit membrane relaxation). Spermine heavy atoms were restrained during all equilibration steps.

Production runs: 500–1500 ns per replica; no transmembrane potential was applied.

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
* Analysis: hydrogen bonds (D-A < 3.5 Å, D-H-A 150–210°), MD-averaged density maps, hydrophobicity/electrostatics maps via Molecular Surface Topography (MST) tool [5,6].


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
* Software: GROMACS 2024.4 [7], PyMOL [8]
* Hardware: GPU cluster

# Simulation command example (production MD-run):
```bash
gmx grompp -f md -c pr3.gro -p system.top -o md.tpr -n grps.ndx -maxwarn 2

gmx mdrun -v -deffnm md -gpu_id $GPU_ID -nt $NT -pin on -pinoffset $PIN_ON -pinstride 1 -nb gpu -bonded gpu -pme gpu -pmefft gpu -update gpu -cpi -cpo -cpt 15
```

# References
[1]: Huang, J. & Mackerell, A. D. CHARMM36 all-atom additive protein force field: Validation based on comparison to NMR data. Journal of Computational Chemistry 34, 2135–2145 (2013). https://doi.org/10.1002/jcc.23354 

[2]: Jorgensen, W. L., Chandrasekhar, J., Madura, J. D., Impey, R. W., & Klein, M. L. (1983). Comparison of simple potential functions for simulating liquid water. The Journal of Chemical Physics, 79(2), 926–935. https://doi.org/10.1063/1.445869

[3]: Vanommeslaeghe, K. et al. CHARMM general force field: A force field for drug-like molecules compatible with the CHARMM all-atom additive biological force fields. Journal of computational chemistry 31, 671–690 (2009). https://doi.org/10.1002/jcc.21367 

[4]: Zhang, A., Yu, H., Liu, C. et al. The Ca2+ permeation mechanism of the ryanodine receptor revealed by a multi-site ion model. Nat Commun 11, 922 (2020). https://doi.org/10.1038/s41467-020-14573-w

[5]: https://model.nmr.ru/cell 

[6]: Trofimov, Y.A., Krylov, N.A., Minakov, A.S. et al. Dynamic molecular portraits of ion-conducting pores characterize functional states of TRPV channels. Commun Chem 7, 119 (2024). https://doi.org/10.1038/s42004-024-01198-z 

[7]: Abraham, M., Alekseenko, A., Basov, V., Bergh, C., Briand, E., Brown, A., Doijade, M., Fiorin, G., Fleischmann, S., Gorelov, S., Gouaillardet, G., Gray, A., Irrgang, M. E., Jalalypour, F., Jordan, J., Kutzner, C., Lemkul, J. A., Lundborg, M., Merz, P., … Lindahl, E. (2024). GROMACS 2024.4 Source code (2024.4). Zenodo. https://doi.org/10.5281/zenodo.14016590

[8]: Schrödinger, L. & DeLano, W. PyMOL (2020), Available at: http://www.pymol.org/pymol



