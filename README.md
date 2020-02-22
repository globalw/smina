# GNM UPDATE

## Setup - Source Development

This fork of smina includes a disclosed (FINALLY!) algorithm for global optimization. So far the project is setup to be build and developed from source on IDEs:

Eclipse IDE for C/C++ Developers Version: 2019-12 (4.14.0)
Eclipse IDE for C/C++ Developers Version: Oxygen.3a Release (4.7.3a)

It has been setup such that you can import the project as an existing project to workspace:

![Image of Import](../assets/import.png?raw=true)

other versions of eclipse might also work fine. Everybody who is a commandline enthusiast can treat the project as a makefile-project as usual.

## Performance

Utilizing the gnm optimizer can enable researchers to calculate faster and better results of their respective protein-ligand docking problem on very few resources. That is based on the popular autodock vina/smina docking software.

A comparison of computational speed and accuracy is exhibitable in the following:

![Image of SpeednAcc](../assets/speedacc.png?raw=true)

The image shows a comparisaon of computation time and computed energies of target human glyci-
namide ribonucleotide synthetase (PDB: 2QK4, [DUD-Database 2006](http://dud.docking.org/r2/)) with Autodock
Smina (red) and the stochastic global newton method (blue)

The Receiver-Operator-Characteristic (ROC) of the calucations show that the computed energies are indeed resulting in more accurate Rankings of true binders as decoy compounds:

### ROC GNM
![Image of ROCSMINA](../assets/roccomparegnm.png?raw=true)

### ROC SMINA

![Image of ROCSMINA](../assets/roccomparesmina.png?raw=true)


## Usage

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

The optimizer can be configured with a configuration file. You can find an example in the example folder in the `conf.txt`-file. Additional pdbt-files for a test run can be found in the example folder as well.


The custom scoring file consists of a weight, term description, and optional
comments on each line.  The numeric parameters of the term description 
can be varied to parameterize the scoring function.  
Use --print_terms to see all available terms.

Example (all weights 1.0, all term types listed):
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

Note that these are all symmetric - you do not need to specify a term for
(t1,t2) and (t2,t1) (doing so will just double the value of the potential).
```
Example:  Faking covalent docking.  Consider this custom scoring function:
-0.035579    gauss(o=0,_w=0.5,_c=8)
-0.005156    gauss(o=3,_w=2,_c=8)
0.840245     repulsion(o=0,_c=8)
-0.035069    hydrophobic(g=0.5,_b=1.5,_c=8)
-0.587439    non_dir_h_bond(g=-0.7,_b=0,_c=8)
1.923        num_tors_div
-100.0       atom_type_gaussian(t1=Chlorine,t2=Sulfur,o=0,_w=3,_c=8)
```
All but the last term are the default Vina scoring function.  That last
term applys a very strong guassian potential between Cl and S.  In the
system we were docking, we modified the two atoms we wanted to be next
to each other (because they are known form a covalent bond) to be a chlorine
and a sulfur (the system is not physical, but that's okay).  Since these
were the only Cl and S in the system and the term has a large weight,
the best docking solutions all placed these atoms together.
The final poses could then be rescored/minimized using just the default
scoring function.
