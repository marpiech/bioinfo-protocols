export RELEASE=v44.3_HO_public
wget "https://reichdata.hms.harvard.edu/pub/datasets/amh_repo/curated_releases/V44/V44.3/SHARE/public.dir/$RELEASE.tar"

tar -xvf $RELEASE.tar

echo "genotypename: $RELEASE.geno" > $RELEASE.par
snpname: $RELEASE.snp >> $RELEASE.par
indivname: $RELEASE.ind >> $RELEASE.par
outputformat: PACKEDPED >> $RELEASE.par
genotypeoutname: $RELEASE.bed >> $RELEASE.par
snpoutname: $RELEASE.bim >> $RELEASE.par
indivoutname: $RELEASE.fam >> $RELEASE.par

convertf -p $RELEASE.par
plink --bfile $RELEASE --recode vcf --out $RELEASE.hg19
CrossMap.py vcf ../hg38/hg19ToHg38.over.chain.gz $RELEASE.hg19.vcf.gz ../hg38/hg38.fa $RELEASE.hg38.vcf
bcftools sort -m 32G $RELEASE.hg38.vcf.gz | bgzip -c > $RELEASE.hg38.sorted.vcf.gz
tabix -p vcf $RELEASE.hg38.sorted.vcf.gz
