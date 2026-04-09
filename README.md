# Alternariaeffectors
Alternaria effectors
ern jobs submit --name=Alternariaeffectors --threads=8 --memory=16gb --hours=8 --inplace --module='fastqc/1.0_4fa3c56' --command=fastqc -- fastqc *.fq
ern jobs submit --name=Alternariaeffectors --threads=8 --memory=16gb --hours=8 --inplace --module='fastp/1.0_2097d02' --command=fastp -- fastp -i As_20_1.fq -I As_20_2.fq -o As_20_1.clean.fq -O As_20_2.clean.fq
ern jobs submit --name=spades.py_13 --threads=8 --memory=128gb --hours=100 --inplace --module='spades/2.0_36268fe python=3.13' --command="spades.py --careful -1 Aa_14_L1.clean.fq -2 Aa_14_L2.clean.fq -o Aa_14_spades -t 8 -m 120"
for f in *.fasta
do
  ern jobs submit \
    --name=aug_${f%.fasta} \
    --threads=1 \
    --memory=8gb \
    --hours=24 \
    --input="$f" \
    --module="augustus/1.0_e86bc41" \
    --command="augustus \
    --species=aspergillus_nidulans \
    --protein=on \
    $f > ${f%.fasta}.out"
done

Signal P

extract_signalp.py
#!/bin/bash

Effectors only remain with

for f in *.fasta; do
    awk '
    /^>/ {
        header=$0
        getline seq
        getline pred

        if (header ~ /\| SP$/) {
            print header
            print seq
        }
    }
    ' "$f" > "${f%.fasta}_SP_only.fasta"
done

grep -c "^>" *_SP_only.fasta

cat AL*under300.fa > AL_all.fa
# Manually install
wget https://github.com/weizhongli/cdhit/archive/refs/heads/master.zip
unzip master.zip
cd cdhit-master
make
/home/muzhinjin/Alternariaeffectors/Alternariasequences/Proteins/effectorsnotmless300/cdhit-master/cd-hit -i AL_all.fa -o AL_nr.fa -c 0.9 -n 5
