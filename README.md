# Prediction of whether a metagenome is from a host or an ecological sample based on its SingleM profile

To operate these scripts, you will need to install the dependencies. Using mamba/conda:
```
$ mamba env create -p ./env -f envs/host_or_not_prediction.yml
$ conda activate ./env
```

## Inference

To apply the predictor, first need to reshape a SingleM taxonomic profile:
```
bin/generate_profiles_from_condensed --depth-index-targets 4 \
 --condensed-otu-table <singlem_taxonomic_profile>
 --output-prefix <output_prefix>
```

And gzip
```
gzip <output_prefix>4.csv
```

And then inference can be done with
```
bin/predict_host_or_not --taxonomy-json data/ncbi_taxonomy.json \
 --model models/host_or_not-4.joblib \
 --columns-file models/host_or_not_column_names4.csv \
 --acc-organism-csv <acc_organism_file> \
 --condensed-profiles <output_prefix>4.csv \
 --output <output_file>
```

For instance, using the example data
```
$ bin/generate_profiles_from_condensed \
 --depth-index-targets 4 \
 --condensed-otu-table data/example_taxonomic_profile.tsv \
 --output-prefix example-prefix-

$ bin/predict_host_or_not --taxonomy-json data/ncbi_taxonomy.json \
 --model models/host_or_not-4.joblib \
 --columns-file models/host_or_not_column_names4.csv \
 --acc-organism-csv data/example_acc_organism.csv \
 --condensed-profiles example-prefix-4.csv \
 --output /dev/stdout

acc     prediction      organism        host_or_not
ERR1914274      host
SRR9841429      host    sponge metagenome       host
```
This indicates both metagenomes were predicted as host-associated, with the second also recorded that way in the NCBI metadata, which is provided by the `--acc-organism-csv` argument.

## Training

A script for training the model, `bin/generate_predictor` is also provided.
