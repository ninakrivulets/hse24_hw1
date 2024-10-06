## Домашнее задание №1
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
6. Оцениваю подрезанные чтения и создаю отчет (в папках fastqc_trimmed, multiqc_trimmed) 
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

# Код
Контиги:
```
f = open("Poil_contig.fa")
a = f.readlines()
n = ln = 0
lng = int(a[0][a[0].find('len') + 3:a[0].rfind('_')])
lst = []
for i in a:
    if '>' in i:
        n += 1
        ln += int(i[i.find('len') + 3:i.rfind('_')])
        ltmp = int(i[i.find('len') + 3:i.rfind('_')])
        lst.append(ltmp)
        if ltmp > lng:
          lng = ltmp
lst.sort()
lst.reverse()
summ = j = ind =  0
while summ <= ln/2:
    summ += lst[j]
    ind = j
    j+=1

print(f'Количество контигов: {n}')
print(f'Длина контигов: {ln}')
print(f'Самый длинный контиг: {lng}')
print(f'N50: {lst[ind]}')
```
Количество контигов: 611
Длина контигов: 3925345
Самый длинный контиг: 179304
N50: 50481

Скаффолды:
```
f = open("Poil_scaffold.fa")
a = f.readlines()
n = ln = mx = sm = 0
lng = a[0].split('len')[1].split('_cov')[0]
lst = []
for i in a:
    if '>' in i:
        n += 1
        ln += int(i[i.index('len') + 3:i.index('_cov')])
        ltmp = int(i[i.index('len') + 3:i.index('_cov')])
        lst.append(ltmp)
    for j in i:
        if j == "N":
            sm += 1
lst.sort()
lst.reverse()
summ = j = ind =  0
while summ <= ln/2:
    summ += lst[j]
    ind = j
    j+=1

print(f'Количество скаффолдов: {n}')
print(f'Длина скаффолдов: {ln}')
print(f'Самый длинный скаффолд: {lng}')
print(f'N50: {lst[ind]}')
print(f'Общая длина гэпов до подрезания: {sm}')
```
Количество скаффолдов: 76
Длина скаффолдов: 3876912
Самый длинный скаффолд: 3836144
N50: 3836144
Общая длина гэпов до подрезания: 7566

Скаффолды после подрезания:
```
f = open("Poil_gapClosed.fa")
a = f.readlines()
length = 0
for i in a:
    for j in i:
        if j == "N":
            length += 1
print(f'Общая длина гэпов после подрезания: {length}')
```
Общая длина гэпов после подрезания: 2888




# Отчёт

Изначальные чтения:

<img width="1382" alt="multiqc_report_full_general_stats" src="https://github.com/user-attachments/assets/8e216732-b9d2-429c-a746-3e93a0dd6dec">

<img width="1378" alt="multiqc_report_full_per_seq_quality_score" src="https://github.com/user-attachments/assets/ab9820a5-38c1-4488-8562-99c423079205">

<img width="1388" alt="multiqc_report_full_adapter_content" src="https://github.com/user-attachments/assets/f4a07c22-9fc1-4d17-bc35-ee52187d63f2">

Подрезанные чтения:

<img width="1382" alt="multiqc_trimmed_general_stats" src="https://github.com/user-attachments/assets/4337fe11-ccca-4597-9b6c-ab9e4b2c4482">

<img width="1380" alt="multiqc_trimmed_per_seq_quality_score" src="https://github.com/user-attachments/assets/f0212b95-a7f0-4eba-b8ed-58d74c418d83">

<img width="1379" alt="multiqc_trimmed_adapter_content" src="https://github.com/user-attachments/assets/803ccda4-4e67-4304-a0b2-e8166741a2cc">

## Бонусная часть

возьмем 3 миллиона чтений типа paired-end и 1 миллион чтений типа mate-pairs.

# Код
Контиги:
```
f = open("Poil_contig_b.fa")
a = f.readlines()
n = ln = 0
lng = int(a[0][a[0].find('len') + 3:a[0].rfind('_')])
lst = []
for i in a:
    if '>' in i:
        n += 1
        ln += int(i[i.find('len') + 3:i.rfind('_')])
        ltmp = int(i[i.find('len') + 3:i.rfind('_')])
        lst.append(ltmp)
        if ltmp > lng:
          lng = ltmp
lst.sort()
lst.reverse()
summ = j = ind =  0
while summ <= ln/2:
    summ += lst[j]
    ind = j
    j+=1

print(f'Количество контигов: {n}')
print(f'Длина контигов: {ln}')
print(f'Самый длинный контиг: {lng}')
print(f'N50: {lst[ind]}')
```
Количество контигов: 623
Длина контигов: 3920635
Самый длинный контиг: 141991
N50: 61136

Скаффолды:
```
f = open("Poil_scaffold_b.fa")
a = f.readlines()
n = ln = mx = sm = 0
lng = a[0].split('len')[1].split('_cov')[0]
lst = []
for i in a:
    if '>' in i:
        n += 1
        ln += int(i[i.index('len') + 3:i.index('_cov')])
        ltmp = int(i[i.index('len') + 3:i.index('_cov')])
        lst.append(ltmp)
    for j in i:
        if j == "N":
            sm += 1
lst.sort()
lst.reverse()
summ = j = ind =  0
while summ <= ln/2:
    summ += lst[j]
    ind = j
    j+=1

print(f'Количество скаффолдов: {n}')
print(f'Длина скаффолдов: {ln}')
print(f'Самый длинный скаффолд: {lst[0]}')
print(f'N50: {lst[ind]}')
print(f'Общая длина гэпов до подрезания: {sm}')
```
Количество скаффолдов: 72
Длина скаффолдов: 3875120
Самый длинный скаффолд: 2763385
N50: 2763385
Общая длина гэпов до подрезания: 7897

Скаффолды после подрезания:
```
f = open("Poil_gapClosed_b.fa")
a = f.readlines()
length = 0
for i in a:
    for j in i:
        if j == "N":
            length += 1
print(f'Общая длина гэпов после подрезания: {length}')
```
Общая длина гэпов после подрезания: 3355

   
# Отчёт

Изначальные чтения:

<img width="1385" alt="multiqc_b_gen_stats" src="https://github.com/user-attachments/assets/bc4ef05e-7954-4df9-9458-ef75032d944c">

<img width="1380" alt="multiqc_b_per_seq_quality" src="https://github.com/user-attachments/assets/9cf8c36d-9b57-4407-9b29-e57715e31f7b">

<img width="1381" alt="multiqc_b_adapter_content" src="https://github.com/user-attachments/assets/dff18eb6-46cf-4571-b6b1-0b8ecd56a31f">

Подрезанные чтения:

<img width="1383" alt="multiqc_trimmed_b_gen_stats" src="https://github.com/user-attachments/assets/630f10a0-fefc-4856-af47-ada6257925fd">

<img width="1381" alt="multiqc_trimmed_b_per_seq_quality" src="https://github.com/user-attachments/assets/aa906580-7caf-4667-a1cc-7f9c824e14c9">

<img width="1393" alt="multiqc_trimmed_b_adapter_content" src="https://github.com/user-attachments/assets/eae0a720-b2a0-4672-adb7-941a491b9991">

# Итог

При меньшем количестве чтений качество сборки хуже, так как становится больше гэпов и длина самого длинного скаффолда уменьшается.
Также на графиках в отчете видно, что качество первой сборки лучше.
