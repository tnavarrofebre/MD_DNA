# MD_DNA
DNA in water Molecular Dynamic  
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/tnavarrofebre/MD_DNA)

## Preparation

Download File B-DNA--> 2BNA.pdb
-----------------------------------
https://www.rcsb.org/structure/2bna
-----------------------------------

To remove the waters from B-DNA. These waters come from the crystal structure.
-------------------------------------
grep -v HOH 2BNA.pdb > 2BNAlimpio.pdb
-------------------------------------

The objective now is to build three files:
1- The molecule's topology file
2- The restrained positions file
3- A post-processed structure file
The topology file (usually named topol.top by default) contains:
* Non-bonded parameters: charge, mass, etc.
* Bonded parameters: angles, dihedrals, bonds, etc.
---------------------------------------------------------------
gmx pdb2gmx -f 2BNAlimpio.pdb -o 2BNAprocesado.gro -water spce
---------------------------------------------------------------
Choose 
4: AMBER99 protein, nucleic AMBER94 (Wang et al., J. Comp. Chem. 21, 1049-1074, 2000)
GENERA LOS SIGUIENTES ARCHIVOS:
_______________________________
2BNAprocesado.gro
topol.top
topol_DNA_chain_A.itp
topol_DNA_chain_B.itp
posre_DNA_chain_A.itp
posre_DNA_chain_B.itp
_______________________________