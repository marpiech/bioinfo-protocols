# beagle phasing
```
export MY_VCF=/path/to/vcf/gz
export WORKING_DIR=.

### prepare file
export MY_VCF=$(readlink -f $MY_VCF)
export WORKING_DIR=$(readlink -f $WORKING_DIR)
mkdir -p $WORKING_DIR
export MY_VCF_NAME=$(basename "$MY_VCF" | cut -d. -f1)
zcat $MY_VCF | sed 's/^chr//' | bgzip -c > $WORKING_DIR'/'$MY_VCF_NAME'.nochr.vcf.gz'

### run beagle
for CHR in $(ls /opt/tools/beagle/map/plink* | cut -d. -f2 | grep chr); do 
  export SHORT_CHR=$(echo $CHR | cut -d_ -f1 | sed 's/chr//'); 
  beagle \
    gt=$WORKING_DIR'/'$MY_VCF_NAME'.nochr.vcf.gz' \
    chrom=$SHORT_CHR \
    ref='/opt/tools/beagle/bref/'$SHORT_CHR'.38.bref3' \
    map='/opt/tools/beagle/map/plink.'$CHR'.GRCh38.map' \
    out=$WORKING_DIR'/'$CHR.$MY_VCF_NAME'.nochr.phased'; 
done;

ls -tr chr*vcf.gz | xargs -i bash -c 'tabix -p vcf {}'
ls chr*vcf.gz.tbi | sed 's/:/\t/' |sed 's/-/\t/' | sed 's/\./\t/' | sort -k1,1V -k2,2n | sed 's/\t/:/' | sed 's/\t/-/' | sed 's/\t/\./'  | sed 's/\.tbi//' > vcf.list
bcftools concat -f vcf.list | grep -v 'IMP' | bgzip -c > bgi.nochr.phased.vcf.gz
```
