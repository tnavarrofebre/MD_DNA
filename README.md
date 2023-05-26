# MD_DNA
DNA in water Molecular Dynamic  
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/tnavarrofebre/MD_DNA)

## Preparación

<iframe width="320" height="180" src="https://www.youtube-nocookie.com/embed/FEa2diI2qgA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="1"></iframe>

Descarga el archivo [B-DNA](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNA.pdb)--> [2BNA.pdb](https://www.rcsb.org/structure/2bna)

Elimino las aguas del B-DNA. Estas aguas provienen de la estructuración del cristal.  
Utilizo funcion de bash <i>grep</i> que elimina la LINEA de un archivo de texto plano que contenga la palabra escrita luego de -v. 
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

4: [AMBER99 protein, nucleic AMBER94 (Wang et al., J. Comp. Chem. 21, 1049-1074, 2000)](https://doi.org/10.1002/1096-987X(200009)21:12<1049::AID-JCC3>3.0.CO;2-F)

GENERA LOS SIGUIENTES ARCHIVOS:
* [2BNAprocesado.gro](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNAprocesado.gro) 
* [topol.top](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/#topol.top.1#) 
* [topol_DNA_chain_A.itp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/topol_DNA_chain_A.itp) 
* [topol_DNA_chain_B.itp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/topol_DNA_chain_B.itp) 
* [posre_DNA_chain_A.itp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/posre_DNA_chain_A.itp) 
* [posre_DNA_chain_B.itp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/posre_DNA_chain_B.itp) 

Ahora viene el momento de la solvatación  
1- Definir el tamaño de la caja con editconf, por ahora la voy a hacer pequeña y triclínica.  
____________________________
gmx editconf -f 2BNAprocesado.gro -o 2BNAcaja.gro -c -bt triclinic
-box 3.234  3.650  5.436 -angles 90 90 90
____________________________ 

GENERA LOS SIGUIENTES ARCHIVOS:
* [2BNAcaja.gro](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNAcaja.gro)

Convierto 2BNAcaja.gro a 2BNAcaja.pdb para poder verla con Avogadro
________________________________
gmx editconf -f 2BNAcaja.gro -o 2BNAcaja.pdb
________________________________

GENERA LOS SIGUIENTES ARCHIVOS:
* [2BNAcaja.pdb](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNAcaja.pdb)

![2BNA en caja triclinica de medidas 3.234,3.650,5.436 y angulos 90,90,90](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/imagenes/2BNAcaja.png)

2- Ahora solvato la molecula de ADN con agua spce llenando la caja.  
Tengo que poner el spc216.gro pues es el GRO de agua de tres puntos los parametros del spce los puse en el gmx pdb2gmx [...] -water spce

___________________________
gmx solvate -cp 2BNAcaja.gro -cs spc216.gro -o 2BNAsolv.gro -p topol_solv.top
___________________________

GENERA LOS SIGUIENTES ARCHIVOS:
* [topol.top](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/#topol.top.2#)
* [2BNAsolv.gro](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNAsolv.gro)

Ahora es el momento de agregar los iones:  
{color:red}Dado que tengo 12 pares de bases y no tengo grupos fosfato en las puntas hay 22 grupos fosfato. Por la regla de Manning [Manning, 1978](https://doi.org/10.1017/S0033583500002031) se que aproximadamente se van a condensar unos 3/4 de NA, esto es 17 sodios. Esto representa un problema a resolver.{color}
Primero debo decargar un MDP del tutorial lyzosyme, lo llamo [ion.mdp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/ions.mdp) va a generar un archivo TPR para luego poder intercambiar moleculas de agua por iones.

_______________________
gmx grompp -f ions.mdp -c 2BNAsolv.gro -p topol.top -o ions.tpr
_______________________

GENERA LOS SIGUIENTES ARCHIVOS:
* [ions.tpr](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/ion.tpr)
* [mdout.mdp](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/mdout.mdp)

Una vez que genero el ions.tpr puedo reemplazar moleculas de agua con iones y lo dejare neutral con -neutral pero en un futuro en este paso ire modificando la concentracion de iones usando tambien -conc, {color:red}quiza modificando esto pueda partir de 17 iones NA.{color}  
Agrego los iones para neutralizar las 22 cargas de ADN

_____________________________
gmx genion -s ions.tpr -o 2BNAions.gro -p topol.top -pname NA -nname
CL -neutral
_____________________________

Me pide que elija que elementos remplazar uso el 3
Group     3 (            SOL) has  5379 elements

GENERA LOS SIGUIENTES ARCHIVOS:
* [topol.top](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/topol.top)
* [2BNAions.gro](https://github.com/tnavarrofebre/MD_DNA/blob/main/Preparacion/2BNAions.gro)
