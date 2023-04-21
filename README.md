# Fish
GEN 811 Project: Metabarcoding to compare fish species across US estuaries

Anders Sonnesyn

# # # # LAB 9 # # # #
# 04/21/23

# ssh into ron
# Metabarcoding to compare fish species across US estuaries
/tmp/gen711_project_data/fish/fastqs

cd Fish/
trouch testfile.txt
echo 'a test' > testfile.txt
ls
git add testfile.txt
git commit -m
git push
# Did not do any of the commands above

# Step 1:
cp /tmp/gen711_project_data/fastp.sh ~/fastp.sh
chmod +x ~/fastp.sh
# ~ will apply the command to your 'home' directory

cd ~
mkdir trimmed_fastqs
# <path to github directory>/fastp.sh <1.poly-g length> <1.path to fastq directory>  <3.path to your output directory>
# Did not do the step in the comment above this line
./fastp.sh 150 /tmp/gen711_project_data/fish/fastqs trimmed_fastqs

# Step 2:

conda env list
conda activate qiime2-2022.8


# HAVE NOT DONE ANY STEPS BELOW, GOT AN ERROR WHEN ENTERING THE QIIME TOOLS IMPORT STEP
qiime tools import \
   --type "SampleData[PairedEndSequencesWithQuality]"  \
   --input-format CasavaOneEightSingleLanePerSampleDirFmt \
   --input-path <path to your output directory of trimmed fastqs> \
   --output-path <path to an output directory>/<a name for the output files> \

qiime tools import --type "SampleData[PairedEndSequencesWithQuality]" --input-format CasavaOneEightSingleLanePerSampleDirFmt --input-path trimmed_fastqs --output-path trimmed_fastqs/Imported_files

# Step 3:
qiime cutadapt trim-paired \
    --i-demultiplexed-sequences <path to the file from step 2> \
    --p-cores 4 \
    --p-front-f <the forward primer sequence> \
    --p-front-r <the reverse primer sequence> \
    --p-discard-untrimmed \
    --p-match-adapter-wildcards \
    --verbose \
    --o-trimmed-sequences <path to an output directory>/<name for the output files>.qza

qiime demux summarize \
--i-data <path to the file from step above> \
--o-visualization  <path to an output directory>/<a name for the output files>.qzv 

# Denoising step:
qiime dada2 denoise-paired \
    --i-demultiplexed-seqs qiime_out/${run}_demux_cutadapt.qza  \
    --p-trunc-len-f ${trunclenf} \
    --p-trunc-len-r ${trunclenr} \
    --p-trim-left-f 0 \
    --p-trim-left-r 0 \
    --p-n-threads 4 \
    --o-denoising-stats <path to an output directory>/denoising-stats.qza \
    --o-table <path to an output directory>/feature_table.qza \
    --o-representative-sequences <path to an output directory>/rep-seqs.qza

qiime metadata tabulate \
    --m-input-file <path to an output directory>/denoising-stats.qza \
    --o-visualization <path to an output directory>/denoising-stats.qzv

qiime feature-table tabulate-seqs \
        --i-data <path to an output directory>/rep-seqs.qza \
        --o-visualization <path to an output directory>/rep-seqs.qzv


# # # # END OF LAB 9 # # # #
