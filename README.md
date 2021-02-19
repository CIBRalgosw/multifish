# EASI-FISH Nextflow Pipeline

The purpose of this pipeline is to analyze imagery collected using [EASI-FISH](https://github.com/multiFISH/EASI-FISH) (Expansion-Assisted Iterative Fluorescence *In Situ* Hybridization). It includes automated image stitching, distributed multi-round image registration, cell segmentation, and distributed spot detection. 

## Quick Start

The only software requirements for running this pipeline are [Nextflow](https://www.nextflow.io) (version 20.10.0 or greater) and [Singularity](https://sylabs.io) (version 3.5 or greater). If you are running in an HPC cluster, ask your system administrator to install Singularity on all the cluster nodes.

To [install Nextflow](https://www.nextflow.io/docs/latest/getstarted.html):

    curl -s https://get.nextflow.io | bash 

To [install Singularity](https://sylabs.io/guides/3.7/admin-guide/installation.html) on CentOS Linux:

    sudo yum install singularity

Clone the multifish repository with the following command:

    git clone https://github.com/JaneliaSciComp/multifish.git

Before running the pipeline for the first time, pull in and build the submodules using the setup script:

    ./setup.sh
  
Launch the demo using the EASI-FISH example data:

    ./examples/demo.sh <data dir> [arguments]

The `data dir` is the path where you want to store the data and analysis results. 

This script will also download the standard segmentation machine learning model, used for cell segmentation.

You can add additional arguments to the end in order to, for example, skip steps previously completed, or add Nextflow Tower monitoring. See below for additional details about the argument usage.


## Pipeline Overview

This pipeline is containerized and portable across the various platforms supported by [Nextflow](https://www.nextflow.io). So far it has been tested on a standalone workstation and the Janelia compute cluster (IBM Platform LSF). If you run it successfully on any other platform, please let us know so that we can update this documentation.

The pipeline includes the following modules:
* **stitching** - Spark-based distributed stitching pipeline
* **spot_extraction** - Spot detection using hAirlocalize
* **segmentation** - Cell segmentation using Starfinity 
* **registration** - Bigstream distributed registration pipeline
* **warp_spots** - Warp detected spots to registration
* **intensities** - Intensity measurement
* **assign_spots** - Mapping of spot counts to segmented cells

![Pipeline Diagram](docs/pipeline_diagram.png)

## Parameters

The following parameters are required to run the full pipeline. See the [parameter documentation](docs/Parameters.md) for a complete list of all possible options.

### Required Parameters

| Argument   | Description                                                                           |
|------------|---------------------------------------------------------------------------------------|
| --data_dir | Path to the directory containing the input CZI/MVL acquisition files | 
| --output_dir | Path to the directory containing pipeline outputs |
| &#x2011;&#x2011;segmentation_model_dir | Path to the directory containing the machine learning model for segmentation |
| --acq_names | Names of acquisition rounds to process. These should match the names of the CZI/MVL files found in the data_dir |  
| --ref_acq | Name of the acquisition round to use as the fixed reference |


## Pipeline Execution

Nextflow supports many different execution engines for portability across platforms and schedulers. We have tested the pipeline using local execution and using the cluster at Janelia Research Campus (running IBM Platform LSF). 

To run this pipeline on a cluster, all input and output paths must be mounted and accessible on all the cluster nodes. 

### Run the pipeline locally

To run the pipeline locally, you can use the standard profile:

    ./main.nf [arguments]

This is equivalent to specifying the localsingularity profile:

    ./main.nf -profile localsingularity [arguments]

### Run the pipeline on IBM Platform LSF 

This example also sets the project flag to demonstrate how to set LSF options.

    ./main.nf -profile lsf --lsf_opts "-P multifish" [arguments]

Usage examples are available in the [examples](examples) directory.

## User Manual

Further detailed documentation is available here:

* [Pipeline Parameters](docs/Parameters.md) 
* [File Organization](docs/FileOrganization.md)
* [Troubleshooting](docs/Troubleshooting.md)
* [Development](docs/Development.md)

## Open Source License

This software is made available under [Janelia's Open Source Software](https://www.janelia.org/open-science/software-licensing) policy which uses the BSD 3-Clause License. 
