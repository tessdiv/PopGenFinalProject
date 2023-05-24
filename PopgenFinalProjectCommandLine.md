All of the codes that I ran in the cluster terminal: 
VCFtools 

Remove non-relevant individuals from text file: 
```
vcftools --gzvcf vcf/chr3_460_540_phased.vcf.gz --keep txt/all_inds.txt --recode --out filtered_inds

``` 
Calculate FST values for all Population Comparisons 
ESN as compare

```
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz   --weir-fst-pop txt/ESN_inds.txt  --weir-fst-pop txt/GWD_inds.txt --out output/ESN_GWD
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz     --weir-fst-pop txt/ESN_inds.txt  --weir-fst-pop txt/LWK_inds.txt  --out output/ESN_LWK 
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/ESN_inds.txt  --weir-fst-pop txt/MSL_inds.txt --out output/ESN_MSL
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/ESN_inds.txt  --weir-fst-pop txt/YRI_inds.txt --out output/ESN_YRI
  grep -v "\-nan" ESN_YRI.weir.fst | awk '!/NaN/' > ESN_YRI_filtered
``` 
GWD as compare 
```
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/GWD_inds.txt  --weir-fst-pop txt/LWK_inds.txt --out output/GWD_LWK
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/GWD_inds.txt  --weir-fst-pop txt/MSL_inds.txt --out output/GWD_MSL
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/GWD_inds.txt  --weir-fst-pop txt/YRI_inds.txt --out output/GWD_YRI
```
LWK as compare 
```
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/LWK_inds.txt  --weir-fst-pop txt/MSL_inds.txt --out output/LWK_MSL
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/LWK_inds.txt  --weir-fst-pop txt/YRI_inds.txt --out output/LWK_YRI
```
MSL as compare 
```
vcftools --gzvcf vcf/filtered_inds.recode.vcf.gz    --weir-fst-pop txt/MSL_inds.txt  --weir-fst-pop txt/YRI_inds.txt --out output/MSL_YRI
```

