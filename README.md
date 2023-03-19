# MD_DNA
DNA in water Molecular Dynamic  
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/tnavarrofebre/MD_DNA)

## Preparación

Descarga el archivo B-DNA--> 2BNA.pdb
https://www.rcsb.org/structure/2bna

ELimino las aguas del B-DNA. Estas aguas provienen de la estructuración del cristal
_____________________________________
grep -v HOH 2BNA.pdb > 2BNAlimpio.pdb
_____________________________________

Ahora el objetivo es construir tres archivos:

1- la topologia de la molecula

2- el archivo de posiciones restringidas

3- un archivo de estructura postprocesado

el archivo de la topologia (topol.top por defecto) contiene:
* parametros de no ligadura: carga, masa, etc
* parametro de ligadura: angulos, dihedros, enlaces, etc

_____________________________________
gmx pdb2gmx -f 2BNAlimpio.pdb -o 2BNAprocesado.gro -water spce
_____________________________________
Elijo

4: AMBER99 protein, nucleic AMBER94 (Wang et al., J. Comp. Chem. 21, 1049-1074, 2000)

GENERA LOS SIGUIENTES ARCHIVOS:
* 2BNAprocesado.gro
* topol.top
* topol_DNA_chain_A.itp
* topol_DNA_chain_B.itp
* posre_DNA_chain_A.itp
* posre_DNA_chain_B.itp
