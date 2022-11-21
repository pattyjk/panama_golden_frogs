## Beta Diversity
```
library(vegan)
library(ggplot2)
library(plyr)

#read in metadata
setwd("./Github/panama_golden_frogs/")
meta<-read.delim("Run11_map.txt", header=T)

#read in ASV table
#read in ASV table
asv_table<-read.delim("asv_table.txt", header=T, row.names=1)
dim(asv_table)
#1437   90

#extract negative sample ASVs
asv_table_neg<-as.data.frame(asv_table$neg3)
contam_ASV<-which(rowSums(asv_table_neg)>0)
length(contam_ASV)
#17

#remove contaminated ASVs from analysis
asv_table_no_contam<-asv_table[-contam_ASV,]
dim(asv_table_no_contam)
#1420   91

#remove mock sample and negative control
asv_table_no_contam<-asv_table_no_contam[, -which(names(asv_table_no_contam) %in% c("Mock", "neg3"))]
dim(asv_table_no_contam)
#1420 89

#get sequence counts per sample
asv_col_sums<-colSums(asv_table_no_contam)
min(asv_col_sums)
#11800
max(asv_col_sums)
#345512

#transpose table for analysis
asv_table<-t(asv_table_no_contam)

#rarefy data to 11895
set.seed(1105)
asv_table_rare<-rrarefy(asv_table, sample=11800)

#calculate PCoA based on Bray-curtis and calculate the percent variation explained
asv.pcoa<-capscale(asv_table_rare ~ 1, distance='bray')

#get axis percentages
100*round(asv.pcoa$CA$eig[1]/sum(asv.pcoa$CA$eig), 3)
#15.1
100*round(asv.pcoa$CA$eig[2]/sum(asv.pcoa$CA$eig), 3)
#14.5

#extract the coordinates
asv.scores<-scores(asv.pcoa)
asv.coords<-as.data.frame(asv.scores$sites)
asv.coords$SampleID<-rownames(asv.coords)
asv.coords<-merge(asv.coords, meta, by=c('SampleID'))

#plot it yo
ggplot(asv.coords, aes(MDS1, MDS2, colour=Treatment))+  
  geom_point(aes(size=2))+
  theme_bw()+
  xlab("PC1-15.1%")+
  ylab("PC2-14.5%")+
  theme(text = element_text(size=14),
        axis.text = element_text(size=14), legend.text=element_text(size=14))

```

## Alpha div
```
#calcuate shannon
asv.shan<-diversity(asv_table_rare, index='shannon')

#OTUs observed
asv.otus<-rowSums(asv_table_rare>0)

#Pielou's Evenness
asv.even<-(diversity(asv_table_rare))/asv.otus

#catenate data into a single frame
asv.div<-as.data.frame(cbind(asv.shan, asv.otus, asv.even))

#add sample IDs
asv.div$SampleID<-row.names(asv.div)

#fix coloumn names
names(asv.div)<-c('Shannon', 'OTUs_Obs', 'Pielous_Even', 'SampleID')

#add metadata
asv.div<-merge(asv.div, meta, by='SampleID')
```





