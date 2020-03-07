# GNM UPDATE

## Performance

Utilizing the gnm optimizer can enable researchers to calculate faster and better results of their respective protein-ligand docking problem on very few resources. That is if you are used to working with the popular autodock vina/smina docking software.

A comparison of computational speed and accuracy is exhibitable in the following:

![Image of SpeednAcc](../assets/speedacc.png?raw=true)

The image shows a comparisaon of computation time and computed energies of target human glyci-
namide ribonucleotide synthetase (PDB: 2QK4, [DUD-Database 2006](http://dud.docking.org/r2/)) with Autodock
Smina (red) and the projected global newton method (blue)

The Receiver-Operator-Characteristic (ROC) of the calucations shows that the computed energies are indeed resulting in more accurate Rankings of true binders as decoy compounds:

### ROC SMINA vs GNM

![Image of ROCSMINA](../assets/roccompare.png?raw=true)

Computaion of Protein-Ligand-Docking problem and evaluation of the Receiver-Operation-Characteristic of PDB: 2QK4, [DUD-Database 2006](http://dud.docking.org/r2/)) Left: Using Autodock Smina form Results shown in the Performance-Section
 (red) Right: Using the projected global newton method (blue).

## Reference

This work has been a collaboration with Konrad-Zuse-Institue Berlin and Technical University Berlin. If you want to get more insight in the mathematical workings, you can find further reference [here](https://opus4.kobv.de/opus4-zib/frontdoor/index/index/start/4/rows/10/sortfield/score/sortorder/desc/searchtype/simple/query/Bender/docId/7707)

Please cite accordingly if you find this useful.

### Preliminaries

* [Openbabel](http://openbabel.org/wiki/Main_Page)
* [Boost Libraries](https://www.boost.org/)

#### Optional

* [Eclipse IDE for C/C++ Developers Version: 2019-12 (4.14.0)](https://www.eclipse.org/downloads/packages/release/kepler/sr2/eclipse-ide-cc-developers)
* [MGLTools](http://mgltools.scripps.edu/downloads)

## Setup - Source Development

This fork of smina includes a disclosed (FINALLY!) algorithm for global optimization. So far the project is setup to be build and developed from source on IDEs:

Eclipse IDE for C/C++ Developers Version: 2019-12 (4.14.0)

Eclipse IDE for C/C++ Developers Version: Oxygen.3a Release (4.7.3a)

The project has been setup such that you can import it as an existing project to workspace:

![Image of Import](../assets/import.png?raw=true)

Other versions of eclipse might also work fine. 

Everybody who is a commandline enthusiast can treat the project as a makefile-project as usual.

 
# Usage

The program is executed as usual with the following output upon parameterless execution.
```
Input:
  -r [ --receptor ] arg rigid part of the receptor (PDBQT)
  --flex arg            flexible side chains, if any (PDBQT)
  -l [ --ligand ] arg   ligand(s)
  --flexres arg         flexible side chains specified by comma separated list 
                        of chain:resid
  --flexdist_ligand arg Ligand to use for flexdist
  --flexdist arg        set all side chains within specified distance to 
                        flexdist_ligand to flexible

Search space (required):
  --center_x arg        X coordinate of the center
  --center_y arg        Y coordinate of the center
  --center_z arg        Z coordinate of the center
  --size_x arg          size in the X dimension (Angstroms)
  --size_y arg          size in the Y dimension (Angstroms)
  --size_z arg          size in the Z dimension (Angstroms)
  --autobox_ligand arg  Ligand to use for autobox
  --autobox_add arg     Amount of buffer space to add to auto-generated box 
                        (default +4 on all six sides)
  --no_lig              no ligand; for sampling/minimizing flexible residues

Scoring and minimization options:
  --scoring arg                specify alternative builtin scoring function [e.g. vinardo]
  --custom_scoring arg         custom scoring function file
  --custom_atoms arg           custom atom type parameters file
  --score_only                 score provided ligand pose
  --local_only                 local search only using autobox (you probably 
                               want to use --minimize)
  --minimize                   energy minimization
  --randomize_only             generate random poses, attempting to avoid 
                               clashes
  --minimize_iters arg (=0)    number iterations of steepest descent; default 
                               scales with rotors and usually isn't sufficient 
                               for convergence
  --accurate_line              use accurate line search
  --gnm                        Enable the global newton method. Default is 
                               false
  --minimize_early_term        Stop minimization before convergence conditions 
                               are fully met.
  --approximation arg          approximation (linear, spline, or exact) to use
  --factor arg                 approximation factor: higher results in a 
                               finer-grained approximation
  --force_cap arg              max allowed force; lower values more gently 
                               minimize clashing structures
  --user_grid arg              Autodock map file for user grid data based 
                               calculations
  --user_grid_lambda arg (=-1) Scales user_grid and functional scoring
  --print_terms                Print all available terms with default 
                               parameterizations
  --print_atom_types           Print all available atom types

Output (optional):
  -o [ --out ] arg      output file name, format taken from file extension
  --out_flex arg        output file for flexible receptor residues
  --log arg             optionally, write log file
  --atom_terms arg      optionally write per-atom interaction term values
  --atom_term_data      embedded per-atom interaction terms in output sd data

Misc (optional):
  --cpu arg                  the number of CPUs to use (the default is to try 
                             to detect the number of CPUs or, failing that, use
                             1)
  --seed arg                 explicit random seed
  --exhaustiveness arg (=8)  exhaustiveness of the global search (roughly 
                             proportional to time)
  --num_modes arg (=9)       maximum number of binding modes to generate
  --energy_range arg (=3)    maximum energy difference between the best binding
                             mode and the worst one displayed (kcal/mol)
  --min_rmsd_filter arg (=1) rmsd value used to filter final poses to remove 
                             redundancy
  -q [ --quiet ]             Suppress output messages
  --addH arg                 automatically add hydrogens in ligands (on by 
                             default)

Configuration file (optional):
  --config arg          the above options can be put here

Information (optional):
  --help                display usage summary
  --help_hidden         display usage summary with hidden options
  --version             display program version

```

The additional option `--gnm` is by default false, however usage of the original smina functions is to be dealt with caution. They have not been tested with the current implementation. The `--exhaustiveness` parameter will be a measure of how many gnm optimizations will be run. Setting this option with `--exhaustivenes 10` will result in ten runs of the gnm optimizer once gnm option is set to `--gnm 1`. smina will output the list of best runs of the ten runs. The parameter `--minimize_iters` will specify the number of iterations of each optimization run of gnm. 

There is a trade-off between number of optimizations specified with the `--exhaustiveness`-parameter and the `--minimize_iters`-parameter. Empirically the method has shown a good performance with the ratio of `--exhaustiveness 300` and `--minimize_iters 500` but results may vary with different cases.

The optimizer can be configured with a configuration file. You can find an example in the example folder in the `conf.txt`-file. Additional pdbt-files for a test run can be found in the example folder as well. Once you have a build of the software ready, put the the built executable into the example folder and run:

```cpp
  ./smina --config conf.txt
```
To further explain what happens with this configuration, lets run this in the example folder:
```cpp
  $ cat conf.txt
  
    receptor = gart.pdbqt
    ligand = xx07.pdbqt
    out = test.pdbqt

    center_x = 19.6
    center_y = 2.3
    center_z = 22.6
    size_x = 62.0
    size_y = 69.7
    size_z = 70.2

    ### mandatory do not change ####
    local_only = false
    gnm = true
    minimize = 1
    ################################
    
    approximation = linear
    cpu = 2
    scoring = vinardo
    
    exhaustiveness = 200
    minimize_iters = 500
```
Everything above `size_z` should be easy to understand. Those variables specify the box and its position to the given receptor-molecule. You can find out how adjust them to your specific problem by using [MGLTools](http://mgltools.scripps.edu/downloads). The author of Autodock Vina gives a exemplatory introduction of how to set those variables with this [Autodock Vina](http://vina.scripps.edu/tutorial.html). This can be done for our purposes just alike.

We set `local_only = false`,`minimize = 1` and `gnm = true` to use the gnm-optimizer. Do not change this if you want to use the program.

`approximation = linear` can be chosen as you like. Choose between `approximation = spline` and `approximation = exact`. This parameter will most likely depend on your problem. However `approximation = linear` will give you a rough picture of how your compound will bind. Essentially it is more memory intensive.

`cpu = 2` will set you up to use parallel optimization runs. In this case you are using two cores or hyperthreads and the speed up of your calculation will run twice as fast! Run with as many CPUs as possible to get fastest results.

`scoring = vinardo` will use the built in vinardo scoring function.

The parameters `exhaustiveness` and `minimize_iters` can be chosen arbitrarily. As mentioned before. This seemed to be a good trade-off in computation time and accuracy.

# Custom Scoring

The custom scoring file consists of a weight, term description, and optional
comments on each line.  The numeric parameters of the term description 
can be varied to parameterize the scoring function.  
Use --print_terms to see all available terms.

Example (all weights 1.0, all term types listed):
```
1.0  ad4_solvation(d-sigma=3.6,_s/q=0.01097,_c=8)  desolvation, q determines whether value is charge dependent
1.0  ad4_solvation(d-sigma=3.6,_s/q=0.01097,_c=8)  in all terms, c is a distance cutoff
1.0  electrostatic(i=1,_^=100,_c=8)	i is the exponent of the distance, see everything.h for details
1.0  electrostatic(i=2,_^=100,_c=8)
1.0  gauss(o=0,_w=0.5,_c=8)		o is offset, w is width of gaussian
1.0  gauss(o=3,_w=2,_c=8)
1.0  repulsion(o=0,_c=8)	o is offset of squared distance repulsion
1.0  hydrophobic(g=0.5,_b=1.5,_c=8)		g is a good distance, b the bad distance
1.0  non_hydrophobic(g=0.5,_b=1.5,_c=8)	value is linearly interpolated between g and b
1.0  vdw(i=4,_j=8,_s=0,_^=100,_c=8)	i and j are LJ exponents
1.0  vdw(i=6,_j=12,_s=1,_^=100,_c=8) s is the smoothing, ^ is the cap
1.0  non_dir_h_bond(g=-0.7,_b=0,_c=8)	good and bad
1.0  non_dir_h_bond_quadratic(o=0.4,_c=8) like repulsion, but for hbond, don't use	
1.0  non_dir_h_bond_lj(o=-0.7,_^=100,_c=8)	LJ 10-12 potential, capped at ^
1.0 acceptor_acceptor_quadratic(o=0,_c=8)	quadratic potential between hydrogen bond acceptors
1.0 donor_donor_quadratic(o=0,_c=8)	quadratic potential between hydroben bond donors
1.0  num_tors_div	div constant terms are not linearly independent
1.0  num_heavy_atoms_div	
1.0  num_heavy_atoms	these terms are just added
1.0  num_tors_add
1.0  num_tors_sqr
1.0  num_tors_sqrt
1.0  num_hydrophobic_atoms
1.0  ligand_length
```

Atom Type Terms
You can define custom functionals between pairs of specific atom types:

```
atom_type_gaussian(t1=,t2=,o=0,_w=0,_c=8)	guassian potential between specified atom types
atom_type_linear(t1=,t2=,g=0,_b=0,_c=8)	linear potential between specified atom types
atom_type_quadratic(t1=,t2=,o=0,_c=8)	quadratic potential between specified atom types
atom_type_inverse_power(t1=,t2=,i=0,_^=100,_c=8)	inverse power potential between specified atom types
```
Use `--print_atom_types` to see all available atom types. Note that hydrogens
are always ignored despite having atom types.

Results from the performace section have been computed using the build-in vinardo-scoring function. One of functionalities that come with smina is to define custom socring functions. The parameters of the scoring function are as follows:
```
-0.045       gauss(o=0,_w=10.8,_c=8)
0.8          repulsion(o=0,_c=8)
-0.035       hydrophobic(g=0,_b=2.5,_c=8)
-0.6         non_dir_h_bond(g=-0.6,_b=0,_c=8)
0            num_tors_div
```
These have produced good results as exhibited by [Quiroga R. and Villarreal MA.](https://doi.org/10.1371/journal.pone.0155183). See for further reference.
