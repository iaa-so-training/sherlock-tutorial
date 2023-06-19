# PySnacks 4: SHERLOCK tutorial
PySnacks 4. SHERLOCK: A python pipeline to explore space-based observations in the search for planets

<p align="center">
  <img src="https://github.com/iaa-so-training/sherlock-tutorial/assets/1053066/4f4c8113-cb8c-41c0-b5a4-48c89bd2b6a8" width="300">
</p>

In this workshop, I present SHERLOCK, an end-to-end public pipeline that allows users to explore the data from space-based missions such as TESS and Kepler/K2 to search for planetary candidates; in particular, I will focus on TESS. This pipeline can be used to recover alerted candidates by the automatic pipelines such as the Science Processing Operations Center (SPOC) and the Quick Look Pipeline (QLP), the so-called TESS objects of interest (TOIs), and to search for candidates that remain unnoticed due to detection thresholds, lack of data exploration or poor photometric quality. To this end, SHERLOCK has six different modules to <b>(1)</b> acquire and prepare the light curves from their online repositories, <b>(2)</b> search for planetary candidates, <b>(3)</b> vet the interesting signals, <b>(4)</b> perform a statistical validation, <b>(5)</b> model the signals to refine their ephemerides, and <b>(6)</b> compute the observational windows from ground-based observatories to trigger a follow-up campaign. To execute all these modules, the user only needs to fill in an initial yaml file with some basic information, such as the star ID, the cadence to be used, etc., and execute a few lines of code sequentially to pass from one step to the next. SHERLOCK can be used to search for planets in any imaginable survey; for example, it is being used in the SPECULOOS project [(Sebastian et al. 2021)](https://ui.adsabs.harvard.edu/abs/2021A%26A...645A.100S/abstract), which is searching for transiting Earth-sized planets orbiting ultra-cool stars in the habitable zone. These planets provide the best opportunities for future atmospheric characterization of temperate small rocky worlds. In addition, SHERLOCK is used in the FATE project [(Van Grootel et al. 2021)](https://ui.adsabs.harvard.edu/abs/2021A&A...650A.205V/abstract), a survey that aims to find transiting planets orbiting hot subdwarfs. Thanks to their small sizes, these stars represent excellent opportunities for addressing the question of the evolution of planetary systems once their host stars left the main sequence and passed through the red giant branch phase. 

Please visit the [SHERLOCK pipeline documentation](https://sherlockpipe.readthedocs.io/en/latest/) for detailed information.



# Installation
For the best performance, SHERLOCK runs over python3.10.

1. Make sure you have conda/mamba installed. You can follow the instructions in [Installing miniconda](https://droplets-spsrc.readthedocs.io/conda/#installing-miniconda).

2. In a terminal, go to your working directory and clone this repository:

```
git clone https://github.com/iaa-so-training/sherlock-tutorial.git
cd sherlock-tutorial
```

3. Install the dependencies for SHERLOCK (replace `mamba` with `conda` if you don't have mamba installed):
```
mamba env create -f environment.yml
conda activate sherlock
```

If you have any problems, check the description in the [SHERLOCK documentation](https://sherlock-ph.readthedocs.io/en/latest/installation.html).

# Running the tutorial
## Executing the Jupyter Notebook tutorials
Unfortunately, SHERLOCK is computationally expensive and has been designed to run, ideally, on a server with several cores available. Running on your laptop might not be very efficient. Hence, in this workshop, we will not purely execute SHERLOCK; on the contrary, we will explore several Jupyter notebooks that mimic how SHERLOCK works and emphasize understanding how SHERLOCK is built. The Jupyter notebooks are stored in the folder [Jupyter Notebooks](https://github.com/iaa-so-training/sherlock-tutorial/tree/main/Jupyter%20Notebooks).

Execute:
```
conda activate sherlock
jupyter lab
```

Of the file explorer (left panel), navigate and open any of the notebooks, for example, the simplest one, `tutorials/tess_lightcurves.ipynb`. Execute the cells in order.

## Executing SHERLOCK on a computational server
If you managed to have SHERLOCK installed on a server, then you might try any of the full examples that are stored in the folder [Full examples](https://github.com/iaa-so-training/sherlock-tutorial/tree/main/Full%20examples). To replicate these results, you just have to follow the six command lines as follows: 

***************************************************************************
(1) Acquire and prepare the light curves from their online repositories
***************************************************************************
```nohup python3.10 -m sherlockpipe --properties explore.yaml --explore &```

Where `explore.yaml` is a general template of the property file where you only need to include the TIC-ID. This template can be found here [explore.yaml](https://github.com/iaa-so-training/sherlock-tutorial). This command line will produce several folders and 
files, which inspection will allow you to prepare the `xxxx.yaml` file for a fine-tunned search to be executed in the next step.

***************************************************************************
(2) Search for planetary candidates
***************************************************************************

```nohup python3.10 -m sherlockpipe --properties xxxx.yaml &```

Where the `xxxx.yaml` file corresponds to the best combination of parameters you want to apply: which cadences to use, which sectors, which detrends etc. In the folder [Yaml_examples](https://github.com/iaa-so-training/sherlock-tutorial/tree/main/Yaml%20examples) you can find a variety of combinations. The main results of this module are the promising signals that might be planetary candidates, for which we need to conduct a careful vetting. This module generates a folder `TIC-xxxxx`; inside it, one can find all the information about the transit search. 

***************************************************************************
(3) Vet the interesting signals
***************************************************************************

We have to navigate to the `TIC-xxxxx` folder generated previously. Then execute:

```nohup python3.10 -m sherlockpipe.vet --candidate x &```

Where `x` is the number of the signal you want to vet; x=1,2,3 etc. This module will produce the folder `vetting_x`, where you will find a report that will give you a better idea about the credibility of the signal found.

***************************************************************************
(4) Perform a statistical validation
***************************************************************************

If the vetting of a given candidate did not show any clear hint of a false positive, we can conduct a statistical validation. For this step, SHERLOCK relies on the TRICERATOPS package [(Giacalone et al. 2021)](https://ui.adsabs.harvard.edu/abs/2021AJ....161...24G/abstract). You just need to execute:

```nohup python3.10 -m sherlockpipe.validate --candidate x &```

This module will explore a variety of astrophysical scenarios that might be producing the periodic transiting signal we found, such as nearby eclipsing binaries. The results will be stored in the folder `validation_x`.

***************************************************************************
(5) Model the signals to refine their ephemerides
***************************************************************************

If the results from the vetting (step 3) and validation (step 4) do not point out to a false positive, then you need to confirm your candidate from the ground. To this end, you need to obtain the most accurate planet parameters
and to refine the ephemerides. In this context, SHERLOCK relies on the ALLESFITTER package [(Günther & Daylan 2021)](https://ui.adsabs.harvard.edu/abs/2021ApJS..254...13G/abstract). You just need to execute:   

```nohup python3.10 -m sherlockpipe.fit --candidate x &```

This command will generate the folder `fit_x`. Inside it, you will find all the information about the fitting, the planet parameters, and all the information to predict observational windows from your favorite telescopes.

***************************************************************************
(6) Compute the observational windows from ground-based observatories to trigger a follow-up campaign
***************************************************************************

You need to navigate to the `fit_x` folder. Copy inside this folder the file `observatories.csv`, where the information about the facility you would like to use have to be provided. A template of this file can be found here: [observatories.csv](https://github.com/franpoz/WORKSHOP). Then, you just need to execute:

```nohup python3.10 -m sherlockpipe.plan --observatories observatories.csv &```

This module will generate an observation plan with the coming observational windows for one year. You just need to trigger your campaign! 

# Additional material
## Slack channel for help
We created the Slack channel `sherlock-help`, where you can find some discussions, alerts for updates, and help if you find some obstacle using SHERLOCK, etc. But remember, the channel is not to report bugs; for this, please open an issue in the main [SHERLOCK](https://github.com/franpoz/SHERLOCK/tree/master) GitHub site. If you are interested in joining this channel, please send us a request.

## Recent usage

Here you will find a list of papers that used SHERLOCK in their analyses. Have a look at them to see different strategies that you might use depending on your scientific case.

[Pozuelos et al. (2023)](https://ui.adsabs.harvard.edu/abs/2023A%26A...672A..70P/abstract) \
[Delrez et al. (2022)](https://ui.adsabs.harvard.edu/abs/2022arXiv220902831D/abstract)\
[Thuillier et al. (2022)](https://ui.adsabs.harvard.edu/abs/2022A%26A...664A.113T/abstract) \
[Dransfield et al. (2022)](https://ui.adsabs.harvard.edu/abs/2022MNRAS.tmp.1364D/abstract) \
[Luque et al. (2022)](https://ui.adsabs.harvard.edu/abs/2022arXiv220410261L/abstract) \
[Schanche et al. (2022)](https://ui.adsabs.harvard.edu/abs/2022A%26A...657A..45S/abstract) \
[Wells et al. (2021)](https://ui.adsabs.harvard.edu/abs/2021A%26A...653A..97W/abstract) \
[Benni et al. (2021)](https://ui.adsabs.harvard.edu/abs/2021MNRAS.505.4956B/abstract)  
[Van Grootel et al. (2021)](https://ui.adsabs.harvard.edu/abs/2021A%26A...650A.205V/abstract) \
[Demory et al. (2020)](https://ui.adsabs.harvard.edu/abs/2020A%26A...642A..49D/abstract)

## ADS Library 

Here you have a library with related papers that are interesting in the context of SHERLOCK and the references used during the workshop.

[SHERLOCK WORKSHOP (IAA-CSIC)](https://ui.adsabs.harvard.edu/public-libraries/XSc7e6ezTn-Sm_eVm6uOlQ)

## Github packages

Here is the main list of packages that are used by SHERLOCK, that you might want to have a look at to have a better understanding:

[Wotan: ](https://github.com/hippke/wotan) to detrend light curves.\
[Transit Least Squares: ](https://github.com/hippke/tls) the search engine to find planets.\
[LightKurve: ](https://github.com/lightkurve/lightkurve) to retrieve the data and use other functions. \
[Allesfitter: ](https://github.com/MNGuenther/allesfitter) to fit the planetary candidates in a Bayesian framework. \
[Triceratops: ](https://github.com/stevengiacalone/triceratops) to statistically validate the planetary candidates.\
[Tpfpoltter: ](https://github.com/jlillo/tpfplotter) to plot the field-of-view of the TESS data.

## Citation
We are currently working on a specific paper for SHERLOCK. In the meantime, the best way to cite SHERLOCK is by referencing the first paper where it was used [Pozuelos et al. (2020)](https://ui.adsabs.harvard.edu/abs/2020A%26A...641A..23P/abstract):

```
@ARTICLE{2020A&A...641A..23P,
       author = {{Pozuelos}, Francisco J. and {Su{\'a}rez}, Juan C. and {de El{\'\i}a}, Gonzalo C. and {Berdi{\~n}as}, Zaira M. and {Bonfanti}, Andrea and {Dugaro}, Agust{\'\i}n and {Gillon}, Micha{\"e}l and {Jehin}, Emmanu{\"e}l and {G{\"u}nther}, Maximilian N. and {Van Grootel}, Val{\'e}rie and {Garcia}, Lionel J. and {Thuillier}, Antoine and {Delrez}, Laetitia and {Rod{\'o}n}, Jose R.},
        title = "{GJ 273: on the formation, dynamical evolution, and habitability of a planetary system hosted by an M dwarf at 3.75 parsec}",
      journal = {\aap},
     keywords = {planets and satellites: dynamical evolution and stability, planets and satellites: formation, Astrophysics - Earth and Planetary Astrophysics, Astrophysics - Solar and Stellar Astrophysics},
         year = 2020,
        month = sep,
       volume = {641},
          eid = {A23},
        pages = {A23},
          doi = {10.1051/0004-6361/202038047},
archivePrefix = {arXiv},
       eprint = {2006.09403},
 primaryClass = {astro-ph.EP},
       adsurl = {https://ui.adsabs.harvard.edu/abs/2020A&A...641A..23P},
      adsnote = {Provided by the SAO/NASA Astrophysics Data System}
}

```
## Developers
The leading developers are [Martín Dévora-Pajares](https://github.com/martindevora) and [Francisco J. Pozuelos](https://github.com/franpoz), with the help of some contributors. 


