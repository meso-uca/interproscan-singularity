# interproscan-singularity

Singularity definition file to build an interproscan image, including database and multithread-aware launch script.


## Build image

Clone this repo, then :
```
$ singularity build interproscan-5.60-92.0.sif interproscan.def
```

You will need at least 180GB of temporary space, perhaps should you override `TMPDIR` (used by singularity) to a place where you have plenty of space ; for instance : 
```
$ export TMPDIR=/storage/scratch/$LOGNAME/tmp
$ mkdir -p $TMPDIR
$ singularity build interproscan-5.60-92.0.sif interproscan.def
```


## Use image

On a standalone server, let the multithread-aware launch script configure the right number of cores : 
```
$ singularity run --bind /tmp:/tmp interproscan-5.60-92.0.sif -dp --goterms --pathways -f tsv --appl "PfamA,TIGRFAM,PRINTS,PrositePatterns,Gene3d" -o /tmp/out.ipr -i /tmp/test.fasta

Adjusting interproscan.properties file to take into account that there are 80 cores.
Setting max ipr5 workers to 40.
Setting starting ipr5 workers to 20.
Setting number if CPU cores for jobs to 2.

19/01/2023 15:03:53:269 Welcome to InterProScan-5.60-92.0
19/01/2023 15:03:53:270 Running InterProScan v5 in STANDALONE mode... on Linux
[...]
```

On a cluster node, you should set the number of cores from your allocation accordingly :
```
$ echo ${SLURM_CPUS_ON_NODE}
8
$ export OVERRIDE_IPR5_WORKERS=2
$ export OVERRIDE_IPR5_CORES=$( python -c "print(max(${SLURM_CPUS_ON_NODE}/2,1))" )
$ singularity run --bind /tmp:/tmp interproscan-5.60-92.0.sif -dp --goterms --pathways -f tsv --appl "PfamA,TIGRFAM,PRINTS,PrositePatterns,Gene3d" -o /tmp/out.ipr -i /tmp/test.fasta

The number of ipr5 workers and/or job cores has been manually overridden.
Setting max ipr5 workers to 2.
Setting starting ipr5 workers to 1.
Setting number if CPU cores for jobs to 4.

19/01/2023 15:03:53:269 Welcome to InterProScan-5.60-92.0
19/01/2023 15:03:53:270 Running InterProScan v5 in STANDALONE mode... on Linux
[...]
```



