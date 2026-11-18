
For ease of use you can call directly from *Python* 

**microbiorust-py** provides high-performance Python bindings for the microBioRust bioinformatics library via Rust and PyO3. You can call all functions directly from Python—no Rust toolchain required.

No need to install Rust 

Install microbiorust-py using pip:

```pip install microbiorust```

macOS users: If you see a cc linker error or missing libpython when importing, add your Python lib directory to DYLD_LIBRARY_PATH (for example, inside a Conda environment):
```export DYLD_LIBRARY_PATH=$CONDA_PREFIX/lib:$DYLD_LIBRARY_PATH```


Quick Start: GenBank to Protein FASTA
All parsed collections are like Python dictionaries (keys(), values(), items(), len(), key indexing, and iteration)

example script for converting from GenBank to protein fasta: 

```
from microbiorust import gbk_to_faa
result = gbk_to_faa("filename.gbk") 
for entry in result.values():
   print(f">{entry.locus_tag}\n{entry.faa}")
```

To write FASTA files directly to disk (fastest, with zero Python object conversion overhead), use parse_gbk or parse_embl with .write_faa(), .write_ffn(), or .write_fna():

```
import microbiorust as mb

records = mb.parse_gbk("filename.gbk")
records.write_faa("proteins.faa")
records.write_ffn("genes.ffn")
records.write_fna("contigs.fna")
```

**Features Overview**

Functions can be imported directly from microbiorust or from submodules (gbk, embl, blast, align, seqmetrics)

GenBank Input (microbiorust.gbk)
1. parse_gbk(filename) — parses a GenBank file into a RecordCollection (contig-level records with .features(), .sequences(), .faa(), .ffn(), and .write_*() methods)

2. gbk_to_faa(filename) — converts GenBank CDS features to a protein FASTA collection (FaaCollection)

3. gbk_to_ffn(filename) — converts GenBank CDS features to a gene nucleotide FASTA collection (FfnCollection)

4. gbk_to_fna(filename) — parses GenBank records for DNA sequence contigs (RecordCollection)

5. gbk_to_gff(filename, dna=False) — converts GenBank format to GFF3 (<filename>.gff), optionally appending contig DNA

6. gbk_to_faa_count(filename) — returns the total count of protein sequences in a GenBank file without allocating sequence strings

Example usages:
```
import microbiorust as mb

# Convert GenBank to GFF3 (with DNA sequences included)
mb.gbk_to_gff("genome.gbk", dna=True)

# Count protein CDS entries directly
count = mb.gbk_to_faa_count("genome.gbk")
print(f"Total proteins: {count}")

# Inspect features and sequences per contig record
import microbiorust as mb

records = mb.parse_gbk("genome.gbk")

for contig_id, record in records.items():
    print(f"Contig: {contig_id} ({len(record.sequence())} bp)")

    #iterate over all the annotated features in this record
    for locus_tag, f in record.features().items():
        print(f.locus_tag, f.gene, f.product, f.start, f.stop, f.strand)

    #to look up a specific locus tag directly on the record
    tags = record.locus_tag()
    if tags:
        first_feature = record[tags[0]]
        print("First feature:", first_feature)
```

**EMBL Input (microbiorust.embl)**

1. parse_embl(filename) — parses an EMBL file into a RecordCollection

2. embl_to_faa(filename) — converts EMBL CDS features to a protein FASTA collection (FaaCollection)

3. embl_to_ffn(filename) — converts EMBL CDS features to a gene nucleotide FASTA collection (FfnCollection)

4. embl_to_fna(filename) — parses EMBL records for DNA sequence contigs (RecordCollection)

5. embl_to_gff(filename, dna=False) — converts EMBL format to GFF3 (<filename>.gff), True/False to append DNA sequence

Example use:
```
from microbiorust.embl import embl_to_faa, embl_to_gff

proteins = embl_to_faa("genome.embl")
for p in proteins.values():
    print(f">{p.locus_tag}\n{p.faa}")

embl_to_gff("genome.embl", dna=False)
```

**JSON Export**
JSON Export
All collections (RecordCollection, FeatureCollection, SequenceCollection, FaaCollection, FfnCollection) support .to_json() for exporting directly from Rust into a JSON string:

```
import json
import microbiorust as mb

records = mb.parse_gbk("genome.gbk")
data = json.loads(records.to_json())

#data is a standard Python dict where the key is the id 
first_id = next(iter(data))
print(json.dumps({first_id: data[first_id]}, indent=2))
```
  
**BLAST Input (microbiorust.blast)**

parse_tabular(path) — streaming parser for tabular BLAST output (-outfmt 6), returning a list of hit dictionaries

parse_xml(path) — streaming parser for BLAST XML output (-outfmt 5), returning nested iteration dictionaries

Example usage:
```
from microbiorust.blast import parse_tabular, parse_xml

hits = parse_tabular("blast_results.tab")
for hit in hits:
    print(hit["qseqid"], hit["sseqid"], hit["pident"], hit["evalue"], hit["bitscore"])

xml_iterations = parse_xml("blast_results.xml")
```

**Multiple Sequence Alignment (microbiorust.align)**

1. subset_msa_alignment(filename, row_index, col_index) — subsets a DNA or protein FASTA alignment using (start, end) index tuples for rows and columns, returning FASTA-formatted strings

2. consensus_sequence(filename) — computes a consensus sequence string from a DNA or protein FASTA alignment

3. purge_gaps(input, output, threshold) — removes alignment columns exceeding a gap fraction threshold (e.g., 0.2) and writes the cleaned FASTA alignment to output

Example usage:
```
from microbiorust.align import consensus_sequence, purge_gaps, subset_msa_alignment

consensus = consensus_sequence("alignment.fasta")
print("Consensus:", consensus)

# Extract rows 0..10 and columns 20..80
subset = subset_msa_alignment("alignment.fasta", row_index=(0, 10), col_index=(20, 80))
for seq in subset:
    print(seq)

# Remove columns with >20% gaps
purge_gaps("alignment.fasta", "filtered_alignment.fasta", threshold=0.2)
```

**Sequence Metrics (microbiorust.seqmetrics)**

1. hydrophobicity(seq, window_size) — calculates a sliding-window hydrophobicity profile (list[float])

2. amino_counts(seq) — counts occurrences of each amino acid in the provided sequence (dict[str, int])

3. amino_percentage(seq) — calculates the percentage of each amino acid in the provided sequence (dict[str, float])

Example usage:
```
from microbiorust.seqmetrics import amino_counts, amino_percentage, hydrophobicity

seq = "MSNTQKKNVPELRFPGFEGEWEEKKLGDLTTKIGSGKTPKGGSENYTNKG"

print(amino_counts(seq))
print(amino_percentage(seq))
print(hydrophobicity(seq, window_size=9))
```
