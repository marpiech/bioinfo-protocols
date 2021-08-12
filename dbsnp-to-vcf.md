# dbsnp to vcf
```
export DBSNP_VERSION=155

wget "https://ftp.ncbi.nih.gov/snp/archive/b$DBSNP_VERSION/VCF/GCF_000001405.39.gz"
wget "https://ftp.ncbi.nih.gov/snp/archive/b$DBSNP_VERSION/VCF/GCF_000001405.39.gz.tbi"
wget "https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/001/405/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_report.txt"

cat GCF_000001405.39_GRCh38.p13_assembly_report.txt | \
  grep -e '^[^#]' | \
  awk '{ print $7, $11 }' > GCF_000001405.39_GRCh38.p13_assembly_report.chrnames

bcftools annotate \
  --rename-chrs GCF_000001405.39_GRCh38.p13_assembly_report.chrnames \
  --threads 10 -Oz \
  -o GRCh38.dbSNP"$DBSNP_VERSION".vcf.gz \
  GCF_000001405.39.gz

tabix -p vcf GRCh38.dbSNP$DBSNP_VERSION.vcf.gz
bcftools norm -c x -f genome/GRCh38.no_alt_analysis_set.fa.gz GRCh38.dbSNP155 chr.vcf.gz GRCh38.dbSNP155.chr.norm.vcf.gz -O z
```
