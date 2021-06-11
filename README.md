# Intelligent Test Selection (ITS)

This tutorial provides the user a step-by-step explanation on how to use Intelligent Test Selection (ATLAS ITS) for RTL regression.  An open source lowRISC/ibex design and RISC-V DV testbench (https://github.com/lowRISC/ibex) has been chosen for this tutorial. 

## Data Source 
Opensource Design: lowRISC/ibex design and RISC-V DV testbench (https://github.com/lowRISC/ibex)

## Prerequisites
- Git access required (version 1.7.1 or newer)
To get latest version
```
module load git
```

- VCS-MX 2019.06 or newer version
To get latest version of VCS-MX (Synopsys VCS Compiler)
```
module load vcs
```

- The user is expected to get a docker enabled machine/VM with sudo access. (only if the user wishes to try tutorial in docker mode)
It should be a machine with following minimum requirements.
```
CPU:	4
Memory:	32-64GB
Storage:	~200GB
```

## ITS Installation
- ITS software is not opensource at the moment (TBD).

## Checkout Commits
### Commit List
- The user can find the list of commits in consideration from given file: rtl_commits

- This file has 72 commit hashes spanned across 39 days from oldest to newest from ibex git history (git command: git log –pretty=fuller) from which we have picked the recent ones that have RTL changes and run successfully to completion. 

- The user can find corresponding timestamp of each commit from commit_date.json file provided.

### Checkout a Commit
- The process of retrieving a commit from git repository. The user should follow following commands.
```
git reset --hard
git checkout <commit_hash>
```

## Run Ibex
This section explains how to compile ibex design and how to run regression of ibex design.

Ibex run consists of 6 make targets (gen gcc_compile iss_sim compile rtl_sim post_compare) in two major phases to run its full regression.
1.	Compilation (This will generate tests in addition to compilation)
2.	Run tests

All these commands should be executed from <work directory>/ibex/dv/uvm directory.

### Compilation 
The user is required to do the following for compilation step (Please refer to Makefile for other variables) 
- ITERATIONS - Number of iterations for each test, assign a non-zero value to override the iteration count in the test list. For fast turnaround, specify it to 1.
- ISS - Select the instruction set simulator, either spike or ovpsim (suggest using ovpsim)
- COV - Enable coverage dump, 1 enabled, 0 disabled, ,by default disabled
- WAVES - Enable waveform dumping, 1 enabled, 0 disabled, by default enabled.

```
make clean gen gcc_compile iss_sim compile ITERATIONS=1 ISS=ovpsim COV=1 WAVES=0

```

This step shall take less than 3 mins. Generated files will be located inside ‘out’ directory.
ITS will require vdb directory (test.vdb/) which is generated inside ‘out/rtl_sim’

### Run tests
In this phase, the user will run all the testcases.

```
make rtl_sim  post_compare ITERATIONS=1 COV=1 ISS=ovpsim WAVES=0

```

This step shall take ~10 mins. If ITERATION=1 is not specified. It could take more than 30 mins. There will be a separate run directory for each test under out/rtl_sim/<test_name>/. Coverage will be stored under out/rtl_sim/test.vdb when coverage is turned on. Compile-time vdb and run-time vdb could be the same or different controlled by option -cm_dir.

*Notes:* For any testbench environment that has over 10,000 tests per simulation (simv), we recommend having one vdb per test instead of having one vdb for all tests using -cm_name for merging performance reason.

In this phase a regr.log file is created inside ‘out/’ directory if this phase finishes successfully. This file is required for ITS.

## ITS Installation
- ITS software is not opensource at the moment (TBD).
- Run ITS software to generate predictions.

## Experimental Results
  
commit_hash |	suggested_count |	failures |	recall |	testcase_reduction
------------|------------|------------|------------|------------
86979e603f4482c4e95e2f8a84e3d72f1ee1b68f	| 32 |	1 |	100 |	13.51351351
6d260250198711e1b684000940451fe0e8461197 |	32 |	1 |	100 |	13.51351351
639964514c5a861b968bd8b2937c77e583e81c63 |	35 |	1 |	100 |	5.405405405
b52aacf91b4139fadd020d4a1034822982900af6 |	8 |	1 |	100 |	78.37837838
86c1775a64bd3f024a53f47a15b86a4dbc476053 |	24 |	3 |	66.66666667 |	35.13513514
3fcede8a13cb6679866ebccc9a9267235cf2efd4 |	10 |	2 |	100 |	72.97297297
b22fca7bbedb5daa808f475e435632b15c82e4b4 |	7 |	1 |	100 |	81.08108108
36eec8e41a58028887907dd82548c31db864b0d3 |	32 |	2 |	100 |	13.51351351
2a42c23eafb43943da35684ced2b94e5c40aa213 |	10 |	2	| 100	| 72.97297297
c914ec5e6acdea49fe1697dcb5db3e42d2503ab9 |	7 |	1 |	100 |	81.57894737
5d16a5b10465e4b4db4b6e3ae9e2ac29c1e6d50a | 9 |	3 |	100 |	76.31578947
24cbc32249d557fa8566fe2e6d7d11bb5f440c42 | 9 |	2 |	100 |	76.31578947
8e28ba0b9e1a1217ee56a6da82f1a72bc16ca8c6 |	33	| 1	| 100	| 13.15789474


