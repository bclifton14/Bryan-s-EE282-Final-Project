#Genome Annotation Summary Plots
#All files in directory "Annotation"
#For Answers, see .png plots in "READMEs_Summaries_Plots"
#For work, see below (some files may have been created in "READMEs_Summaries_Plots/README_GenomeAnnotationSummaryReport.txt"


#Prepares environment and downloads the data
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


#Generating Transcripts per Gene file
##gets just the features and attributes
cut -f3,9 FullAnnotation.txt > FeatureAttrib.txt	
##gets just the mRNA
grep 'mRNA' FeatureAttrib.txt > mRNAAttrib.txt	
##gets just "FBgnXXXXXXX";
awk '{print $3}' mRNAAttrib > mRNAFBgn.txt 
##sorts the gene IDs for counting
sort mRNAFBgn.txt > SmRNAFBgn.txt	
##counts the number of transcripts per gene
uniq -c SmRNAFBgn.txt > UmRNAFBgn.txt	
##gives number of transcript per gene in order from most to least
sort -rn UmRNAFBgn.txt > Transcripts.txt 
##gives just the number of transcripts per gene
awk '{print $1}' Transcripts.txt > Transcounts.txt 
##shows distribution
less Transcounts.txt

#Generating Transcripts per Gene Plot
##loads R
module load R 
##opens R
R 
##loads ggplot
library(ggplot2) 
##loads data into R
TransGene <- read.table("Transcounts.txt") 
##loads data into ggplot
TG = ggplot(data = TransGene) 
##makes labeled histogram out of data, with a log scale and bin sizes of 1000
TG + geom_histogram(mapping = aes(x=V1), binwidth = 1) + ggtitle("Transcripts per Gene(Dmel)") + xlab("#Transcripts per Gene") + ylab("#Genes")
##saves the plot as 6' x 6' file
ggsave("TranscriptsPerGene.png", width = 6, height = 6)


#Generating Length of Genes File
##gets just the features and lengths
cut -f3,4,5 FullAnnotation.txt > FeatureEnds.txt	
##gets just the Genes and pseudogenes
grep 'gene' FeatureEnds.txt > GeneEndsP.txt	
##removes pseudogenes
grep -v 'pseudogene' GeneEndsP.txt > GeneEnds.txt 
##gets length (by subtracting first from last +1)
awk '{print $3-$2+1}' GeneEnds.txt > GeneLength.txt 
##sorts the lengths
sort -rn GeneLength.txt > GeneLengthDist.txt	
##shows unbinned data for the plot
less GeneLengthDist.txt

#Generating Length of Genes Plot
##loads R
module load R
##opens R
R
##loads ggplot
library(ggplot2)
##loads data into R
GeneLengths <- read.table("GeneLengthDist.txt")
##loads data in ggplot
GL <- ggplot(data=GeneLengths)
##makes a labeled histogram with log10 scale and 1000 bins
GL + geom_histogram(mapping = aes(x=V1), bins = 1000) + scale_x_log10() + ggtitle("Gene Lengths (Dmel)") + xlab("Gene Length (bp)") + ylab("#Genes")
##saves the plot as a 6' x 6' png file
ggsave("GeneLengthPlot.png", width = 6, height = 6)


#Generating Length of Exons File
##gets just the features and lengths 
cut -f3,4,5 FullAnnotation.txt > FeatureEnds.txt
##gets just the exons
grep 'exon' FeatureEnds.txt > ExonEnds.txt
##gets length (by subtracting first from last)
awk '{print $3-$2 +1}' ExonEnds.txt > ExonLength.txt
##sorts the lengths
sort -rn ExonLength.txt > ExonLengthDist.txt 

#Generating Length of Exons Plot
##loads R
module load R
##opens R
R
##loads ggplot
library(ggplot2)
##loads data into R
ExonLengths <- read.table("ExonLengthDist.txt")
##loads data in ggplot
EL <- ggplot(data=ExonLengths)
##makes a labeled histogram with log10 scale and 1000 bins
EL + geom_histogram(mapping = aes(x=V1), bins = 1000) + scale_x_log10() + ggtitle("Exon Lengths (Dmel)") + xlab("Exon Length (bp)") + ylab("# of Exons)
##saves the plot as a 6' x 6' png file
ggsave("ExonLengthPlot.png", width = 6, height = 6)
