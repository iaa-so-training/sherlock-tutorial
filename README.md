# sherlock-tutorial
PySnacks 4. SHERLOCK: A python pipeline to explore space-based observations in the search for planets

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/iaa-so-training/sherlock-tutorial/HEAD)

Please vitis the [SHERLOCK pipeline documentation](https://sherlockpipe.readthedocs.io/en/latest/) for detailed information.

<img src="https://github.com/iaa-so-training/sherlock-tutorial/assets/1053066/4f4c8113-cb8c-41c0-b5a4-48c89bd2b6a8" width="200">

# Installation

1. Make sure you have conda/mamba installed. You can follow the instructions in [Installing miniconda](https://droplets-spsrc.readthedocs.io/conda/#installing-miniconda).

2. In a terminal, go to your working directory and clone this repository:

```
git clone https://github.com/iaa-so-training/sherlock-tutorial.git
cd sherlock-tutorial
```

3. Install the dependencies for sherlock (replace `mamba` with `conda` if you don't have mamba installed):
```
mamba env create -f environment.yml
conda activate sherlock
```

If you have any problems, check the description in the [SHERLOCK documentation](https://sherlock-ph.readthedocs.io/en/latest/installation.html).

# Running the tutorial
## Executing the ipython Notebook tutorial

Execute:
```
conda activate sherlock
jupyter lab
```

Of the file explorer (left panel), navigate and open the notebook `tutorials/sherlock_algorithm.ipynb`. Execute the cells in order.

## Executing sherlockpipe in the terminal
We will run sherlockpipe using the `TOI270.yaml` configuration:

```
conda activate sherlock
python -m sherlockpipe --properties tutorials/TOI270.yaml
```

## Running on myBinder (no installation needed)

Click on the following button to create an on-the-fly virtual machine. This is temporary, so make sure you save all your work regularly in a persistent location.

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/iaa-so-training/sherlock-tutorial/HEAD)

Note that myBinder resources are limited, so it will take time to create the virtual machine, and CPU/memory allocation may be small.
