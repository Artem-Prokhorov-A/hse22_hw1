# hse22_hw1

## Прохоров Артём Александрович

### Создаем ссылки
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```
### Выбор случайных чтений
```
seqtk sample -s1121 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s1121 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s1121 oilMP_S4_L001_R1_001.fastq 1500000 > mp1.fastq
seqtk sample -s1121 oilMP_S4_L001_R2_001.fastq 1500000 > mp2.fastq
```
### Оцениваем качество с помощью fastQC
```
mkdir fastqc
ls sub* mp* | xargs -tI{} fastqc -o fastqc {}
```
### Cтатистика по multiQC
```
mkdir multiqc
multiqc -o multiqc fastqc
```
### Подрезаем чтения и удаляем адаптеры
```
platanus_trim sub*
platanus_internal_trim mp*
```
### Удаляем старые файлы
```
rm sub1.fastq
rm sub2.fastq
rm mp1.fastq
rm mp2.fastq
```
### Оцениваем качество с помощью fastQC
```
mkdir fastqc_trimmed
ls sub* mp* | xargs -tI{} fastqc -o fastqc_trimmed {}
```
### Cтатистика по multiQC
```
multiqc -o multiqc_trimmed fastqc_trimmed
```
### Cобираем контиги 
```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
### Собираем сканфолды
```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> scaffold.log
```
### Уменьшаем количество гэпов
```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 mp1.fastq.int_trimmed mp2.fastq.int_trimmed 2> gapclose.log
```

### Удаляем файлы
```
rm sub1.fastq.trimmed
rm sub2.fastq.trimmed
rm mp1.fastq.int_trimmed
rm mp2.fastq.int_trimmed
```
