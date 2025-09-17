
<h1 align="center">How to Build a Bracken Database</h1>


<h3 align="center">M. Asaduzzaman Prodhan<sup>*</sup> </h3>


<div align="center"><b> DPIRD Diagnostics and Laboratory Services </b></div>


<div align="center"><b> Department of Primary Industries and Regional Development </b></div>


<div align="center"><b> 3 Baron-Hay Court, South Perth, WA 6151, Australia </b></div>


<div align="center"><b> *Correspondence: prodhan82@gmail.com </b></div>


<br />


<p align="center">
  <a href="https://github.com/asadprodhan/How_to_build_a_Customised_Kraken2_Database/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-GPL%203.0-yellow.svg" alt="License GPL 3.0" style="display: inline-block;"></a>
  <a href="https://orcid.org/0000-0002-1320-3486"><img src="https://img.shields.io/badge/ORCID-green?style=flat-square&logo=ORCID&logoColor=white" alt="ORCID" style="display: inline-block;"></a>
</p>


<br />


## **STEP 1: BUILD THE KRAKEN2 DATABASE**


👉 [How to How to Build a Customised Kraken2 Database](https://github.com/asadprodhan/How_to_build_a_Customised_Kraken2_Database/blob/main/README.md)



<br />

## **STEP 2: RUN THE FOLLOWING SCRIPT TO BUILD THE BRACKEN DATABASE**


```
#!/bin/bash --login
#SBATCH --job-name=bracken-job           # Name of the job
#SBATCH --account=XXXX                   # Pawsey project account
#SBATCH --partition=XXXX                 # Partition (queue) to run the job
#SBATCH --time=1-00:00:00                # Time limit (1 day)
#SBATCH --ntasks=1                       # Single task/job
#SBATCH --ntasks-per-node=1
#SBATCH --nodes=1                        # Request 1 node
#SBATCH --exclusive                      # Request exclusive access to the entire node (all CPUs), alternative --cpus-per-task=128
#SBATCH --no-requeue
#SBATCH --export=none

unset SBATCH_EXPORT
module load singularity/4.1.0-nompi

# Define the path to the Kraken2 database and container details
CONTAINER_DIR="xxxx/containers"
BRACKEN_CONTAINER="$CONTAINER_DIR/bracken_3.1.sif"
BRACKEN_IMAGE="quay.io/biocontainers/bracken:3.1--h9948957_0"


# Ensure the container directory exists
mkdir -p "$CONTAINER_DIR"

# Download the Kraken2 container if it doesn't already exist
if [ ! -f "$BRACKEN_CONTAINER" ]; then
  echo "Downloading Bracken container..."
  singularity pull "$BRACKEN_CONTAINER" "docker://$BRACKEN_IMAGE"
fi

# Path to your Kraken2 database
# If the database directory is in the current working directory, then just write the directory name
DBDIR="xxxxx"

# K-mer size and read lengths
KMER=35
READ_LENGTHS=(100 150 250)

# Loop over read lengths and build Bracken database
for RL in "${READ_LENGTHS[@]}"; do
    echo "Building Bracken database for read length $RL..."
    singularity exec "$BRACKEN_CONTAINER" bracken-build \
        -d "$DBDIR" \
        -k $KMER \
        -l $RL \
        -t "$SLURM_CPUS_ON_NODE"
done
```

## **At this stage, your Bracken database is built on your custom Kraken2 database, and ready for classification!**
