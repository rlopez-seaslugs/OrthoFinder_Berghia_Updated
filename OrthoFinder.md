# Installing OrthoFinder

```
conda create -n orthofinder
conda activate orthofinder
conda install orthofinder -c bioconda
```

# Download proteomes

Downloaded proteomes from Lopez et al. 2024 

https://datadryad.org/dataset/doi:10.5061/dryad.zpc866tf1


Proteomes are all here: 

Swapped out the berghia.pep used in Lopez-Anido et al. 20224 for the new Berghia proteome into this folder and renamed to match formatting 

```
mv bste_polished_fB_soft_editnames_transcriptscd95.fasta.transdecoder.pep Berghia_stephanieae.pep
```

# Species tree 



# Running Orthofinder 


I ran this: 

```
#!/bin/sh
#SBATCH -p compute
#SBATCH --job-name orthofinder_og
#SBATCH --error=orthofinder.err
#SBATCH --output=orthofinder.out
#SBATCH --nodes=1
#SBATCH --tasks-per-node=48
#SBATCH --mem=200gb
#SBATCH --time=8-08:00:00

#getcondaready
eval "$(conda shell.bash hook)"

#acitvate environment
conda activate orthofinder


fasta_dir="/home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025"
result_dir="/home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_dec2025_1"




# 1. submit with -og (Stop after inferring orthogroups)
orthofinder \
  -f "$fasta_dir" \
  -o "$result_dir" \
  -M msa \
  -t 48 \
  -og
```


& Recieved this error:

```

2025-12-18 09:33:13 : Starting OrthoFinder v3.1.0
48 thread(s) for highly parallel tasks (BLAST searches etc.)
6 thread(s) for OrthoFinder algorithm

OrthoFinder version 3.1.0 Copyright (C) 2014 David Emms

Results directory:
    /home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_dec2025_1/Res
ults_Dec18/

Checking required programs are installed
========================================
Test can run "mcl" - ok
Test can run "famsa" - ok
Test can run "fasttree" - ok

Dividing up work for BLAST for parallel processing
--------------------------------------------------

ERROR: external program called by OrthoFinder returned an error code: 1

Command: diamond makedb --threads 1 --ignore-warnings --in 
/home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_dec2025_1/Results
_Dec18/WorkingDirectory/Species32.fa -d 
/home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_dec2025_1/Results
_Dec18/WorkingDirectory/diamondDBSpecies32

stdout:
b''
stderr:
b'diamond v2.1.16.170 (C) Max Planck Society for the Advancement of Science, 
Benjamin J. Buchfink, University of Tuebingen\nDocumentation, support and 
updates available at http://www.diamondsearch.org\nPlease cite: 
http://dx.doi.org/10.1038/s41592-021-01101-x Nature Methods (2021)\n\n#CPU 
threads: 1\nScoring parameters: (Matrix=BLOSUM62 Lambda=0.267 K=0.041 
Penalties=11/1)\nDatabase input file: 
/home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_dec2025_1/Results
_Dec18/WorkingDirectory/Species32.fa\nOpening the database file...  
[0.002s]\nLoading sequences... Error: Missing fields in input line\n'
ERROR: diamond makedb failed
ERROR: An error occurred, ***please review the error messages*** they may 
contain useful information about the problem.

OrthoFinder finished in 51.742165s

```

- species32 is Daphnia_magna.pep so I am going to try running it without that proteome 
```
(base) [rlopez-anido@mendel-head orthofinder]$ ls -1 /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/*.pep | sort | nl | grep " 32"
    32	/home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Daphnia_magna.pep
```

## Checking formatting of other protemoes

```
for f in /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/*.pep; do
>     # skips if file doesn't exist (in case of missing extensions)
>     [ ! -f "$f" ] && continue
> 
>     issues=0
> 
>     # is file is empty?
>     if [ ! -s "$f" ]; then
>         echo "EMPTY: $f"
>         issues=1
>     fi
> 
>     # check for Windows line endings
>     if grep -q $'\r' "$f"; then
>         echo "DOS line endings: $f"
>         issues=1
>     fi
> 
>     # check for blank lines
>     if grep -q '^$' "$f"; then
>         echo "BLANK lines found: $f"
>         issues=1
>     fi
>
> 
>     # If no issues, echo as OK
>     [ $issues -eq 0 ] && echo "OK: $f"
```


```
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Acanthochitona_crinita.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Acanthopleura_granulata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Acropora_digitifera.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Acropora_millepora.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Actinocyclus_verrucosus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Adineta_vaga.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Amphibalanus_amphitrite.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Amphimedon_queenslandica.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Anadara_broughtonii.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Aplysia_californica.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Architeuthis_dux.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Archivesica_marissinica.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Argopecten_purpuratus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Azumapecten_farreri.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Berghia_stephanieae.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Biomphalaria_glabrata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Branchiostoma_lanceolatum.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Bugula_neritina.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Caenorhabditis_elegans.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Capitella_teleta.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Ceratosoma_tenue.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Charonia_lampas.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Chromodoris_westraliensis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Chrysomallon_squamiferum.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crassostrea_gigas.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crassostrea_virginica.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crepidula_atrasolea.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crepidula_fornicata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crepidula_fornicata_smps.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Crepidula_navicella.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Danio_rerio.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Doryteuthis_pealeii.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Dreissena_polymorpha.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Dreissena_rostriformis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Drosophila_melanogaster.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Elysia_chlorotica.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Eubranchus_rustyus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Euprymna_scolopes.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Favorinus_auritulus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Gadila_tolmiei.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Gallus_gallus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Gigantidas_platifrons.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Gigantopelta_aegis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Gymnomenia_pellucida.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Haliotis_discus_hannai.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Haliotis_rubra.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Haliotis_rufescens.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Hancockia_uncinata.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Hanleya_hanleyi.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Helobdella_robusta.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Hermissenda_crassicornis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Hofstenia_miamia.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Homo_sapiens.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Hydra_vulgaris.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Laevipilina_hyalina.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Lingula_anatina.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Littorina_saxatilis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Lottia_gigantea.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Lymnaea_stagnalis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Lytechinus_pictus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Lytechinus_variegatus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Magallana_hongkongensis.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Magellania_venosa.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Mizuhopecten_yessoensis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Mnemiopsis_leidyi.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Mus_musculus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Mytilus_coruscus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Nematostella_vectensis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Neverita_didyma.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Octopus_bimaculoides.pep
DOS line endings: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Odontodactylus_scyllarus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Owenia_fusiformis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Patella_vulgata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Patiria_miniata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Pinctada_fucata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Pinctada_imbricata.pep
BLANK lines found: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Platynereis_dumerilii.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Pomacea_canaliculata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Prodoris_clavigera.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Rattus_norvegicus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Saccoglossus_kowalevskii.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Saccostrea_glomerata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Scutopus_ventrolineatus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Scyllaea_fulva.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Sinonovacula_constricta.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Strongylocentrotus_purpuratus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Stylophora_pistillata.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Tigriopus_californicus.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Trichoplax_adhaerens.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Tritia_obsoleta.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Tritonia_festiva.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Tritonicula_hamnerorum.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Unidentia.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Verconia_verconis.pep
OK: /home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025/Wirenia_argentea.pep
```

