# Домашнее задание №1
Кривулец Нина 

# Команды в терминале:

1. Создаю ссылки
```
ls /usr/share/data-minor-bioinf/assembly/* | xargs -t -I{} ln -s {}
```

2. Выбираю случайные чтения c random seed = 113:
```
seqtk sample -s113 oil_R1.fastq 5000000 > sub1.fastq

seqtk sample -s113 oil_R2.fastq 5000000 > sub2.fastq

seqtk sample -s113 oilMP_S4_L001_R1_001.fastq 1500000 > matep1.fastq

seqtk sample -s113 oilMP_S4_L001_R2_001.fastq 1500000 > matep2.fastq
```
3. Создаю папки fastqc, multiqc
```
mkdir fastqc
mkdir multiqc
```
4. Оцениваю файлы через fastqc и делаю отчет с multiqc
```
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}
multiqc -o multiqc fastqc
```
5. Подрезаю чтения
```
platanus_trim sub*
platanus_internal_trim matep*   
```
6. оцениваю подрезанные чтения и создаю отчет (в папках fastqc_trimmed, multiqc_trimmed) 
```
mkdir fastqc_trimmed
mkdir multiqc_trimmed
ls sub* matep*| xargs -tI{} fastqc -o fastqc_trimmed {}
multiqc -o multiqc_trimmed fastqc_trimmed
```
7. Запускаю сбор контиг
```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log   
```
8. Запускаю сбор скаффолдов
```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> scaffold.log
```
9. Уменьшаю количество промежутков
```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matep1.fastq.int_trimmed matep2.fastq.int_trimmed 2> gapclose.log
```
10. С помощью команды rm удаляю лишние файлы
   
