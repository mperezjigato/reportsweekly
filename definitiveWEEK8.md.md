# Internship - Week 8 - Summary

## Computational Chemistry - HPC calculations for training material development

I have decided to get started with Quantum Espresso (QE) [^1], one of the best known first-principles quantum mechanical calculations codes for materials simulations. Work carried out with QE is described next, and the tables depicting our progress with the Computational Chemistry packages are updated accordingly (repository "computationalchemistry"). 

A set of independent runs has been carried out both sequentially and under the MPI parallel mode, although no systematic data are reported at this point (neither scaling studies nor walltime tables). On the other hand, the as-provided test-suite is currently being tried, as well as some  of the benchmark cases. Incidentally, it must be noted that all our CPU time on GENIUS has been consumed, and a new application for five million extra credits submitted [^2].

### Quantum Espresso 6.8 calculations

QE is based on a collection of executables/packages, at difference with most First-Principles Quantum Mechanical and Molecular Dynamics codes. Most of the calculations we are currently doing make use of the pw.x executable (PWscf package) for self-consisten field calculations.

Two as-provided independent systems as well as a system with input files built from scratch, have been run: 

 - GaAs PWscf calculation under PAW (as provided),
 - SiH4 -molecule in a box- PWscf calculation under norm-conserving pseudopotentials (as provided),
 - *Immm* Ag2PdO2 PWscf calculation under norm-conserving pseudopotentials (CIF geometry file downloaded from materialscloud.org, and processed on the QE input generator; reference input and output files are available as well). 
 
 While no further details are discussed regarding the first two cases, work on the third one deserves some comments (see subsection below). On the other hand, two QE benchmark cases are discussed as well. Moreover, some aspects of our work on the test-suite are reported below.
 
#### QE calculations on Ag2PdO2

The materialscloud.org website (Quantum ESPRESSO input generator and structure visualizer) lets us transform the corresponding CIF file (downloaded from the topological materials database) on QE inputs. Although a few calculations have been carried out on GENIUS, most of them have been based on trial-and-error input files, being run hence on the DSI guest2 laptop.  

#### QE calculations on two PRACE benchmark cases: Gold surface ("small") and Iridium carbide ("medium")

Calculations on a Gold surface with a unit cell of 112 atoms and on an Iridium carbide supercell with 443 atoms are reported next. Since we have run out of CPU time during the latter, we were only able to compare the wall-time for the sequential calculation with that of an MPI calculation on 1152 cores for the initialisation step previous to the start of the SCF cycle (not even a single SCF step could be completed): *18630.1 seconds (sequential run) vs 63.O seconds (1152 MPI run)* for the initialisation step. Just the Gold surface input/output files are uploaded in the "computationalchemistry" repository, as this is the only calculation that has been completed within the benchmarkl set.

Both job submission file and main input file are shown below for both cases, the Gold surface and the Iridum carbide supercell. It should be noted that extra parallelisation (mpirun) parameters are required for very large core numbers (1152), ie those corresponding to k-point (4) and to Davidson diagonalisation (16) parallelisation, whilst they do not seem to be necessary for small number of cores (in fact, we have tested it, and the calculation is slower for small core numbers, when using the extra parallelisation parameters).

## Computational Chemistry - Collection of specific CIF files, atomistic geometry visualisation and geometry conversion tools. 

The visualisation of atomistic geometries of materials and molecules has become crucial in simulations, therefore its importance in research and in supercomputing applications must be stressed. Materials simulation work requires the manipulation of geometry files (CIF, PDB, XSF, etc), making essential to visualise their geometry during the course of the simulations. The above PdAg2O2 QE calculations provide a good example, since conversions between the conventional cell (shown below) and the primitive cell, require the visualisation of both geometries. On the other hand, comparison with reference outputs also require the geometry visualisation stage (the software XCRYSDEN can visualise atomistic geometries from QE output files directly).

