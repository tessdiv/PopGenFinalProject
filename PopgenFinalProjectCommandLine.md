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

After this instructions for the rest of the FST tools portion are in the FST notebook. 

RELATE: 

Copy Necessary files: 

``` scp ~/populationgenomics/project_data/chr3region/20140520.chr3.strict_mask.fasta.gz . 
scp ~/populationgenomics/data/relate_data/genetic_map_chr2_combined_b37.txt
scp ~/populationgenomics/data/relate_data/human_ancestor_2.fa
scp ~/populationgenomics/data/relate_data/60_inds.txt
scp ~/populationgenomics/data/relate_data/chr2_130_145_phased.vcf.gz
```

Remove non-relevant individuals from vcf file
```
vcftools --gzvcf vcf/chr3_460_540_phased.vcf.gz --keep txt/all_inds.txt --recode --out filtered_inds
```

Initial Relate run on all populations: 
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3.haps.gz --sample prep.chr3.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate
```
Estimate Population Size for all populations: 
```
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate -m 1.25e-8 --num_iter 1 --poplabels all_inds.txt -o popsize_oneiter --threshold 0 
```

Detect Selecton all populations: 

```
conda activate pg-relate
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_oneiter -m 1.25e-8 --poplabels all_inds.txt -o selection_relate_oneiter
```
Plot interesting trees: 

```
~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps relate/chr3.haps --sample relate/chr3.sample --anc relate/chr3_relate.anc --mut relate/one_iter/popsize_oneiter.mut --poplabels relate/all_inds.txt --years_per_gen 28 -o tree_52196241 --bp_of_interest 52196241


~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps relate/chr3.haps --sample relate/chr3.sample --anc relate/chr3_relate.anc --mut relate/one_iter/popsize_oneiter.mut --poplabels relate/all_inds.txt --years_per_gen 28 -o tree_468300001 --bp_of_interest 46830000
```
Use Relate Notebook for the rest of the all populations relate analysis. 
Repeat steps from above for all populations seperately: 

Make .haps file 
```
~/populationgenomics/software/relate/bin/RelateFileFormats --mode ConvertFromVcf --haps chr3.haps --sample chr3.sample -i filtered_inds.recode
```

ESN:
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_ESN.haps.gz --sample prep.chr3_ESN.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate_ESN
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate_ESN -m 1.25e-8 --poplabels all_inds.txt -o popsize --threshold 0
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_ESN -m 1.25e-8 --poplabels ESN_inds.txt -o selection_relate_ESN
sort -k35r selection_relate_ESN.sele | cut -f 1,2,35 -d ' ' | head -n 10 > top_SNPs_Relate_ESN
```


LWK: 
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_LWK.haps.gz --sample prep.chr3_LWK.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate_LWK
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate_LWK -m 1.25e-8 --poplabels LWK_inds.txt -o popsize_LWK --threshold 0
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_LWK -m 1.25e-8 --poplabels LWK_inds.txt -o selection_relate_LWK
sort -k35r selection_relate_LWK.sele | cut -f 1,2,35 -d ' ' | head -n 10 > top_SNPs_Relate_LWK
```
GWD:
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_GWD.haps.gz --sample prep.chr3_GWD.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate_GWD
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate_GWD -m 1.25e-8 --poplabels all_inds.txt -o popsize_GWD --threshold 0
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_GWD -m 1.25e-8 --poplabels GWD_inds.txt -o selection_relate_GWD
sort -k35r selection_relate_GWD.sele | cut -f 1,2,35 -d ' ' | head -n 10 > top_SNPs_Relate_GWD
```
MSL
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_MSL.haps.gz --sample prep.chr3_MSL.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate_MSL
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate_MSL -m 1.25e-8 --poplabels all_inds.txt -o popsize_MSL --threshold 0
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_MSL -m 1.25e-8 --poplabels MSL_inds.txt -o selection_relate_MSL
sort -k35r selection_relate_MSL.sele | cut -f 1,2,35 -d ' ' | head -n 10 > top_SNPs_Relate_MSL
```
YRI 
```
~/populationgenomics/software/relate/bin/Relate --mode All -m 1.25e-8 -N 30000 --haps prep.chr3_YRI.haps.gz --sample prep.chr3_YRI.sample.gz --map genetic_map_chr3_combined_b37.txt -o chr3_relate_YRI
~/populationgenomics/software/relate/scripts/EstimatePopulationSize/EstimatePopulationSize.sh -i chr3_relate_YRI -m 1.25e-8 --poplabels all_inds.txt -o popsize_YRI --threshold 0
~/populationgenomics/software/relate/scripts/DetectSelection/DetectSelection.sh -i popsize_YRI -m 1.25e-8 --poplabels YRI_inds.txt -o selection_relate_YRI
sort -k35r selection_relate_YRI.sele | cut -f 1,2,35 -d ' ' | head -n 10 > top_SNPs_Relate_YRI
```

Make tree at BP of interest: 
```
~/populationgenomics/software/relate/scripts/TreeView/TreeView.sh --haps relate/ESN/chr3_ESN.haps --sample relate/ESN/chr3_ESN.sample --anc relate/ESN/chr3_relate_ESN.anc --mut relate/ESN/chr3_relate_ESN.mut --poplabels relate/ESN/ESN_inds.txt --years_per_gen 28 -o tree_46659530_ESN --bp_of_interest 46659530
```

CLUES:
https://github.com/standard-aaron/clues/wiki/Sampling-coalescence-times-with-Relate

ESN SNP -> 46659530
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i chr3_relate_ESN -o chr3_ESN_relate_resample_46659530_100 -m 1.25e-8 --coal popsize_ESN.coal --format b --num_samples 100 --first-bp 46659530 --last-bp 46659530
```

