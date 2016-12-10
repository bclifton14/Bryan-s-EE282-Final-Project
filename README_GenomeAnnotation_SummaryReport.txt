#Genome Annotation Summary Report
#All files in directory "Annotation"
#For answers, less file "Annotation/READMEs_Summaries_Plots/GenomeAnnotation_SummaryReport.txt"


#Methods used to summarize the D. melanogaster Genome Annotation

#Preparing the environment and getting Genome Annotation Data from FlyBase
##loads bioawk,etc
module load jje/jjeutils/0.1a
##loads faSize, etc
module load jje/kent/2014.02.19
##gets the zipped file of the Genome Annotation (GFF) from FlyBase
wget ftp://ftp.flybase.net/genomes/Drosophila_melanogaster/dmel_r6.13_FB2016_05/gtf/dmel-all-r6.13.gtf.gz	
##unzips the file
gunzip dmel-all-r6.13.gtf.gz	
##renames the file
mv dmel-all-r6.13.gtf.gz FullAnnotation.txt	

#Calculating total number of features of each type, sorted form most to least common
##gets just the features
cut -f3 < FullAnnotation.txt > Feature.txt	
##groups features for counting
sort Feature.txt > SortFeature.txt	
##finds number of each features
uniq -c SortFeature.txt > UniqFeature.txt	
##sorts from greatest to least
sort -rn UniqFeature.txt > FeatureCount.txt	
##shows answer
less FeatureCount.txt	

#Calculating total number of genes per chromosome arm
##gets just the chromosomes and features
cut -f1,3 FullAnnotation.txt > ChrFeature.txt 
##gets just the genes and pseudogenes
grep 'gene' ChrFeature.txt > ChrGeneP.txt 
##gets rid of pseudogenes
grep -v 'pseudo' ChrGeneP.txt > ChrGene.txt 
##removes the word gene from the file
tr -d 'gene' < ChrGene.txt > ChrGene2.txt 
##sorts by chromosome arm
sort ChrGene2.txt > SChrGene.txt 
##counts number of genes per chromosome arm
uniq -c SChrGene.txt > UChrGene.txt 
##gives number of genes per chromosome arm from least to greatest
sort -rn UChrGene.txt > GenesChr.txt 
##shows answer
less GenesChr.txt 


#Bash script for displaying Annotation Summary Report all nice and such
##write bash script in nano
nano bash_GenomeAnnotation.sh
##make script executable
chmod +x bash_GenomeAnnotation.sh
##use script to make Genome Annotation Summary Report
bash bash_GenomeAnnotation.sh > GenomeAnnoation_SummaryReport.txt

##bash script follows
#!/usr/bin/env bash
echo -e "There are this many of each feature in this annotation file:\n$(bioawk '{print}' FeatureCount)" 
echo -e "There are this many genes per chromosome arm in this annotation file:\n$(bioawk '{print}' GenesChr)"

