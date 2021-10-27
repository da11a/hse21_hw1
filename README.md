# hse21_hw1
**Список всех команд, которые были выполнены на сервере:**
    
    cd ~/Datalog/hw1

    ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

    mkdir fastqc
    mkdir multiqc

    seqtk sample -s722 oil_R1.fastq 5000000 > sub1.fq
    seqtk sample -s722 oil_R2.fastq 5000000 > sub2.fq
    seqtk sample -s722 oilMP_S4_L001_R1_001.fastq 1500000 > sub1_MP.fq
    seqtk sample -s722 oilMP_S4_L001_R2_001.fastq 1500000 > sub2_MP.fq

    screen -S dagilmanova_fastqc
    htop
    ls sub* | xargs -P 1 -tI{} fastqc -o fastqc {}
    multiqc -o multiqc fastqc

    #скачать файлы
    mkdir common
    mv fastqc/sub1_fastqc.zip fastqc/sub2_fastqc.zip fastqc/sub1_MP_fastqc.zip fastqc/sub2_MP_fastqc.zip common
    mv fastqc/sub1_fastqc.html fastqc/sub2_fastqc.html fastqc/sub1_MP_fastqc.html fastqc/sub2_MP_fastqc.html multiqc/multiqc_report.html common
    tar -cvzf common.tar.gz common

    rm -r common.tar.gz

    platanus_trim sub1.fq sub2.fq
    platanus_internal_trim sub1_MP.fq sub2_MP.fq
    
    rm sub1.fq
    rm sub2.fq
    rm sub1_MP.fq
    rm sub2_MP.fq
    
    mkdir trimmed_fastqc
    mkdir trimmed_multiqc
    ls sub* | xargs -P 1 -tI{} fastqc -o trimmed_fastqc {}
    multiqc -o trimmed_multiqc trimmed_fastqc
    
    
    time platanus assemble -o Poil -f sub1.fq.trimmed sub2.fq.trimmed 2> assemble.log
    
    time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fq.trimmed sub2.fq.trimmed -OP2 sub1_MP.fq.int_trimmed sub2_MP.fq.int_trimmed 2> scaffold.log

    time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fq.trimmed sub2.fq.trimmed -OP2 sub1_MP.fq.int_trimmed sub2_MP.fq.int_trimmed 2> gapclose.log

    exit
    exit

    mkdir contig
    mkdir gap
    mv Poil_32merFrq.tsv Poil_contigBubble.fa Poil_contig.fa contig
    mv Poil_gapClosed.fa gap

    rm sub1.fq.trimmed sub2.fq.trimmed sub1_MP.fq.int_trimmed sub2_MP.fq.int_trimmed


    head gap/Poil_gapClosed.fa
    echo scaffold1_cov232 > _tmp.txt
    seqtk subseq gap/Poil_gapClosed.fa _tmp.txt > longest.fa
    
    
**Скриншоты и статистика из файлов multiQC:**

Качество исходных чтений

![image](https://user-images.githubusercontent.com/67833171/139153924-7cee4789-3d55-4b9d-8912-62c07ed43bff.png)

![image](https://user-images.githubusercontent.com/67833171/139154519-8d3c3c3e-5068-4714-b1c1-7e8b46ce8669.png)

Качество подрезанных чтений

![image](https://user-images.githubusercontent.com/67833171/139154319-9174b58e-2a14-49be-8332-4fe258dd1256.png)

![image](https://user-images.githubusercontent.com/67833171/139154577-7c11b1a7-1d5a-4e11-a914-934f03f340de.png)

