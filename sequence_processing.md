## panama golden frogs
### Sequence processing

```
#load QIIME 2022.8
source activate qiime2-2022.8

#load raw FASTQ reads into QIIME
qiime tools import --type EMPSingleEndSequences --input-path ./data --output-path run11_seqs.qza

#demultiplex reads
qiime demux emp-single \
  --i-seqs run11_seqs.qza \
  --m-barcodes-file Run11_map.txt \
  --m-barcodes-column BarcodeSequence \
  --o-per-sample-sequences Run11_demux.qza \
  --o-error-correction-details Run11_demux-details.qza \
  --p-no-golay-error-correction
  
#quality filer
qiime quality-filter q-score \
 --i-demux Run11_demux.qza \
 --o-filtered-sequences Run11_demux-filtered.qza \
 --o-filter-stats Run11_demux-filter-stats.qza
 
  #call ASVs with deblur
  qiime deblur denoise-16S \
  --i-demultiplexed-seqs Run11_demux-filtered.qza \
  --p-trim-length 120 \
  --o-representative-sequences Run11_rep-seqs-deblur.qza \
  --o-table Run11_table-deblur.qza \
  --p-sample-stats \
  --o-stats Run11_deblur-stats.qza
 
 #make phylogenetic tree with fasttree
 qiime phylogeny align-to-tree-mafft-fasttree \
  --i-sequences Run11_rep-seqs-deblur.qza \
  --output-dir phylogeny-align-to-tree-mafft-fasttree
  
  #export tree as NWK format
  qiime tools export --input-path tree.qza --output-path tree
 
#pull trained taxonomy dataset (SILVA 138. Has only 515F/806R region)
wget https://data.qiime2.org/2022.8/common/silva-138-99-515-806-nb-classifier.qza
 
#assign taxonomy to  SILVA with sklearn
qiime feature-classifier classify-sklearn \
  --i-classifier silva-138-99-515-806-nb-classifier.qza \
  --i-reads  run11_seqs-001.qza \
  --o-classification run11_taxonomy.qza


qiime metadata tabulate \
  --m-input-file taxonomy.qza \
  --o-visualization taxonomy.qzv
 
# convert ASV table to biom/text
 
  ```
