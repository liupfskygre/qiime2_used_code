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

# interactive barplots, Archaea
```
qiime taxa barplot --i-table sample-filtered-table_clean_Arc.qza \
--i-taxonomy taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file may-sept_merged_mapping_table_clean.txt \
--o-visualization liu_taxa-bar-plots_clean_arc.qzv
```
#
```
qiime tools view liu_taxa-bar-plots_clean_arc.qzv 
```
#Sort Samples By, addd level to order the samples

*bacteria
```
qiime taxa filter-table \
  --i-table sample-filtered-table_clean.qza \
  --i-taxonomy taxonomy.dada2.silva132.250_nospace.qza  \
  --p-exclude Archaea \
  --o-filtered-table sample-filtered-table_clean_Bac.qza
  
 #--p-include p__ \ #include things
```

# interactive barplots, bacteria
```
qiime taxa barplot --i-table sample-filtered-table_clean_Bac.qza \
--i-taxonomy taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file may-sept_merged_mapping_table_clean.txt \
--o-visualization liu_taxa-bar-plots_clean_Bac.qzv
```
#
```
qiime tools view liu_taxa-bar-plots_clean_Bac.qzv 
```

#August-Bac-D1-D6 (All D1 in August, to see ecosites effects)
```
grep -e '#\|Aug' may-sept_merged_mapping_table_clean.txt > August_all_mapping_table_clean.txt
for dep in D1 D2 D3 D4 D5 D6
do
mkdir August_Bac_"${dep}"
cd August_Bac_"${dep}"
grep -E "#|$dep" ../August_all_mapping_table_clean.txt > August_mapping_table_clean.txt
cd ..
done

for dep in D1 D2 D3 D4 D5 D6
do
cd August_Bac_"${dep}"
#creat a new clean table
qiime feature-table filter-samples \
  --i-table ../sample-filtered-table_clean_Bac.qza \
  --m-metadata-file August_mapping_table_clean.txt \
  --o-filtered-table August_feature_Bac.qza
  
# interactive barplots, clean
qiime taxa barplot --i-table August_feature_Bac.qza \
--i-taxonomy ../taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file August_mapping_table_clean.txt   \
--o-visualization August_feature_Bac.qzv
cd ..
done

#/Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne

```
#August-Arc-D1-D6 (All D1 in August, to see ecosites effects)
```
grep -e '#\|Aug' may-sept_merged_mapping_table_clean.txt > August_all_mapping_table_clean.txt
for dep in D1 D2 D3 D4 D5 D6
do
mkdir August_"${dep}"
cd August_"${dep}"
grep -E "#|$dep" ../August_all_mapping_table_clean.txt > August_mapping_table_clean.txt
cd ..
done

for dep in D1 D2 D3 D4 D5 D6
do
cd August_"${dep}"
#creat a new clean table
qiime feature-table filter-samples \
  --i-table ../sample-filtered-table_clean_Arc.qza \
  --m-metadata-file August_mapping_table_clean.txt \
  --o-filtered-table August_feature_Arc.qza
  
# interactive barplots, clean
qiime taxa barplot --i-table August_feature_Arc.qza \
--i-taxonomy ../taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file August_mapping_table_clean.txt   \
--o-visualization August_feature_Arc.qzv
cd ..
done
```


## D1-D6 for each month
** bacteria **
#Bac-D1-D6 (All D1 in All month)

```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne # start here

#grep -e '#\|Aug' may-sept_merged_mapping_table_clean.txt > August_all_mapping_table_clean.txt
for dep in D1 D2 D3 D4 D5 D6
do
mkdir Bac_"${dep}"
cd Bac_"${dep}"
grep -E "#|$dep" ../may-sept_merged_mapping_table_clean.txt > D_mapping_table_clean.txt
cd ..
done

for dep in D1 D2 D3 D4 D5 D6
do
cd Bac_"${dep}"
#creat a new clean table
qiime feature-table filter-samples \
  --i-table ../sample-filtered-table_clean_Bac.qza \
  --m-metadata-file D_mapping_table_clean.txt \
  --o-filtered-table D_feature_Bac.qza
  
# interactive barplots, clean
qiime taxa barplot --i-table D_feature_Bac.qza \
--i-taxonomy ../taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file D_mapping_table_clean.txt   \
--o-visualization D_feature_Bac.qzv
cd ..
done
```

** Archaea **
#Arc-D1-D6 (All D1 in All month)

```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne # start here

for dep in D1 D2 D3 D4 D5 D6
do
mkdir Arc_"${dep}"
cd Arc_"${dep}"
grep -E "#|$dep" ../may-sept_merged_mapping_table_clean.txt > D_mapping_table_clean.txt
cd ..
done

for dep in D1 D2 D3 D4 D5 D6
do
cd Arc_"${dep}"
#creat a new clean table
qiime feature-table filter-samples \
  --i-table ../sample-filtered-table_clean_Arc.qza \
  --m-metadata-file D_mapping_table_clean.txt \
  --o-filtered-table D_feature_Arc.qza
  
# interactive barplots, clean
qiime taxa barplot --i-table D_feature_Arc.qza \
--i-taxonomy ../taxonomy.dada2.silva132.250_nospace.qza \
--m-metadata-file D_mapping_table_clean.txt   \
--o-visualization D_feature_Arc.qzv
cd ..
done
```

#/Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne


# Section 4: ANCOM; analysis of composition of microbiomes #

*link
https://docs.qiime2.org/2018.11/tutorials/filtering/

https://github.com/LangilleLab/microbiome_helper/wiki/Amplicon-SOP-v2-(qiime2-2018.6)

#pseudocount of 1 first needs to be added

** Bacteria, all month, all Depth **
```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
# creat pseudo-count table
for dep in D1 D2 D3 D4 D5 D6
do
cd Bac_"${dep}"

qiime composition add-pseudocount --i-table D_feature_Bac.qza \
--p-pseudocount 1 \
--o-composition-table D_table_filt_pseudocount.qza
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
done
```

**Bacteria, on by month**
```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
# do ANCOM analysis on month
for dep in D1 D2 D3 D4 D5 D6
do
cd Bac_"${dep}"
qiime composition ancom --i-table D_table_filt_pseudocount.qza \
--m-metadata-file D_mapping_table_clean.txt \
--m-metadata-column month \
--output-dir ancom_output_month
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
done
                                  
#CATEGORY which category used for groups, or what you want to compare
```

**Archaea, on by month**
#pseudocount of 1 first needs to be added

**archaea, all month, all Depth **
```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
# creat pseudo-count table
for dep in D1 D2 D3 D4 D5 D6
do
cd Arc_"${dep}"
qiime composition add-pseudocount --i-table D_feature_Arc.qza \
--p-pseudocount 1 \
--o-composition-table D_table_filt_pseudocount.qza
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
done
```

```
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
# do ANCOM analysis on month
for dep in D1 D2 D3 D4 D5 D6
do
cd Arc_"${dep}"
qiime composition ancom --i-table D_table_filt_pseudocount.qza \
--m-metadata-file D_mapping_table_clean.txt \
--m-metadata-column month \
--output-dir ancom_output_month
cd /Users/pengfeiliu/A_Wrighton_lab/Wetland_project/16SrRNA_ana_Adrienne/Qiime2_out_Adrienne
done
                                  
#CATEGORY which category used for groups, or what you want to compare
```

# questions # 
*how to convert from a table to qza?*

