

# Installing OrthoFinder

Following instructions from https://github.com/OrthoFinder/OrthoFinder#installation 

```
export MV2_SMP_USE_CMA=0

conda create -n of3_env python=3.12
conda activate of3_env
conda install orthofinder
```

# Download proteomes

Downloaded proteomes from Lopez et al. 2024 

https://datadryad.org/dataset/doi:10.5061/dryad.zpc866tf1


Proteomes are all here: 

Swapped out the berghia.pep used in Lopez-Anido et al. 20224 for the new Berghia proteome from NCBI into this folder and renamed to match formatting 

```
mv bste_polished_fB_soft_editnames_transcriptscd95.fasta.transdecoder.pep Berghia_stephanieae.pep
```
