# TDA_Pangenomics

# Extracción de datos

Subset
- Clavibacter Michiganensis Nebraskensis 1097677.33
- Clavibacter Michiganensis Nebraskensis 31963.54
- Clavibacter Michiganensis Nebraskensis 31963.56

# Blast

Concatenar archivos y correr BLAST con la opción de `prot` porque son Pangenomas.

`cat ~/blast_Nebraskensis/data/*.faa > all-genomes.faa`

`mkdir database
makeblastdb -in ~/blast_Nebraskensis/data/all-genomes.faa -dbtype prot -out ~/blast_Nebraskensis/database/all-genomes
`
Correr `blastp` con cada uno de los archivos

`nohup blastp -query ~/blast_Nebraskensis/data/31963.56.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/31963.56.blast &

nohup blastp -query ~/blast_Nebraskensis/data/31963.54.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/31963.54.blast &

nohup blastp -query ~/blast_Nebraskensis/data/1097677.33.faa -db ~/blast_Nebraskensis/database/all-genomes -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore" > ~/blast_Nebraskensis/output-blast/1097677.33.blast &
`

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

# Construir filtración de simplejos

# Resultados

Se detectan las mismas familias que con get_homologues



