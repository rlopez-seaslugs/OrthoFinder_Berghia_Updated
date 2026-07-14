

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

I also removed Doryteuthis_pealeii.pep because the formatting was giving me errors and I couldn't figure out how to fix it. 

```
mv bste_1.3_protein.faa Berghia_stephanieae.pep

```

# Species tree

I used the same species tree from Lopez-Anido et al. 2025 with a few modifications:
- corrected nudibranch relationships
- made ctenophore sister to all other taxa
- removed Doryteuthis_pealeii due to issues with proteome formatting

```
(Mnemiopsis_leidyi,(Amphimedon_queenslandica,((Trichoplax_adhaerens,(Hydra_vulgaris,(Nematostella_vectensis,(Stylophora_pistillata,(Acropora_digitifera,Acropora_millepora))))),(Hofstenia_miamia,(((Saccoglossus_kowalevskii,(Patiria_miniata,(Strongylocentrotus_purpuratus,(Lytechinus_variegatus,Lytechinus_pictus)))),(Branchiostoma_lanceolatum,(Danio_rerio,(Gallus_gallus,(Homo_sapiens,(Rattus_norvegicus,Mus_musculus)))))),((Caenorhabditis_elegans,(Drosophila_melanogaster,(Daphnia_magna,(Amphibalanus_amphitrite,Odontodactylus_scyllarus,Tigriopus_californicus)))),(Adineta_vaga,(Bugula_neritina,(Magellania_venosa,Lingula_anatina),(Helobdella_robusta,(Platynereis_dumerilii,(Capitella_teleta,Owenia_fusiformis))),(((Hanleya_hanleyi,(Acanthochitona_crinita,Acanthopleura_granulata)),(Scutopus_ventrolineatus,(Gymnomenia_pellucida,Wirenia_argentea))),(Laevipilina_hyalina,((Octopus_bimaculoides,(Architeuthis_dux,Euprymna_scolopes)),(((Archivesica_marissinica,Sinonovacula_constricta,(Dreissena_rostriformis,Dreissena_polymorpha)),(Anadara_broughtonii,(Pinctada_imbricata,Pinctada_fucata),(Gigantidas_platifrons,Mytilus_coruscus),(Argopecten_purpuratus,Azumapecten_farreri,Mizuhopecten_yessoensis),(Magallana_hongkongensis,Saccostrea_glomerata,(Crassostrea_gigas,Crassostrea_virginica)))),(Gadila_tolmiei,(((Gigantopelta_aegis,Chrysomallon_squamiferum),((Lottia_gigantea,Patella_vulgata),(Haliotis_discus_hannai,Haliotis_rubra,Haliotis_rufescens))),((Tritia_obsoleta,Pomacea_canaliculata,(Neverita_didyma,Charonia_lampas,Littorina_saxatilis,(Crepidula_navicella,Crepidula_fornicata,Crepidula_fornicata_smps,Crepidula_atrasolea))),(Aplysia_californica,(Elysia_chlorotica,(Biomphalaria_glabrata,Lymnaea_stagnalis)),((Prodoris_clavigera,(Actinocyclus_verrucosus,(Ceratosoma_tenue,Chromodoris_westraliensis,Verconia_verconis))),((Scyllaea_fulva,Hancockia_uncinata),((Tritonia_festiva,Tritonicula_hamnerorum),(Unidentia,Eubranchus_rustyus,(Favorinus_auritulus,Berghia_stephanieae,Hermissenda_crassicornis)))))))))))))))))))));
```

# First try 

Then I ran this script below 


```
#!/bin/sh
#SBATCH -p compute
#SBATCH --job-name orthofinder_og
#SBATCH --error=orthofinder_no32.err
#SBATCH --output=orthofinder_no32.out
#SBATCH --nodes=1
#SBATCH --tasks-per-node=48
#SBATCH --mem=200gb
#SBATCH --time=8-08:00:00

#set enviro variable
export MV2_SMP_USE_CMA=0

#getcondaready
eval "$(conda shell.bash hook)"

#acitvate environment
conda activate of3_env


fasta_dir="/home/rlopez-anido/mendel-nas1/orthofinder/proteomes_dec2025_no32"
result_dir="/home/rlopez-anido/mendel-nas1/orthofinder/orthofinder_results_july2026"

orthofinder \
  -d "$fasta_dir" \
  -o "$result_dir" \
  -M msa \
  -t 48 \
  -s species_tree_revised.tre




```
- I realized Doryteuthis pealeii is still in the species tree... not sure if that matters... we will see


# Output 



