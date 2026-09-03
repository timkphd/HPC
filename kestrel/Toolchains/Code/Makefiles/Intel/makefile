SHELL:=/usr/bin/bash

recurse:
	module reset                        ; \
	module load  oneapi                 ; \
	module load intel-oneapi-mpi        ; \
	$(MAKE) -f $(firstword $(MAKEFILE_LIST)) intel gnu rmo

intel: ex_f ex_c 
gnu: gex_f gex_c 

#Intel mpi  with intel compilers
PF90=mpiifx
PCC=mpiicx
ICC=icx


#Intel mpi with gcc and gfortran
PG90=mpifc
PGCC=mpicc

OPS_I=-O3 -g -qopenmp
OPS_G=-O3 -g -fopenmp


triadi.o: triad.c
	$(ICC) $(OPS_I) $(XOPT) -c triad.c -o triadi.o

triadg.o: triad.c
	gcc $(OPS_G) -c triad.c -o triadg.o
	

ex_f: ex1.f90 triadi.o
	$(PF90) $(OPS_I)       ex1.f90  triadi.o -o ex_f

ex_c: export I_MPI_CC=$(ICC)
ex_c: ex1.c 
	$(PCC) $(OPS_I) $(XOPT)  ex1.c  triadi.o -o ex_c

gex_f: ex1.f90 triadg.o
	$(PG90) $(OPS_G)  ex1.f90 triadg.o  -o gex_f

gex_c: ex1.c triadg.o
	$(PGCC) $(OPS_G)       ex1.c  triadg.o -o gex_c


clean:rmo
	rm -rf  ex_f ex_c gex_f gex_c intel.tar exc exf

#clean up after the compile
rmo:
	rm -rf *o *mod*

tar:
	tar -cf intel.tar ex1.c ex1.f90 makefile script triad.c makecray

