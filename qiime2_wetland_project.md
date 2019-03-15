## this is a markdown file

# qiiime2 code used in the OWC_wetland_projects2018, 16S

```
# interactive barplots
qiime taxa barplot --i-table filtered_ge10_table.qza \
--i-taxonomy taxonomy.dada2.silva132.250.qza \
--m-metadata-file may-sept_merged_mapping_table.txt \
--o-visualization liu_taxa-bar-plots.qzv
```
#check
```
qiime tools view  liu_taxa-bar-plots.qzv

```


* filtering and view *



