# ¿Cuantas lecturas hay en mi archivo FASTQ?

```bash
$ echo $(cat ejemplo_fastq.fastq|wc -l)/4|bc
$ awk '{s++}END{print s/4}' ejemplo_fastq.fastq
```

## Identificar secuencias clave en mi archivo FASTQ.
### ***La opción de grep -A 2 le dará dos líneas después y -B 1 le dará una línea antes de la coincidencia de grep. El comando sed sirve para eliminar las '--' líneas que grep agrega a la salida***

```bash
$ grep -A 2 -B 1 'AGAAAGTTGTCAGTGTCTT' ejemplo_fastq.fastq | sed '/^--$/d'| cat
$ grep -A 2 -B 1 'GTAGG' ejemplo_fastq.fastq | echo $(wc -l)/4|bc
```
## FASTQ a FASTA.
### ***El comando "sed" se puede usar para imprimir selectivamente las líneas deseadas de un archivo, por lo que si imprime la primera y la segunda línea de cada 4 líneas, obtiene el encabezado de secuencia y la secuencia necesaria para el formato fasta.}***

```bash
$ sed -n '1~4s/^@/>/p;2~4p' ejemplo_fastq.fastq > lecturas_1.fasta
$ awk 'BEGIN{P=1}{if(P==1||P==2){gsub(/^[@]/,">");print}; if(P==4)P=0; P++}' ejemplo_fastq.fastq > mis_lecturas.fasta
$ cat ejemplo_fastq.fastq | awk '{if(NR%4==1) {printf(">%s\n",substr($0,2));} else if(NR%4==2) print;}' > file.fasta
```
## Contar encabezados a ficheros FASTA.

```bash
$ grep -c ">" ejemplo_fasta.fasta
$ grep -c ">" ejemplo_fasta.fasta
$ grep -c "^>" ejemplo_fasta.fasta
```
## Identificar únicamente encabezados FASTA.

```bash
$ grep -e ">" ejemplo_fasta.fasta | head
$ awk 'sub(/^>/, "")' ejemplo_fasta.fasta | less
$ cut -d ' ' -f1 ejemplo_fasta.fasta | grep -e ">" | less
```
## Identificar secuencias duplicadas con base al encabezado.

```bash
$ grep ">" ejemplo_fasta.fasta | sort | uniq -d | cat | wc -l
```
### Un paso útil es linealizar sus secuencias (es decir, eliminar el ajuste de secuencia). Esta no es una solución perfecta, ya que sospecho que se podrían evitar algunos pasos, pero funciona bastante rápido, incluso para miles de secuencias.

```bash
$ sed -e 's/\(^>.*$\)/#\1#/' ejemplo_fasta.fasta | tr -d "\r" | tr -d "\n" | sed -e 's/$/#/' | tr "#" "\n" | sed -e '/^$/d' | head
```
