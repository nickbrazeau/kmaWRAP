# kmaWRAP Introduction
The purpose of this pipeline is to identify genes or other small-contig elements (target) from raw reads (subject). We query the target using the [kma](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-018-2336-6) algorithm, which essentially uses k-mer based matching to form local alignments.

### Submodules
#### WGS
This module is meant to run on Illumina paired-end whole-genome reads. First, it passes reads through `cutadapt` using the "interleaved" option to trim adapter sequences (user must specify adapters). Then, it passes the trimmed PE reads to `kma` to be queried against the target database.

#### Stitched Amplicon
This module is meant to run on Illumina paired-end amplicon reads where the insert overlaps between R1 and R2 (i.e. the reads can be stitched). First, raw reads are passed to `cutadapt` and sequences of interest are extracted based on the locations of the Forward and Reverse primers (user must specify primers and primer reverse complements). Then, it passes the trimmed interleaved reads to `FLASh` for stitching (user must specify min-overlap and max-overlap of expected insert). Finally, as above, the now trimmed-merged-read (i.e. a single-read) to `kma` to be queried against the target database.


### Getting Started
Note, we have provided scripts to be used on a SLURM system. If you are using a different cluster manager, you will need to adjust the `lauch.py` and `launch_kmawrap.sh` scripts accordingly.   

#### Step 0: Indexing
Target databases must be indexed prior to use for querying.  

```

kma index -i <inputfasta> -o <name>

```
#### Step 0: Config file
You must update the `config_kma.yaml` and the `launch_kmawrap.sh` files with your user inputs prior to running the pipeline.

#### Step 1: Main
Always a good idea to check with the `--dryrun` flag before sending to snakemake.

```
bash launch_kmawrap.sh
```

### Dependencies
* cutadapt (v>=2.3)
* FLASh (v>=1.2.11)
* kma (v>=1.2.3t)


### Error Handling
If you hit a permissions issue, make sure to the files are executable `chmod u+x`.   
Make sure your indexed database path is <path_to_index><name>

#### Notes to Future-Self or Other Users
You could consider making the cutadapt step more or less "strict" depending on the `a/A, g/G, b/B` flags as well as the `--pair-filter=both --discard-untrimmed` flags depending on amount of expected "read-through".
