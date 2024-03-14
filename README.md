GOAL: Maintain BED files for in-house custom panels and for kitted panels from vendors.

For assay agnostic exon loci, make a BED file of MANE Select and Clinical Plus isoforms from MANE release 1.3:
```
curl -sL https://ftp.ncbi.nlm.nih.gov/refseq/MANE/MANE_human/release_1.3/MANE.GRCh38.v1.3.refseq_genomic.gff.gz | gzip -dc | grep -v ^# | perl -a -F'\t' -ne '%m=map{split("=")}split(";",$F[8]); $m{ID}=~s/^exon-//; print join("\t",$F[0],$F[3]-1,$F[4],$m{gene}.":".$m{ID})."\n" if($F[2] eq "exon" and ($m{tag} eq "MANE Select" or $m{tag} eq "MANE Plus Clinical"))' > mane_release_v1.3_exon_targets.hg38.bed
```

Add clinically relevant non-coding genes that are not in MANE, and merge overlapping exons:
```
echo -e "chr3\t169764609\t169765060\tTERC:NR_001566.1-1" >> mane_release_v1.3_exon_targets.hg38.bed
bedtools sort -i mane_release_v1.3_exon_targets.hg38.bed | bedtools merge -i - -c 4 -o distinct > mane_release_v1.3_exon_targets.hg38.new.bed
mv -f mane_release_v1.3_exon_targets.hg38.new.bed mane_release_v1.3_exon_targets.hg38.bed
```
