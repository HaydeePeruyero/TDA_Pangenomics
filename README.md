# TDA_Pangenomics

# Extracción de datos

Subset
- Clavibacter Michiganensis Nebraskensis 1097677.33 [link](https://ncbi.nlm.nih.gov/assembly/GCF_000355695.1)
- Clavibacter Michiganensis Nebraskensis 31963.54 [link](https://ncbi.nlm.nih.gov/assembly/GCF_023279165.1)
- Clavibacter Michiganensis Nebraskensis 31963.56 [link](https://ncbi.nlm.nih.gov/assembly/GCF_009739635.2)

# Rast

Anotar los genomas en [rast](https://rast.nmpdr.org/rast.cgi?page=Jobs&logout=1) y descargar los archivos .fasta y .gbk.

# Blast

Concatenar archivos y correr BLAST con la opción de `prot` porque son Pangenomas.

`cat ~/blast_Nebraskensis/data/*.faa > all-genomes.faa`

`mkdir database`

`makeblastdb -in ~/blast_Nebraskensis/data/all-genomes.faa -dbtype prot -out ~/blast_Nebraskensis/database/all-genomes`

Correr `blastp` con cada uno de los archivos

`nohup blastp -query ~/blast_Nebraskensis/data/31963.56.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/31963.56.blast &`

`nohup blastp -query ~/blast_Nebraskensis/data/31963.54.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/31963.54.blast &`

`nohup blastp -query ~/blast_Nebraskensis/data/1097677.33.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/1097677.33.blast &`

  * qseqid: Query Seq-id
  * sseqid: Subject Seq-id
  * pident: Percentage of identical matches
  * length: Alignment length
  * mismatch Number of mismatches
  * gapopen: Number of gap openings
  * qstart: Start of alignment  in query
  * qend: End of alignment in query
  * sstart: Start of alignment in subject
  * send: End if alignment in subject
  * evalue: Expect value
  * bitscore: Bit score

Concatenar los outputs

`cat ~/blast_Nebraskensis/output-blast/*.blast > all-blast.blast`

[all-blast.blast](https://drive.google.com/file/d/1oWipwiNpgw_iYnwnnctz-vGTbn7cv1Vv/view?usp=share_link)

[all-genomes.blast](https://drive.google.com/file/d/1ywn-prfhKFMiL8FzZfnYFBNLERZNrTXS/view?usp=share_link)

[all-blast_6genomas.blast](https://drive.google.com/file/d/1tVuMD9wBr4wPShjiUOb_dhUiJb3Wel6p/view?usp=share_link)

# Get_homologues

Correrlo con los algoritmos COG y COML

COG
`get_homologues.pl -d genomes_gbks -G -t 0 -n 16`

COML
`get_homologues.pl -d genomes_gbks -M -t 0 -c -n 16`

Obtenemos familias

`for infile in *.faa; do base=$(basename ${infile} .faa); echo -n "${base},"; cat ${infile} | grep '>' | less -S | cut -d' ' -f1 | cut -d'>' -f2 | awk '{print}' ORS=',' | perl -ple 'chop'; done &> families.csv`

Obtener pancore_matrix

`less -S pancore-matrix.csv | cut -d',' -f1,6 | sort -t ',' -k 2 > ordered-pancore-matrix.csv`

# Crear matriz de distancias

Scrip para generar la matriz de distancia usando el output de blast.

[Script en R](https://drive.google.com/file/d/1X2EHO6JxKU2biYL65jwu7sTLErOnN_0U/view?usp=sharing)

[Matriz 3 Genomas](https://drive.google.com/file/d/1sf3fnX-rSDy7sqMfx1-dFr0cViweQ15M/view?usp=sharing)

[Matriz 6 Genomas](https://drive.google.com/file/d/1UlrFa4zW2SG25vJNuzuMgNrmM3Dk_Rby/view?usp=sharing)

# Construir filtración de simplejos

Scrip en Gudhi para crear la filtración de simplejos y analizar su persistencia.

[Script en python](https://drive.google.com/file/d/1RKq8ZLbdxICwBUf0lfFJRkuyEvoQGJbL/view?usp=share_link)

# Resultados

Se detectan las mismas familias con TDA que con get_homologues y se puede determinar su persistencia y confianza.

![Persistencia de familias](https://github.com/HaydeePeruyero/TDA_Pangenomics/blob/main/images/simplejos_persistencia.png)

# Streptomyces

| ID_Taxonomy | Genero       | Especie        | Subespecie | Sepa                 | Genbank_seq                                                   | Assemby ID      | Rast_id    |
| ----------- | ------------ | -------------- | ---------- | -------------------- | ------------------------------------------------------------- | --------------- | ---------- |
| 227882      | Streptomyces | avermitilis    |            | MA-4680 = NBRC 14893 | BA000030.4                                                    | ASM976v2        | 227882.63  |
| 1901        | Streptomyces | clavuligerus   |            | ATCC 27064           | [CM001015.1](https://www.ncbi.nlm.nih.gov/nuccore/CM001015.1) | GCA_000148465.1 |            |
| 653045      | Streptomyces | violaceusniger |            | Tu 4113              | [CP002994.1](https://www.ncbi.nlm.nih.gov/nuccore/CP002994.1) | ASM14781v3      | 653045.26  |
| 132474      | Streptomyces | rimosus        | rimosus    | DV3                  | [CP100443.1](https://www.ncbi.nlm.nih.gov/nuccore/CP100443.1) | ASM2422305v1    | 132474.59  |
| 100226      | Streptomyces | coelicolor     |            | A3(2)                | [AL645882.2](https://www.ncbi.nlm.nih.gov/nuccore/AL645882.2) | ASM20383v1      | 100226.232 |
| 1343740     | Streptomyces | rapamycinicus  |            | NRRL 5491            | [CP085193.1](https://www.ncbi.nlm.nih.gov/nuccore/CP085193.1) | ASM2429896v1    | 1343740.3  |
| 1901        | Streptomyces | clavuligerus   |            | F1D7                 | [CP065253.1](https://www.ncbi.nlm.nih.gov/nuccore/CP065253.1) | ASM1576777v1    | 1901.63    |

[All-blast-Streptomyces](https://drive.google.com/file/d/11DDh0vuwveYCTOhVIzrXyw4KHQyBXGd1/view?usp=share_link)

# Mycoplasma

| ID_Taxonomy | Genero         | Especie                      | Subespecie  | Sepa        | Genbank_seq | Assemby ID |
| ----------- | -------------- | ---------------------------- | ----------- | ----------- | ----------- | ---------- |
| 347257      | Mycoplasmopsis | Mycoplasmopsis agalactiae    |             | PG2         | CU179680.1  | ASM6360v1  |
| 262722      | Mesomycoplasma | Mesomycoplasma hyopneumoniae |             | 7448        | AE017244.1  | ASM822v1   |
| 272632      | Mycoplasma     | Mycoplasma mycoides          | mycoides SC | PG1         | BX293980.2  | ASM1144v1  |
| 243273      | Mycoplasmoides | Mycoplasmoides genitalium    |             | G37         | L43967.2    | ASM2732v1  |
| 273119      | Ureaplasma     | Ureaplasma parvum            | serovar 3   | ATCC 700970 | AF222894.1  | ASM662v1   |
| 272634      | Mycoplasmoides | Mycoplasmoides pneumoniae    |             | M129        | U00089.2    | ASM2734v1  |
| 1403316     | Mycoplasma     | Mycoplasma parvum            |             | Indiana     | CP006771.1  | ASM47741v1 |

[All-blast-Mycoplasma](https://drive.google.com/file/d/1c4R7YQcdQDY12P9BK7d6I3D1oHBG7NSo/view?usp=share_link)


---
# Equipo

- José María Ibarra Rodríguez
- Adriana Haydeé Contreras Peruyero
- Fernando Altamirano Fernández
- Andrea Chávez Heredia
- Marisol Navarro Miranda
- Cruz Vargas de León
