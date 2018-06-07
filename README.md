## Identifying exons for target capture in _Trifolium repens_

This repository contains scripts associated with identifying exons to be targeted with sequence capture probes in a large-scale project identifying the genome-wide consequences of urbanization. The goal is to begin with transcript sequences of a non-model organism (white clover in this case) and end with annotated exons with their corresponding boundaries identified in those transcripts. This approach is broadly based on [Bi *et. al.* (2012)](https://bmcgenomics.biomedcentral.com/articles/10.1186/1471-2164-13-403). Briefly, the pipeline is as follows:

1. Obtain transcriptome sequence for study organism, either through a preliminary RNAseq experiment or from published literature and sequence repositories.
    * **Note:** It will help if the transcriptome is annotated. This can be done using [*blastx*](https://www.ncbi.nlm.nih.gov/books/NBK279684/), which matches the transcript nucleotide sequences against a database of protein sequences.
2. Use *blastx* to query the transcript sequences against the protein databases of a (or multiple) closely related species for which there is a reference genome. For example, in the case of white clover, possibilities include the congeners [red clover (*Trifolium pratense*)](https://www.nature.com/articles/srep17394) and [subterraneum clover (*Trifolium subterraneum*)](https://onlinelibrary.wiley.com/doi/full/10.1111/pbi.12697) and the confamilial [barrel clover (*Medicago truncatula*)](https://bmcgenomics.biomedcentral.com/articles/10.1186/1471-2164-15-312).
    * **Note:** Some filtering of blast results will likely be required since each query sequences may map to multiple proteins or map to the same protein multiple times. For example, I chose to filter out sequences with Expect values (E) greater than 1e<sup>-10</sup>. Also, in cases where the query sequences had multiple matches, I kept the longest alignment.
3. Retrieve the sequences of all of the proteins that the blastx search identified as being homologous to the transcript sequences in your study species. Note this can be automated in Python.
4. Use the *protein2genome* model in [Exonerate](https://www.ebi.ac.uk/about/vertebrate-genomics/software/exonerate) to map the protein sequences to the reference genome of the closely related species.
5. **Optional:** Select among a subset of the proteins that map to the genome or a subset of exons among all of the genes. This decision should be based on the goals of the study. In my case, I am opting to capture as many genes as possible and thus most genes will only be represented by a single exon. However, in a study of phylogenomics for example, fewer genes may be necessary and thus all exons of those genes may be captured. In fact, evidence suggests that in such cases, probes can be designed directly from transcript sequencies without loss of capture efficiency, thereby circumventing identifying exon boundaries in the way describe here. (See this nice paper by [Portik *et al.* (2016)](https://onlinelibrary.wiley.com/doi/full/10.1111/1755-0998.12541).
6. Once the genes/exons are chosen, use the *est2genome* model in exonerate to map the exon sequences back onto the initial transcript sequences to identify the exon boundaries.
