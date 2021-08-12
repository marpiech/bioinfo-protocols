# beagle phasing
```
export MY_VCF=/path/to/vcf/gz
export WORKING_DIR=.

### prepare file
export MY_VCF=$(readlink -f $MY_VCF)
export WORKING_DIR=$(readlink -f $WORKING_DIR)
mkdir -p WORKING_DIR
export MY_VCF_NAME=$(basename "$MY_VCF" | cut -d. -f1)
zcat $MY_VCF | sed 's/^chr//' | bgzip -c > $WORKING_DIR'/'$MY_VCF_NAME'.nochr.vcf.gz'

### run beagle
beagle gt=$WORKING_DIR'/'$MY_VCF_NAME'.nochr.vcf.gz' ref=1.38.bref3 out=$WORKING_DIR'/'$MY_VCF_NAME'.nochr.phased'
```
