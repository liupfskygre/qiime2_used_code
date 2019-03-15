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


**filtering samples and view**
*link
https://docs.qiime2.org/2018.11/tutorials/filtering/

*


*Identifier-based filtering
#metatable to samples-to-keep.tsv , keep only rows with samples we need in the 
#samples-to-remove.list, remaining should contain 630 samples

```
##sample_id_all_merged_mapping.txt, 728

#low number samples, from R, 10
#reseq, first round, from R, 7
grep -e 'Sept-B' sample_id_all_merged_mapping.txt >new.txt # 6
grep -e '-W-' sample_id_all_merged_mapping.txt >>new.txt # 30
grep -e 'D9' sample_id_all_merged_mapping.txt >>new.txt #1
grep -e '-air-water' sample_id_all_merged_mapping.txt >>new.txt # 3
grep -e 'MPP' sample_id_all_merged_mapping.txt >>new.txt #36
grep -e 'S000036' sample_id_all_merged_mapping.txt #0

grep -e '-soil-water' sample_id_all_merged_mapping.txt  # 3

#632 left? not like in R 630 samples??
```


```
grep -v -f sample-to-remove.txt may-sept_merged_mapping_table.txt > may-sept_merged_mapping_table_clean.txt #
grep -c '.*' may-sept_merged_mapping_table_clean.txt
```

```
#creat a new clean table
qiime feature-table filter-samples \
  --i-table filtered_ge10_table.qza \
  --m-metadata-file may-sept_merged_mapping_table_clean.txt \
  --o-filtered-table sample-filtered-table_clean.qza

# interactive barplots, clean
qiime taxa barplot --i-table sample-filtered-table_clean.qza \
--i-taxonomy taxonomy.dada2.silva132.250.qza \
--m-metadata-file may-sept_merged_mapping_table_clean.txt \
--o-visualization liu_taxa-bar-plots_clean.qzv

#
qiime tools view liu_taxa-bar-plots_clean.qzv 
```
*separate archaeal and bacteria

*archaea
```
qiime taxa filter-table \
  --i-table sample-filtered-table_clean.qza \
  --i-taxonomy taxonomy.dada2.silva132.250_nospace.qza  \
  --p-exclude Bacteria \
  --o-filtered-table sample-filtered-table_clean_Arc.qza
  
  #error, white space in taxonomy.dada2.silva132.250.qza file
  #with fixed taxonomy
  --p-include p__ \ #include things
```

*fix taxonomy table
```
#fix taxonomy.qza, https://forum.qiime2.org/t/qiime-taxa-filter-table-error/3947/5
qiime tools export --input-path taxonomy.dada2.silva132.250.qza \
  --output-path taxonomy-with-spaces
qiime metadata tabulate \
  --m-input-file taxonomy-with-spaces/taxonomy.tsv  \
  --o-visualization taxonomy-as-metadata.qzv
qiime tools export --input-path taxonomy-as-metadata.qzv \
  --output-path taxonomy-as-metadata
qiime tools import \
  --type 'FeatureData[Taxonomy]' \
  --input-path taxonomy-as-metadata/metadata.tsv \
  --output-path taxonomy.dada2.silva132.250_nospace.qza
```



**Section 4: ANCOM; analysis of composition of microbiomes
