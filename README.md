# SORN

This codes simulates the self-organizing recurrent neural network ([SORN](http://dx.doi.org/10.3389/neuro.10.023.2009)).

## Install
Just `git clone https://github.com/chrhartm/SORN`.

## Code organization
The code is set up in a somewhat modular manner:
* the *common* folder contains code common to all experiments such as the sorn, default parameters, ...
* the *utils* folder contains code for backing up files, submitting jobs to clusters, ...
* the other folders (*examples*, *chartmann*) contain the experiments implemented so far.

In general, this is scientific code so it is neither pretty nor well documented. The files in the common folder are somewhat documented and especially the default parameter file (`defaults.py`) might be useful to get started.

## Getting started
To run experiments, navigate to the *common* folder and run `python test_single.py your_parameter_file`.

For example, run `python test_single.py examples.zheng2013.param_noinput` to replicate some figures of [Zheng2013](http://dx.doi.org/10.1371/journal.pcbi.1002848).

## Designing your own experiments
If you are planning to also work with SORN and use this code, feel free to fork and set up your experiments in a folder like *chartmann*. Once you are ready to publish, issue a pull request to publish your code in this repository. If you need to modify files in the common folder, try to keep backwards compatibility to the other experiments (e.g. by introducing default parameters).

## Exploring parameters and averaging independent simulations on a cluster
Apart from running single experiments, you can also explore parameters with MPI (using mpi4py) and average over multiple instantiations of the same network. Both is done with the file `test_cluster.py`. For this you can:
* adapt the `submit-sleuth` file in the *utils* folder to your needs and run from the *common* folder `../utils/submit-sleuth -n N_JOBS test_cluster.py your_parameter_file`
* do this locally by running `mpirun -n N_JOBS python test_cluster.py your_parameter_file`

Please note that the code is written in such a way that `N_JOBS` has to match the number of different parameters to be explored (`c.cluster.params` in the parameter files).

## Parameter files for [Hartmann2015](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1004640)
The simulations will always produce more plots than there are in the paper.

**c** indicates cluster simulations. Most also return plots for single experiments.
* Fig.1: `chartmann.spont.param_spont_randletters`
* Fig.2: **c** `chartmann.spont.param_sequence` (switch the test_words in the param file to get both a and b)
* Fig.3: `chartmann.spont.param_spont`, **c** `chartmann.spont.param_fiser`
* Fig.4: **c** `chartmann.spont.param_spont`
* Fig.5: **c** `chartmann.spont.param_hesselmann`
* Fig.7: **c** `chartmann.spont.param_hesselmann` (set eta_stdp to 0 for b and d)
* Fig.8: `chartmann.spont.param_spont`

## Parameter files for [Hartmann2016](http://journal.frontiersin.org/article/10.3389/fncir.2015.00090/)
* Fig.1: see notebook `chartmann/alignment/kesten_notebbok/kesten_process_alignment.ipynb`
* Fig.2: see Fig.1
* Fig.3: see code at [http://fias.uni-frankfurt.de/~miner/](http://fias.uni-frankfurt.de/~miner/)
* Fig.4: `chartmann.alignment.param_alignment_potbias`
* Fig.5: `chartmann.alignment.param_alignment_depbias`
* Fig.6: see Fig.4 and Fig.5

## UML
The following simplified UML diagram sketches the simulation setup:
![UML-diagram](/doc/SORN_UML.png?raw=true)

When running `test_single.py` with a parameter file, the script will instantiate the *Experiment* specified in the parameters. The *Experiment* then creates *Sources* that specify the stimulation paradigm and *Stats* that record data at every time step. These *Stats* are then collected in a *StatsCollection* that will be an attribute of *Sorn*. Finally, the *Experiment* instantiates a *Sorn* with the parameters from the parameter file. The *Sorn* then instantiates a number of *SynapticMatrices* that can be either sparse or dense and will perform stdp or other plasticity mechanisms as specified in the parameters. After the initialization, the *Experiment* is `run` by the `test_single.py` script and the results are analyzed and plottet according to the `plot_single` method of the *Experiment*. If one runs multiple simulations with `test_cluster.py`, the *Experiment* is `reset` by the script before `run` is called again.



