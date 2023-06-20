# Progress log for Katrina Mejia, CSI Princeton summer intern 2023

We will use this Github markdown as well as [this Doc](https://docs.google.com/document/d/12itv_W-V2J_6J6nlkn0hcssBlbA7c94CLIHFn9NCGsg/edit?usp=sharing) to set and meet our agenda for this project. Our objectives at this point are the following:

- Establish proficiecy with Linux command line and vi(m) text editor
- Gain experience using CP2K to perform DFT calculations of small molecules in vacuum
- Demonstrate the chirality of H<sub>2</sub>O<sub>2</sub> via its potential energy surface for its dihedral angle
- Use visualization software on both the cluster and your local machine to depict molecular structure, calculate distances and angles, plot isosurfaces of frontier orbitals
- Use software (not Excel) to make plots of relevant data

We will both update this space with instructions, snippets of code, input files, plots, results, etc. as we go to document our progress.

## DFT calculations of H<sub>2</sub>O and H<sub>2</sub>O<sub>2</sub> molecules with CP2K

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
