## panama golden frogs
### Sequence processing

```
source activate qiime2-2022.8

#load data into QIIME
##Run 11
qiime tools import --type EMPSingleEndSequences --input-path ./data --output-path run11_seqs.qza

#julia's already in QZA format so no script needed

#demultiplex reads
qiime demux emp-single \
  --i-seqs becker_seqs.qza \
  --m-barcodes-file Atelopus1_Mapping_File.txt \
  --m-barcodes-column BarcodeSequence \
  --o-per-sample-sequences becker_demux.qza \
  --o-error-correction-details becker_demux-details.qza \
  --p-no-golay-error-correction
  
 #quality filer firsts
qiime quality-filter q-score \
 --i-demux becker_demux.qza \
 --o-filtered-sequences becker_demux-filtered.qza \
 --o-filter-stats becker_demux-filter-stats.qza
 
  #call ASVs with deblur
  qiime deblur denoise-16S \
  --i-demultiplexed-seqs becker_demux-filtered.qza \
  --p-trim-length 120 \
  --o-representative-sequences becker_rep-seqs-deblur.qza \
  --o-table becker_table-deblur.qza \
  --p-sample-stats \
  --o-stats becker_deblur-stats.qza
  
  # convert ASV table
  ```
