
For ease of use you can call directly from *Python* 

No need to install Rust 

Install microbiorust-py using pip:

```pip install microbiorust```

example script for converting from GenBank to protein fasta: 

```
from microbiorust import gbk_to_faa
result = gbk_to_faa("filename.gbk") 
for r in result:
   print(r)
```

microbiorust-py contains the following features: 

GenBank input: 

gbk_to_faa # converts gbk format to protein fasta
 
gbk_to_fna # converts gbk format to DNA sequence contigs fasta 
 
gbk_to_ffn # converts gbk format to DNA gene sequence fasta 
 
gbk_to_gff # converts gbk format to GFF3 
 
gbk_to_faa_count # counts numbers of protein fasta converted from gbk   

EMBL input: 

embl_to_faa 

embl_to_fna 

embl_to_gff 


BLAST input: 

parse_tabular # streaming parse BLAST input as -outfmt 6 

parse_XML # streaming BLAST parser for -outfmt 5 


Alignment input (MSA): 

subset_msa_alignment # subset the alignment by row and column 

get_consensus # get a consensus sequence for your MSA alignment 


Calculate sequence metrics: 

hydrophobicity 

amino_counts # counts of each amino acid in the provided sequence 

amino_percentage # percentage of each amino acid in the provided sequence 



