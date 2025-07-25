# W+JetsBaseline

I. INTRODUCTION

This is a GitHub repository created to store the W+Jets baseline simulation (without matching & merging).  All of the supporting files relevant to the simulation can also be found here, as well as in the tarball, which I uploaded as a release asset (see "Releases").  I've also included the code used to analyze the outputs of the Pythia simulation in a second tarball titled WplusJetsAnalysis.  Within it, you will find the detector simulation which implements event reconstruction as well as the analysis and plotting code (the plotting code is very messy, but can be used as a starting point for generating your own graphics).

This document serves as a guide to the various files required to run the simulations, but it should not be regarded as exhaustive!  Please feel free to direct any questions to the author, Ben Johnson: bjohns18@tufts.edu.

The first step would be to retrieve the code and supporting materials from the GitHub.  To do so, navigate to a directory on the cluster where you would like to store everything, then create a directory for the new code with "mkdir WplusJetsBaseline".  Open it with "cd WplusJetsBaseline".  Then, input "wget https://github.com/bjohns664297/WplusJets/releases/download/WplusJetsBaseline/WplusJetsBaseline.tgz".  Finally, unpack the tarball with "tar -xzvf WplusJetsBaseline.tgz".  Similar steps should be repeated for the analysis tarball stored at https://github.com/bjohns664297/WplusJets/releases/download/WplusJetsAnalysis/WplusJetsAnalysis.tgz.

II. ROOT SETUP

After unpacking the WplusJetsBaseline tarball, you should see the main code, VjetsPythia8.cc, in addition to a host of supporting files.  Before anything else, it's good form to switch off the login node you're connected to by accessing the cluster via SSH.  You should also initialize ROOT from the CERN Virtual Machine File System (CVMFS).  This can be accomplished easily by accessing the text file included in the WplusJetsAnalysis tarball named RootCopyPasta.txt (it's also stored separately in the repo).  NOTE: there are a few methods of connecting to a computing node and loading ROOT included in this file.  You should only run four lines of code total (you can copy and paste them directly into the terminal).  The first will begin with "srun" (this connects you to a node, and I generally recommend the "largemem" version); then, the next two will begin with "export," then "source" (these define a path to where the ROOT versions are stored in the CVMFS, and you should wait a few seconds after running the "source" command); finally, end with one of the lines beginning in "lsetup" to complete the ROOT initialization.  Since this code was compiled with Pythia8.302, you should use the “lsetup” command labeled with that version.  If all goes well, you should now be connected to a computing node running ROOT.  Now, let’s review the various files in the repo to summarize their roles.  First, we can cover the command files.

III. COMMAND FILES

The command files end in .cmnd, and they provide instructions for Pythia to run the physics simulations.  There is one primary command file and two support files.  The primary file is named to match the main code, VjetsPythia8.cmnd, and must be supplied as a command line input when running the script so that Pythia can perform the simulation as desired.

The two support command files are read in by the main program automatically, and they contain a variety of settings and parameters relevant to the simulation.  These include, but are not limited to, the number of events, the random seed, the physics tunes, and a variety of Standard Model values like particle masses.  It's essential to properly adjust the support command files when running the simulations, but you don't need to include them on the command line.

IV. SLURM SCRIPTS

Next, we can cover the .sbatch file.  This is a Slurm script which directs a cluster node to run the Pythia simulation.  There’s only one in the baseline tarball to call the executable with the associated command file.  It should be relatively straightforward, but, in a nutshell, it requests time on a node, establishes output files for information storage, ensures ROOT setup, then runs the executable.

V. SOURCE FILES

Then, we also need to include a set of dependencies which define assorted classes and functions called by the main program.  The header files all end in .h or .hpp, and the other source files end in .cc.  The primary header shares the same name as the main program: VjetsPythia8.h, but it calls include statements for the other headers: ANA_utils.h(.cc), StdArg.hpp, TruthJets.h(.cc), TruthPart.h(.cc).  The dependencies also have associated object files ending in .o which are created by the compiler before linking everything into the main executable.  Their names match their respective source files: ANA_utils.o, TruthJets.o, TruthPart.o, and the main object file, VjetsPythia8.o.  The compiler names the final executable to match the code, but without an ending--it's simply called VjetsPythia8. This will eventually be run with the command "./VjetsPythia8 VjetsPythia8.cmnd" (either in the terminal or in a Slurm script, and flags can be added to this command for some additional options: for example, “-outroot” followed by a file name specifies a ROOT file for output storage).  Slurm scripts are generally preferable as they allow for parallel computing; running the simulation from the command line will occupy the terminal.  NOTE: ROOT must be initialized first!

VI. MAKEFILES

Finally, the last relevant files in the tarball are titled Makefile and Makefile.inc.  The Makefile provides instructions to the compiler when building the simulation, and the Makefile.inc includes the paths to the modules on which the code depends.  After making any changes to the code, you should save the file, then run the command “make” in the terminal (in the same directory where the code is stored) to recompile and update the executable.  NOTE: while the Makefile itself should function as soon as you unzip, the Makefile.inc may need to be updated with the correct paths to the support modules (these include FastJet, HepMC2, LHAPDF, and Pythia8).  I'm going to include the Makefile.inc that links to the SoftwarePack25 directory that I built within /cluster/tufts/beaucheminlab, and I believe you won't need to change anything, but I can't guarantee that at this point.  However, since the compiled executable is already included in the tarball, you should be able to run it out of the box without recompiling.

VII. CONCLUSION

This concludes the basic introduction to the W+Jets fiducial simulation.  Further information regarding the cluster can be found at https://it.tufts.edu/high-performance-computing, and additional details on matching &amp; merging can be found at https://pythia.org/latest-manual/htmldoc/MatchingAndMerging.html.  Good luck with your analysis!