Atomistic geometry tools do not always work, even though strict CIF file validation/sanitation procedures are followed. For instance, the QE input file generator at materialscloud.org seems to partially fail on the body-centered orthorhombic PdAg2O2 as-provided CIF file, since it gives as output the QE input file for the 10 atom conventional cell, as opposed to the 5 atom primitive cell in the reference input/output files. XCRYSDEN allows visualising the reference output file provided by the same website, allowing a reconfirmation of the differences already seen in the calculated total energies (twice as much).

Another example of the above can be seen in the materials simulation academic course by Prof Dr Stefaan Cottenier [^3]. The combined use of both FINDSYM and CIF2CELL stumbles upon an inconsistency. As described by him in a video, this combination is very helpful when starting from a CIF file that corresponds to the output of a simulation or to any P1 conventional cell. On the one hand, the FINDSYM software (online run) identifies the space group of the supercell, and gives rise to a new supercell with symmetry. After running its output with CIF2CELL (simple ubuntu installation), we should be able to produce input files for QE, VASP, sprkkr, castep, etc. As a matter of fact, the second step fails, since CIF2CELL only understands Hall space-group terminology (FINDSYM produces only H-M), A simple solution based on modifying two lines is very helpful to the intern, since I have met exactly the same problem. On the other hand, CIF2CELL has its limitations, since it only produces QE input files without symmetry (ibrav=0), although the lattice information is really transparent.

