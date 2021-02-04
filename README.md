# smMIP-tools: a computational toolset for read processing and mutation detection from molecular-inversion-probes derived data

Here we present an example of how to use smMIP-tools two main features:
The first is a read processing tool that performs quality control steps, generates read-smMIP linkages and retrieval of molecular tags.
The second is an error-aware variant caller capable of detecting both single nucleotide variants and short insertion and deletions.
The example data contain sequencing information obtained by a single smMIP that covers a recurrent mutation of which we will attemp to identify.

## Quick Start
smMIP-tools can be executed from the terminal. There is no need for installation. Copy the [code files](https://www.) to your folder of choice.

## Dependencies
This pipeline requires the following software and packages:
| Program | Packages                                       |
| ------------------------|------------------------- |
| R (https://www.r-project.org) | optparse, data.table, parallel, Rsamtools, dplyr |               

*You will be prompt to install/update other packages when installing the above 5 packages.
   

## Read Processing
### Description
map_smMIPs_extract_UMIs.R takes as an input a paired-end read alignment bam file and a smMIP design file containing information about each probe and its targeted sequenced. It applys a set of filters on the input bam file to discard hard clipped reads, reads with low mapping quality, paired reads with an unexpected insert size or improper alignment orientations. To validate the proper structure of reads, and to identify corrupted UMI sequences read-smMIP linkages are being conducted. The final output contains a couple of quality control summary files concerning raw and consensus reads as well as a BAM file containing only high-quality reads. UMIs sequences and smMIPs-of-origin will be included in each read’s header.

### Configuration 
1) Make sure to have a [smMIP-design file](https://www.). We used [MIPgen](http://shendurelab.github.io/MIPGEN) to design the smMIP used in this example. If you designed your smMIPs differently make sure that your file contain the following columns (case sensitve) and their relavant information: chr, ext_probe_start, ext_probe_stop, ext_probe_sequence, lig_probe_start, lig_probe_stop, lig_probe_sequence, mip_scan_start_position, mip_scan_stop_position, scan_target_sequence, mip_sequence, probe_strand, mip_name
2) Please generate a single folder and copy into it all the bam files that you want to analyse. We provide [bam files](https://github.com/BioSoft/smMIP-tools/tree/main/Example/bams) that can be used with this manual.  

### Running the code
Run map_smMIPs_extract_UMIs.R with the required input parameters:
```
-b, --bam.file. Path to bam file. [MANDATORY]
-p, --panel.file. Path to smMIP design file. [MANDATORY]
-s, --sample.name. Sample ID that will be used to name the output bam. [MANDATORY]
-o, --output, Path for output files.  Path for output files. A new folder which is named based on the -s parameter will be generated by default. If not supplied, the sample folder will be generated within the folder that contain the bam files.
-c, --code, Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume the code share the same folder as this code folder with this code (map_smMIPs_extract_UMIs.R).
-f, --filtered.reads,  default="n", options="y" or "n". Output a sam file that contain the filtered reads. A sam file for the non-filtered reads will also be generated.  
-t, --threads, default=1. Specify the number of threads to use for parallel processing. 
-O, --OVERLAP, default=0.95. Fine-tuning the overlap between reads and smMIPs. Used in the map.smip_to_site function
-M, --MAPQ, default=50. MAPQ cut-off. Used in the filter.on.mappingscore function
  ```
\*map_smMIPs_extract_UMIs.R was built to process one bam file at a time. An example shell script to assign jobs to multiple HPC cluster cores is provided [here](https:/www)

```
Rscript /.mounts/example_github/R/map_smMIPs_extract_UMIs.R -b /.mounts/example_github/Example/bams/control1.bam -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt -s control1

Loading required package: GenomeInfoDb
Loading required package: BiocGenerics
Loading required package: parallel

Attaching package: ‘BiocGenerics’

The following objects are masked from ‘package:parallel’:

    clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
    clusterExport, clusterMap, parApply, parCapply, parLapply,
    parLapplyLB, parRapply, parSapply, parSapplyLB

The following objects are masked from ‘package:stats’:

    IQR, mad, sd, var, xtabs

The following objects are masked from ‘package:base’:

    anyDuplicated, append, as.data.frame, basename, cbind, colnames,
    dirname, do.call, duplicated, eval, evalq, Filter, Find, get, grep,
    grepl, intersect, is.unsorted, lapply, Map, mapply, match, mget,
    order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
    rbind, Reduce, rownames, sapply, setdiff, sort, table, tapply,
    union, unique, unsplit, which, which.max, which.min

Loading required package: S4Vectors
Loading required package: stats4

Attaching package: ‘S4Vectors’

The following object is masked from ‘package:base’:

    expand.grid

Loading required package: IRanges
Loading required package: GenomicRanges
Loading required package: Biostrings
Loading required package: XVector

Attaching package: ‘Biostrings’

The following object is masked from ‘package:base’:

    strsplit


Attaching package: ‘dplyr’

The following objects are masked from ‘package:Biostrings’:

    collapse, intersect, setdiff, setequal, union

The following object is masked from ‘package:XVector’:

    slice

The following objects are masked from ‘package:GenomicRanges’:

    intersect, setdiff, union

The following object is masked from ‘package:GenomeInfoDb’:

    intersect

The following objects are masked from ‘package:IRanges’:

    collapse, desc, intersect, setdiff, slice, union

The following objects are masked from ‘package:S4Vectors’:

    first, intersect, rename, setdiff, setequal, union

The following objects are masked from ‘package:BiocGenerics’:

    combine, intersect, setdiff, union

The following objects are masked from ‘package:stats’:

    filter, lag

The following objects are masked from ‘package:base’:

    intersect, setdiff, setequal, union


Attaching package: ‘data.table’

The following objects are masked from ‘package:dplyr’:

    between, first, last

The following object is masked from ‘package:GenomicRanges’:

    shift

The following object is masked from ‘package:IRanges’:

    shift

The following objects are masked from ‘package:S4Vectors’:

    first, second

###############################
        Run Parameters
###############################
$bam.file
[1] "/.mounts/example_github/Example/bams/control1.bam"

$panel.file
[1] "/.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt"

$sample.name
[1] "control1"

$code
[1] "/.mounts/example_github/R"

$filtered.reads
[1] "n"

$threads
[1] 1

$OVERLAP
[1] 0.95

$MAPQ
[1] 50

$help
[1] FALSE

$output
[1] "/.mounts/example_github/Example/bams"

###############################
            Running...
###############################
Loading bam
Filtering reads based on low mapping score
Filtering reads based on bad sam flag
Filtering reads based on mate distance
Filtering hard clipped reads
Filtering reads with missing mates
Mapping smMIPs to reads. Considering overlap and distance measurements :  100%      
Verifying mapping by local smMIP arms alignment : 100%%     
Extracting UMIs
Writing coverage per smMIP and filtered reads summary files
Adding smMIPs names and UMI sequences to the reads names
Writing UMIs per smmip summary
Writing the clean bam file
[1] "/.mounts/example_github/Example/bams/control1/control1_clean.bam"

###############################
             DONE
###############################
  ```










Who to contact: sagi.abelson@gmail.com
