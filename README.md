
# sigma-sph

Sigma-SPH is a covariance-based smoothed particle hydrodynamics. The kernel
suport is the optimal ellipsoid that fits the anisotropic k-nearest neighbors.

## Parallelization

The code is partially parallelized using Open MP directives.
The only parts not full parallelized are the procedures for
covariance-octree traversals, used both to perform the anisotropic
kNN and to perform gravity estimation. However, the quadrupole
approximation is performed using Open MP.

## Source directory

The source directory is in the _project_path_/sigma-sph/src and the include folder is in the relative path src/include

## How to compile

In order to compile the Sigma-SPH codes you have to check the sph-treecode quantities.c module in order to set (unset) cpp directives. For instance, uncomment the #define \_ADIABATIC\_ to run adiabatic simulation. A careful read in the code is required to understand the usage of such directives. After setting the cpp directives, all you have to do is giving a comand line "make all" to create the executables for isotropic and anisotropic simulations, using molecular or atomic hydrogen. Feel free to improve chemical composition other than pure hydrogen.

To perform the proposed multi-phase model you have to unset the \_ADIABATIC\_ flag and set #define \_MULTIPHASE\_MODEL\_2\_, which works combined with #define H2\_MOLECULE. In the latter simulation model the gas gas is "transparent" for densities below 22.1 computational unities (see arXiv paper) and behaves like "opaque" for greater densities.

## How to run

There exists in the subfolder src/scripts a bash script 
do-sigmasph-simulation.bash, which is required to perform
a sigma-sph simulation. The script is used as

do-sigmasph-simulation.bash _exec-path_ _series-name_ _i1_ _i2_ _K_ _DT_ _theta_ _epsilon_ _numthreads_

The best to perform a sigma-sph simulation is having a subdirectory
src/data where the series of output files is placed. Of course, it is
required an initial file, preferentially generated by Monte-Carlo. 
The initial file is a data-set where each line corresponds to an SPH
particle. The data-set has 12 columns according to

Column 1: mass

Column 2 - 4: coordinates

Column 5 - 7: velocity coordinates

Column 8: specific thermal energy

Column 9: old time step (leave 0 at first run)

Column 10: specific thermal energy rate (leave 0 at first run)

Column 11: density rate (leave 0 at first run)

Column 12: is gas flag (=1 if gas, =0 if not a gas particle)

There are other columns as the simulation outcome - inspect the file
sph-treecode-io.c to understand their meaning.

It is welcome to perform a one-step simulation to have the columns 9 and
beyond fulfilled.

For example, if we aim to perform an adiabatic simulation of cloud
collision, and we have an initial condition file named 
collision\_32k-0.data, from time-level 0 through time-level 1000, we do

../scripts/do-sigmasph-simulation.bash
../bin/sph-treecode\_anisotropic
collision\_32k- 0 1000 64 .0001220703125 .125 .027 20 2> collision\_32k-.log &

where the 1st parameter is the path for the executable file,
the 2nd parameter is the initial frame number (0 in the example),
the 3rd is the last frame number (1000 in the example)
the 4th is
the number of nearest neighbors, the 5th is the
major time step, the 6th is the aperture parameter, 7th is the softening
length, and the 8th is the number of threads for Open MP (check your system
properties before choosing a number of threads).

The standard output error redirection (using the descriptor 2) to a log file named collision\_32k.log is just a suggestion.

# Teste request