A few atomistic visualisations are shown below, whilst the corresponding CIF files are uploaded to the GitHub repository "geometryfilesCIF_PDB_XSF_FASTA" (this repository contains all the geometry files and started from week 7's summary, which includes a couple of visualised geometries as well).

The Ag(16 10 9) surface structure [^4] is depicted below.

 ![](Ag16109.png)
 
On the other hand, the crystal structure (conventional cell) for the above topological material Ag2PdO2 is depicted next:

 ![](Ag2PdO2.png)

Finally, the modulated structure of the plagioclase feldspar CaxNa1-xAl1+xSi3-xO8 solid solution is shown below [^5]. This represents the textbook example of a solid solution.

 ![](plagioclasefeldspar.png)

NOTE: The GPAW test has been run sequentially. Since the output is a simple file, it is shown below. Moreover, the test script seeks the ELPA implementation, which obviously does not run with GPAW even after loading the ELPA module. A ticket to support (Genius) is to be submitted in order to have GPAW installed with ELPA.
```

  ___ ___ ___ _ _ _  
 |   |   |_  | | | | 
 | | | | | . | | | | 
 |__ |  _|___|_____|  21.6.0
 |___|_|             

User:   vsc35663@tier2-p-login-2
Date:   Thu Aug 17 16:19:14 2023
Arch:   x86_64
Pid:    547
Python: 3.9.5
gpaw:   /apps/leuven/skylake/2021a/software/GPAW/21.6.0-intel-2021a/lib/python3.9/site-packages/gpaw
_gpaw:  /apps/leuven/skylake/2021a/software/GPAW/21.6.0-intel-2021a/lib/python3.9/site-packages/
        _gpaw.cpython-39-x86_64-linux-gnu.so
ase:    /apps/leuven/skylake/2021a/software/ASE/3.22.0-intel-2021a/lib/python3.9/site-packages/ase (version 3.22.0)
numpy:  /apps/leuven/skylake/2021a/software/SciPy-bundle/2021.05-foss-2021a/lib/python3.9/site-packages/numpy (version 1.20.3)
scipy:  /apps/leuven/skylake/2021a/software/SciPy-bundle/2021.05-foss-2021a/lib/python3.9/site-packages/scipy (version 1.6.3)
libxc:  5.1.5
units:  Angstrom and eV
cores: 1
OpenMP: False
OMP_NUM_THREADS: 1

Input parameters:
  kpts: [1 1 8]
  mode: {ecut: 200.0,
         gammacentered: False,
         name: pw}

System changes: positions, numbers, cell, pbc, initial_charges, initial_magmoms 

Initialize ...

H-setup:
  name: Hydrogen
  id: 4766778ce56282eaa64abeb28b7c1de3
  Z: 1.0
  valence: 1
  core: 0
  charge: 0.0
  file: /apps/leuven/skylake/2021a/software/GPAW-setups/0.9.20000/H.LDA.gz
  compensation charges: gauss, rc=0.15, lmax=2
  cutoffs: 0.85(filt), 0.53(core),
  valence states:
                energy  radius
    1s(1.00)    -6.353   0.476
    *s          20.858   0.476
    *p           0.000   0.476

  Using partial waves for H as LCAO basis

Reference energy: -12.128958

Spin-paired calculation

Convergence criteria:
  Maximum total energy change: 0.0005 eV / electron
  Maximum integral of absolute density change: 0.0001 electrons
  Maximum integral of absolute eigenstate change: 4e-08 eV^2
  Maximum number of iterations: 333

Symmetries present (total): 16

  ( 1  0  0)  ( 1  0  0)  ( 1  0  0)  ( 1  0  0)  ( 0  1  0)  ( 0  1  0)
  ( 0  1  0)  ( 0  1  0)  ( 0 -1  0)  ( 0 -1  0)  ( 1  0  0)  ( 1  0  0)
  ( 0  0  1)  ( 0  0 -1)  ( 0  0  1)  ( 0  0 -1)  ( 0  0  1)  ( 0  0 -1)

  ( 0  1  0)  ( 0  1  0)  ( 0 -1  0)  ( 0 -1  0)  ( 0 -1  0)  ( 0 -1  0)
  (-1  0  0)  (-1  0  0)  ( 1  0  0)  ( 1  0  0)  (-1  0  0)  (-1  0  0)
  ( 0  0  1)  ( 0  0 -1)  ( 0  0  1)  ( 0  0 -1)  ( 0  0  1)  ( 0  0 -1)

  (-1  0  0)  (-1  0  0)  (-1  0  0)  (-1  0  0)
  ( 0  1  0)  ( 0  1  0)  ( 0 -1  0)  ( 0 -1  0)
  ( 0  0  1)  ( 0  0 -1)  ( 0  0  1)  ( 0  0 -1)

8 k-points: 1 x 1 x 8 Monkhorst-Pack grid
4 k-points in the irreducible part of the Brillouin zone
       k-points in crystal coordinates                weights
   0:     0.00000000    0.00000000    0.06250000          2/8
   1:     0.00000000    0.00000000    0.18750000          2/8
   2:     0.00000000    0.00000000    0.31250000          2/8
   3:     0.00000000    0.00000000    0.43750000          2/8

Wave functions: Plane wave expansion
  Cutoff energy: 200.000 eV
  Number of coefficients (min, max): 30, 42
  Pulay-stress correction: 0.000000 eV/Ang^3 (de/decut=0.000000)
  Using FFTW library
  ScaLapack parameters: grid=1x1, blocksize=None
  Wavefunction extrapolation:
    Improved wavefunction reuse through dual PAW basis 

Occupation numbers: Fermi-Dirac: width=0.1000 eV
 

Eigensolver
   Davidson(niter=2) 

Densities:
  Coarse grid: 9*9*3 grid
  Fine grid: 18*18*6 grid
  Total Charge: 0.000000 

Density mixing:
  Method: separate
  Backend: pulay
  Linear mixing parameter: 0.05
  Mixing with 5 old densities
  Damping of long wave oscillations: 50 

Hamiltonian:
  XC and Coulomb potentials evaluated on a 18*18*6 grid
  Using the LDA Exchange-Correlation functional
 

Memory estimate:
  Process memory now: 155.08 MiB
  Calculator: 0.16 MiB
    Density: 0.10 MiB
      Arrays: 0.05 MiB
      Localized functions: 0.04 MiB
      Mixer: 0.02 MiB
    Hamiltonian: 0.03 MiB
      Arrays: 0.03 MiB
      XC: 0.00 MiB
      Poisson: 0.00 MiB
      vbar: 0.00 MiB
    Wavefunctions: 0.03 MiB
      Arrays psit_nG: 0.00 MiB
      Eigensolver: 0.00 MiB
      Projections: 0.00 MiB
      Projectors: 0.01 MiB
      PW-descriptor: 0.01 MiB

Total number of cores used: 1

Number of atoms: 1
Number of atomic orbitals: 1
Number of bands in calculation: 1
Number of valence electrons: 1
Bands to converge: occupied

... initialized

Initializing position-dependent things.

Density initialized from atomic densities
Creating initial wave functions:
  1 band from LCAO basis set

   .-----.  
  /.-----.  
 */     /   
 H-----*    

Positions:
   0 H      0.000000    0.000000    0.000000    ( 0.0000,  0.0000,  0.0000)

Unit cell:
           periodic     x           y           z      points  spacing
  1. axis:    no     2.500000    0.000000    0.000000     9     0.2778
  2. axis:    no     0.000000    2.500000    0.000000     9     0.2778
  3. axis:    yes    0.000000    0.000000    0.900000     3     0.3000

  Lengths:   2.500000   2.500000   0.900000
  Angles:   90.000000  90.000000  90.000000

Effective grid spacing dv^(1/3) = 0.2850

                     log10-error:    total        iterations:
           time      wfs    density  energy       poisson
iter:   1  16:19:14                  -4.727648           
iter:   2  16:19:14  -1.25  -0.92    -4.731580           
iter:   3  16:19:14  -2.10  -0.89    -4.758682           
iter:   4  16:19:15  -1.39  -0.71    -4.711326           
iter:   5  16:19:15  -2.66  -1.81    -4.714499           
iter:   6  16:19:15  -2.65  -1.34    -4.710863           
iter:   7  16:19:15  -5.03  -2.93    -4.710868           
iter:   8  16:19:15  -7.17  -2.82    -4.710868           
iter:   9  16:19:15  -5.83  -2.82    -4.710864           
iter:  10  16:19:15  -7.43  -3.36    -4.710864           
iter:  11  16:19:15  -7.46  -3.47    -4.710864           
iter:  12  16:19:15  -7.42  -3.75    -4.710864           
iter:  13  16:19:15  -9.06  -5.12    -4.710864           

Converged after 13 iterations.

Dipole moment: (0.000000, 0.000000, -0.000000) |e|*Ang

Energy contributions relative to reference atoms: (reference = -12.128958)

Kinetic:         -0.546804
Potential:       -1.736074
External:        +0.000000
XC:              -2.418621
Entropy (-ST):   -0.000000
Local:           -0.009365
--------------------------
Free energy:     -4.710864
Extrapolated:    -4.710864

Showing only first 2 kpts
 Kpt  Band  Eigenvalues  Occupancy
  0     0    -13.13574    2.00000

  1     0     -7.39839    2.00000


Fermi level: -2.20476

No gap

Forces in eV/Ang:
  0 H     0.00000    0.00000    0.00000

Stress tensor:
     0.666757     0.000000     0.000000
     0.000000     0.666757     0.000000
     0.000000     0.000000    -0.501774
Timing:                              incl.     excl.
-----------------------------------------------------------
Forces:                              0.002     0.002   0.2% |
Hamiltonian:                         0.021     0.000   0.0% |
 Atomic:                             0.005     0.000   0.0% |
  XC Correction:                     0.005     0.005   0.5% |
 Calculate atomic Hamiltonians:      0.015     0.015   1.7% ||
 Communicate:                        0.000     0.000   0.0% |
 Initialize Hamiltonian:             0.000     0.000   0.0% |
 Poisson:                            0.000     0.000   0.0% |
 XC 3D grid:                         0.001     0.001   0.1% |
LCAO initialization:                 0.167     0.004   0.5% |
 LCAO eigensolver:                   0.056     0.000   0.0% |
  Calculate projections:             0.000     0.000   0.0% |
  DenseAtomicCorrection:             0.000     0.000   0.0% |
  Distribute overlap matrix:         0.000     0.000   0.0% |
  Orbital Layouts:                   0.036     0.036   4.0% |-|
  Potential matrix:                  0.004     0.004   0.5% |
  Sum over cells:                    0.015     0.015   1.7% ||
 LCAO to grid:                       0.001     0.001   0.1% |
 Set positions (LCAO WFS):           0.106     0.008   0.9% |
  Basic WFS set positions:           0.001     0.001   0.1% |
  Basis functions set positions:     0.000     0.000   0.0% |
  P tci:                             0.009     0.009   1.0% |
  ST tci:                            0.043     0.043   4.8% |-|
  mktci:                             0.045     0.045   5.1% |-|
PWDescriptor:                        0.088     0.088   9.8% |---|
SCF-cycle:                           0.209     0.006   0.7% |
 Davidson:                           0.100     0.028   3.1% ||
  Apply H:                           0.003     0.002   0.3% |
   HMM T:                            0.000     0.000   0.0% |
  Subspace diag:                     0.016     0.003   0.3% |
   calc_h_matrix:                    0.008     0.005   0.6% |
    Apply H:                         0.003     0.003   0.3% |
     HMM T:                          0.000     0.000   0.0% |
   diagonalize:                      0.003     0.003   0.4% |
   rotate_psi:                       0.002     0.002   0.2% |
  calc. matrices:                    0.038     0.032   3.6% ||
   Apply H:                          0.005     0.005   0.5% |
    HMM T:                           0.001     0.001   0.1% |
  diagonalize:                       0.014     0.014   1.5% ||
  rotate_psi:                        0.002     0.002   0.3% |
 Density:                            0.017     0.000   0.0% |
  Atomic density matrices:           0.005     0.005   0.5% |
  Mix:                               0.006     0.006   0.7% |
  Multipole moments:                 0.001     0.001   0.1% |
  Pseudo density:                    0.006     0.002   0.2% |
   Symmetrize density:               0.004     0.004   0.4% |
 Hamiltonian:                        0.069     0.002   0.2% |
  Atomic:                            0.058     0.004   0.4% |
   XC Correction:                    0.055     0.055   6.1% |-|
  Calculate atomic Hamiltonians:     0.002     0.002   0.3% |
  Communicate:                       0.000     0.000   0.0% |
  Poisson:                           0.000     0.000   0.1% |
  XC 3D grid:                        0.006     0.006   0.6% |
 Orthonormalize:                     0.018     0.000   0.0% |
  calc_s_matrix:                     0.016     0.016   1.8% ||
  inverse-cholesky:                  0.001     0.001   0.1% |
  projections:                       0.000     0.000   0.0% |
  rotate_psi_s:                      0.000     0.000   0.0% |
Set symmetry:                        0.004     0.004   0.4% |
Stress:                              0.021     0.000   0.0% |
 Stress tensor:                      0.021     0.021   2.3% ||
Other:                               0.387     0.387  43.1% |----------------|
-----------------------------------------------------------
Total:                                         0.898 100.0%

Memory usage: 157.07 MiB
Date: Thu Aug 17 16:19:15 2023
```

[^1]: www.quantum-espresso.org
[^2]: https://admin.kuleuven.be/icts/onderzoek/hpc/extra-project-credits
[^3]: crystallography section at https://beta.compmatphys.org/ 
[^4]: Both CIF file and atomic structure image of Ag(16 10 9) have been provided by Dr Stephen J Jenkins.
[^5]: It has been downloaded from The Bilbao Incommensurate Crystal Structure Database at https://www.cryst.ehu.es/