LWK SNP -> 46659530
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i chr3_relate_LWK -o chr3_LWK_relate_resample_46659530_100 -m 1.25e-8 --coal popsize_LWK.coal --format b --num_samples 100 --first-bp 46659530 --last-bp 46659530
```
GWD SNP -> 46659530
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i chr3_relate_GWD -o chr3_GWD_relate_resample_46659530_100 -m 1.25e-8 --coal popsize_GWD.coal --format b --num_samples 100 --first-bp 46659530 --last-bp 46659530

```
YRI SNP -> 46659530
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i chr3_relate_YRI -o chr3_YRI_relate_resample_46659530_100 -m 1.25e-8 --coal popsize_YRI.coal --format b --num_samples 100 --first-bp 46659530 --last-bp 46659530
```
MSL SNP -> 46659530
```
~/populationgenomics/software/relate/scripts/SampleBranchLengths/SampleBranchLengths.sh -i chr3_relate_MSL -o chr3_MSL_relate_resample_46659530_100 -m 1.25e-8 --coal popsize_MSL.coal --format b --num_samples 100 --first-bp 46659530 --last-bp 46659530
```

Run CLUES from CLUES folder


ESN
```
python inference.py --times ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/ESN/chr3_ESN_relate_resample_46659530_100 --coal ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/ESN/popsize_ESN.coal --burnin 60 --out ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/CLUES/selection_clues_ESN_46659530_100_burnin_coal
```
YRI
```
python inference.py --times ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/YRI/chr3_YRI_relate_resample_46659530_100 --coal ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/YRI/popsize_YRI.coal --burnin 60 --out ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/CLUES/selection_clues_YRI_46659530_100_burnin_coal
```

LWK 
```
python inference.py --times ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/LWK/chr3_LWK_relate_resample_46659530_100 --coal ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/LWK/popsize_LWK.coal --burnin 60 --out ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/CLUES/selection_clues_LWK_46659530_100_burnin_coal
```
GWD 
```
python inference.py --times ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/GWD/chr3_GWD_relate_resample_46659530_100 --coal ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/GWD/popsize_GWD.coal --burnin 60 --out ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/CLUES/selection_clues_GWD_46659530_100_burnin_coal
```

MSL 
```
python inference.py --times ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/MSL/chr3_MSL_relate_resample_46659530_100 --coal ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/relate/MSL/popsize_MSL.coal --burnin 60 --out ~/populationgenomics/students/tessdiv/FinalProjectPopGenome/CLUES/selection_clues_MSL_46659530_100_burnin_coal
```
```
python plot_traj.py selection_clues_ESN_46659530_100_burnin_coal plots/ESN_traj.png --ext png
```
```
python plot_traj.py selection_clues_GWD_46659530_100_burnin_coal plots/GWD_traj.png --ext png
```
```
python plot_traj.py selection_clues_LWK_46659530_100_burnin_coal plots/LWK_traj.png --ext png
```
```
python plot_traj.py selection_clues_MSL_46659530_100_burnin_coal plots/MSL_traj.png --ext png
```
```
python plot_traj.py selection_clues_YRI_46659530_100_burnin_coal plots/YRI_traj.png --ext png
```

Everything else should be in Python notebooks 


