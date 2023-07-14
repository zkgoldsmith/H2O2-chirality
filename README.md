# Progress log for Katrina Mejia, CSI Princeton summer intern 2023

ZKG: We will use this Github markdown as well as [this Doc](https://docs.google.com/document/d/12itv_W-V2J_6J6nlkn0hcssBlbA7c94CLIHFn9NCGsg/edit?usp=sharing) to set and meet our agenda for this project. Our objectives at this point are the following:

- Establish proficiecy with Linux command line and vi(m) text editor
- Gain experience using CP2K to perform DFT calculations of small molecules in vacuum
- Demonstrate the chirality of H<sub>2</sub>O<sub>2</sub> via its potential energy surface for its dihedral angle
- Use visualization software on both the cluster and your local machine to depict molecular structure, calculate distances and angles, plot isosurfaces of frontier orbitals
- Use software (not Excel) to make plots of relevant data

We will both update this space with instructions, snippets of code, input files, plots, results, etc. as we go to document our progress.

## ZKG: DFT calculations of H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub> molecules with CP2K

You can find CP2K input files for H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub> on Della in subdirectories of `/home/zkg/Share/for-katrina`. Copy these files+directories to your `/scratch` directory with

```
cp -r /home/zkg/Share/for-katrina/H2* /path/to/your/scratch/
```

by filling in the path to your scratch. (Hint: if your present directory is your target directory then it is just `./`)

Once the files are in your scratch, start with the H<sub>2</sub>O single-point energy calculation, `cd H2O/energy`.

Review the CP2K input file `H2O.inp` by doing `vi H2O.inp`. If you built H2O in Avogadro and have the coordinates in an `*.xyz` file, delete the coordinates already there and replace them with those from your file. Otherwise you can use the coordinates already there. The coordinates are under the namelist `&COORD`.

Then, open the slurm submit script `job.sh`. Review the paramters at the top of the file such as the `job-name` and `time`. Then, in the `srun` command, change the path to reflect the CP2K `*.sif` file that you have installed in your Della `/scratch`. Once you have done that you can run the job from the command line:

```
sbatch job.sh
```

Now `cd ../../H2O2/energy` and do the same tasks: review the CP2K input and `job.sh` files, using your own H2O2 coordinates if you have them and making sure your own CP2K `*.sif` file is sourced in the submit script. Then, submit this job the same way.

If your job ran successfully, you can see the energy of the molecule with

```
grep "ENERGY|" [output file]
```

When the output file corresponds to a geometry optimization, you should get multiple calculated energies.

## KM: Results of DFT Calculation of H2O

After running the Single-Point Energy Calculation for H2O, the relevant data I collected was the end coordinates in the output file, the time spent runing the calculation, and the final energy values.

Time- H2O energy: 11.669 seconds


![image](https://github.com/zkgoldsmith/H2O2-chirality/assets/137853012/3cc681f1-9f84-421d-88f3-a539e3aedcf4)

After plotting the total energy of the H2O molecule in google sheets using CP2K, the graph has an exponential decay and remains stable at the eleventh step, reaching its lowest energy point at the last step, 41.

## ZKG: Linking and using pre-installed XCrysDen

I have copied over a pre-installed XCrysDen software package for you to do visualizations on the Della without any further installation. First add the following lines to your Della `~/.bashrc` file:

```
XCRYSDEN_TOPDIR=/home/zkg/Share/for-katrina/xcrysden-1.5.60-bin-semishared
XCRYSDEN_SCRATCH=/home/zkg/xcrysden_scratch
export XCRYSDEN_TOPDIR XCRYSDEN_SCRATCH
PATH="$XCRYSDEN_TOPDIR:$PATH:$XCRYSDEN_TOPDIR/scripts:$XCRYSDEN_TOPDIR/util"
```

Then do a `source ~/.bashrc`. Then, by simply using the command `xcrysden`, you should be able to open the program. You can read online about what file formats XCrysDen will support for you to quickly do visualizations and illustrate how e.g. the molecule went from it's inital to optimized geometry. You can `cd` to a directory where you performed a geometry optimization calculation and do the following:

```
xcrysden --xyz PROJECT-1-pos.xyz
```

And click through the prompts the GUI gives you as you deem appropriate.

## KM: Optimizations of H2O (CP2K)

Using CP2K to optimize the geometry of the H2O molecule provided me with the global minimum of H2O. The geometry optimization took 41.388 seconds. The geometry optimization calculation was 29.719 seconds longer than the single-point energy calculation. With the energy results that I got from the H2O.out file, I made a graph to visualize the energy decreasing. 

The XYZ coordinates before optimization are the following:

'''
O         -1.22146        2.10626        0.00000
H         -0.25146        2.10626        0.00000
H         -1.54479        2.59417        0.77350
'''

The XYZ coordinates after optimization for the final energy value of -17.2050491884 are the following:

```
O        -1.2499803158        2.0835319913        0.0002989149
H        -0.2707887169        2.1243561196        0.0289958752
H        -1.5134525431        2.6038259476        0.7886601011
```


## KM: Optimizations of H2O2 (CP2K)

We previously optimized H2O2 with seven different input xyz coordinates utilizing CP2K to recognize the lowest energy value. The first geometry optimization took 118.742 seconds. The geometry optimization calculation took 101.001 seconds longer than the single-point energy calculation. With the values of the global minima for H2O2, I made graphs and charts to record the lowest energy values and the degree of each dihedral angle. The lowest energy value from the geometry optimization of H2O2 is -33.1266130021056. 

The XYZ coordinates before optimization for this energy value are the following:

```
O             0.00000        0.00000        0.00000
  
O             1.50000        0.00000        0.00000

H             0.00000        1.00000        0.00000

H             1.50000        0.00000        1.00000
``` 



With the lowest energy value discovered, I am able to transition to begin AIMD of H2O2 to recognize the change H2O2 will have, now that temperature is a factor of change in energy values. 

## KM: Difference between the CP2K input files for the geometry optimization and Single-point Energy Calculation

```
<  RUN_TYPE ENERGY 
---
>  RUN_TYPE GEO_OPT 
5c5
<  WALLTIME 00:15:00
---
>  WALLTIME 00:25:00
6a7,12
> &MOTION
>  &GEO_OPT
>   OPTIMIZER BFGS #CG
>   MAX_ITER 40
>  &END GEO_OPT
> &END MOTION
11c17
<   POTENTIAL_FILE_NAME ./POTENTIAL
---
>   POTENTIAL_FILE_NAME ./POTENTIAL 
58,60c64,66
< O         -2.77888        1.76816        0.00000
< H         -1.80888        1.76816        0.00000
< H         -3.20855        2.45255        0.83837
---
> O         -1.22146        2.10626        0.00000
> H         -0.25146        2.10626        0.00000
> H         -1.54479        2.59417        0.77350
```

## KM: Visualization (Avogadro)

H2O molecule

bond lengths/angles BEFORE geometry optimization:

O-H bond length- 0.970 Angstroms 

HOH angle- 109.5 degrees


bond lengths angles AFTER geometry optimization:

O-H bond length- 0.980631 Angstroms

HOH angle- 109.471 degrees


H2O2 molecule

bond lengths/angles Before geometry optimization:
O-H bond length- 0.970 Angstroms

O-O bond length- 1.375 Angstroms

H-H bond length- 2.727 Angstroms

dihedral angle- -180.0 degrees


bond lengths/angles AFTER geometry optimization:

O-H bond length- 0.985 Angstroms

O-O bond length- 1.488 Angstroms 

H-H bond length- 2.622 Angstroms

dihedral angle- -179.333 degrees

## ZKG: Initializing an AIMD trajectory of H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub> using CP2K

I prepared a CP2K input and slurm script for you to begin AIMD of H2O2. Copy them from my shared directory:

```
cp /home/zkg/Share/for-katrina/H2O2/md/* [new directory in your scratch for AIMD]
```

You will find a few new aspects in the input file, feel free to do a `diff` command between this `*.inp` and previous ones.

- The `&EXT_RESTART` section will be how you indicate whether you are restarting a run. After your initial job, this will be uncommented (automatically through the job script) to restart from where you left off.
- The `RUN_TYPE` now says `MD` because we are performing molecular dynamics
- The `WALLTIME` is now `02:00:00` (2 hours) because we are going to accumulate data rather than converge to a solution.
- There is now the namelist `&MD`. You can read about the meaning of these options here. The most important one for now is `TEMPERATURE`, which is in Kelvin. We are running at a high temperature to try to sample many configurations (and dihedral angles).
- Also note the `TIMESTEP`, which is the $\Delta t$ (in femtoseconds) used in our Verlet integration.

Edit the `&COORD` section with any choice of optimized coordinates from your own calculations.

Now see how the `job.sh` differs from previous calculations. You will of course need to edit the path to the executable with your own, as you have done previously. There is also an `if` loop after the `srun` line:

```
if [ -e PROJECT-1.restart ]
 then
  sed -i 's/#RESTART_FILE/RESTART_FILE/g' H2O2.inp
  sbatch job.sh
 else
  exit
fi
```

This means that if there exists a file `PROJECT-1.restart`, then do a sed command to uncomment the `RESTART_FILE` line in the input and re-submit the `job.sh` file. This will essentially continuously restart your calculation automatically, 24/7, until you want to stop it yourself.

Once you’ve edited the input and job script as appropriate, go ahead and submit the calculation. We will discuss ways of checking and analyzing the output soon.

## ZKG: Monitoring and analyzing the AIMD trajectory of H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub>

You can check the progress of the AIMD run with:

```
head -n1 PROJECT-1.ener && tail -n1 PROJECT-1.ener
```

The `head -n1` command prints the first line of the file and the `tail -n1` command prints the last. You can you multiple commands in one entry by connecting them with `&&`. Post the output of this command here.

## KM: Progress of the AIMD Trajectory of H2O2
Using the command Zach provided:

```
head -n1 PROJECT-1.ener && tail -n1 PROJECT-1.ener
```

I recieved this output:

```
#     Step Nr.          Time[fs]        Kin.[a.u.]          Temp[K]            Pot.[a.u.]        Cons Qty[a.u.]        UsedTime[s]
     41206        41206.000000         0.011400535       800.000000000       -33.126123959       -33.112005524         5.262881273
```

This result tells me that the calculation stopped at 41,206 steps. The calculation ran for 42,206 femtoseconds. The kinetic energy, 0.011400535, and potential energy, -33.126123959, were given as astronomical units. The temperature was set at 800 kelvin and throughout the calculation, the kinetic energy was increased to keep the same temperature to decrease the velocity. If the potential energy is too low during the calculation, the calculation will speed up the velocity to reach the constant temperature set at 800 kelvin. The molecule is constantly moving and changing throughout the calculation. 

## ZKG: Continuation of Monitoring and analyzing the AIMD trajectory of H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub>

Let's also try to plot with `matplotlib` before we reach out to PRC about visualization. Make sure you are logged in to Della with `ssh -Y`. 

First, make sure you have the appropriate modular environment by doing `module load anaconda3/2023.3`.

Then copy the file I've prepared for you to the directory where your AIMD ran (run this from the AIMD directory):

```
cp /home/zkg/Share/for-katrina/H2O2/md/plot_pot_energy.py .
```

This is a Python script that will plot specified columns of the `PROJECT-1.ener` file. Execute it with:

```
python plot_pot_energy.py
```

This should take at least a few moments to work. If it works, send me or post here a screenshot of the plot; if it doesn't then email me the error message and we will troubleshoot from there.

## KM: Results of AIMD Calculation

The AIMD calculation differs from the single-point energy calculations and the geometry optimization calculations due to temperature being a factor, the amount of time used for the AIMD calculation, and the amount of times the AIMD calculation was ran. 

In order to visualize and transcribe the results, I needed to utilize the graphing tool on della, gnuplot. As I was having trouble accessing gnuplot, I was assisted by one of my post-doc's, Pablo Piaggi. He had sat with me and investigated my error message I kept recieving on della and made sure that I had XQuartz previously installed. He made sure that I was also logged in to della using:

```
ssh -Y [username]@della[-gpu].princeton.edu
```

Once I was logged in using the correct format, we restarted my local computer and eventually, gnuplot began working. 

To visualize how the temperature evolved throughout the calculation as a fuction of time, 

## NEB calculations of the barriers to interconversion of chiral H2O2 enantiomers

We will attempt to characterize the barriers to interconvert the two lowest energy enantiomers using Nudged Elastic Band (NEB) calculations in CP2K. NEB is a method for calculating intermediates between local minimum configurations using constrained optimizations that enforces the sampling of the reaction coordinate. A well-converged NEB calculation will approximate the minimum energy path (MEP) connecting the known minima. Climbing-image NEB (CI-NEB) furthermore will use the highest-energy intermediate to seek a saddle-point transition state configuration to best characterize the barrier. 

Find the input files in the two subdirectories of `/home/zkg/Share/for-katrina/H2O2/neb`. Make an NEB subdirectory of your scratch for the H2O2 calculations and do `cp -r /home/zkg/Share/for-katrina/H2O2/neb/* [target directory]` where the target directory is this new directory in your scratch. If you execute the `cp -r` from this directory then your target directory is just `.`.

You will run two CI-NEB calculations represented by the two subdirectories that you just copied, indicated by the higher energy cis and trans configurations of H2O2 that you have already established as local minima in the geometry optimizations. Let's look at the key differences of the NEB input files. This is from `.../cis_ts/H2O2.inp`:

```
&GLOBAL
 RUN_TYPE BAND
 FLUSH_SHOULD_FLUSH T
 PRINT_LEVEL MEDIUM
&END GLOBAL
&MOTION
 &BAND
  BAND_TYPE CI-NEB
  NUMBER_OF_REPLICA 9
  K_SPRING 0.05
  &CONVERGENCE_CONTROL
   MAX_FORCE 0.0050
   RMS_FORCE 0.0250
  &END
  &CI_NEB
   NSTEPS_IT 2
  &END
  &REPLICA
   COORD_FILE_NAME h2o2_ini.xyz
  &END
  &REPLICA
   COORD_FILE_NAME h2o2_cis.xyz
  &END
  &REPLICA
   COORD_FILE_NAME h2o2_fin.xyz
  &END
 &END BAND
&END MOTION
```

The `RUN_TYPE BAND` indicates that we are doing an NEB calculation; `BAND_TYPE CI-NEB` further specifies the method. `NUMBER_OF_REPLICA` indicates that we are using 9 images along the reaction path; this includes the initial and final configurations we will provide. The `NSTEPS_IT` keyword means that we will some (2) iterations of standard NEB before turning on the climbing image.

The various .xyz files under `&REPLICA \\ COORD_FILE_NAME` give the files containing the initial, transition state guess, and final coordinates from your optimizations.

Make sure to edit the CP2K executable in `job.sh` before running this (`sbatch job.sh`). Run both of them (`cis_ts` and `trans_ts`) for the indicated 3 hours and we will analyze them from there.

