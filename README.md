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

---
# Equipo

- José María Ibarra Rodríguez
- Adriana Haydeé Contreras Peruyero
- Fernando Altamirano Fernández
- Andrea Chávez Heredia
- Marisol Navarro Miranda
- Cruz Vargas de León
