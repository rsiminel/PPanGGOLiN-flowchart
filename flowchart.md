flowchart TD

%% =====================
%% Styles
%% =====================
classDef cmd fill:#ffffff,stroke:#111111,stroke-width:2px,color:#111111;
classDef file fill:#eef4ff,stroke:#3366cc,stroke-width:1.5px,color:#2244aa;
classDef opt fill:#f3fff1,stroke:#4c9a2a,stroke-width:1.5px,color:#2f6f1e;

%% =====================
%% Inputs
%% =====================
fasta[(genomes.fasta.list)]:::file
anno[(genomes.gbff list)]:::file
clusters_tsv["clusters.tsv"]:::file

%% =====================
%% Annotate
%% =====================
fasta -."--fasta".-> annotate[annotate]:::cmd
anno -."--anno".-> annotate
annotate_opts["
    -p --prodigal
    --norna
    --allow-overlap
    --kingdom XXX
    --use-pseudo
"]:::opt -.-> annotate

annotate --> h5_annotate["pangenome.h5"]:::file

%% =====================
%% Cluster
%% =====================
clusters_tsv -."--clusters".-> cluster
h5_annotate -."-p".-> cluster[cluster]:::cmd
cluster_opts["
    --identity 0.8
    --coverage 0.8
    --mode 1/2/3
    --infer_singleton
    --no_defrag
"]:::opt -.-> cluster

cluster --> h5_cluster["pangenome.h5"]:::file

h5_cluster -."-p".-> wp_cluster[write_pangenome]:::cmd
wp_cluster -."--Rtab".-> rtab["gene_presence_absence.Rtab"]:::file
wp_cluster -."--csv".-> csv["matrix.csv"]:::file

%% =====================
%% Graph
%% =====================
h5_cluster -."-p".-> graf[graph]:::cmd
graph_opts["
    --remove_high_copy_number
"]:::opt -.-> graf

graf --> h5_graph["pangenome.h5"]:::file

%% =====================
%% Partition
%% =====================
h5_graph -."-p".-> partition[partition]:::cmd
partition_opts["
    --nb_of_partitions X
"]:::opt -.-> partition

partition --> h5_partition["pangenome.h5"]:::file

h5_partition -."-p".-> wp_partition[write_pangenome]:::cmd
wp_partition -."--partitions".-> partitions["partition/XXX.txt"]:::file
wp_partition -."--gexf".-> gexf["XXX.gexf"]:::file
wp_partition -."--light_gexf".-> lgexf["XXX_light.gexf"]:::file
wp_partition -."--json".-> json["XXX.json"]:::file

%% =====================
%% Module
%% =====================
module[module]:::cmd
h5_partition -."-p".-> module

module --> h5_module["pangenome.h5"]:::file

%% =====================
%% RGP
%% =====================
rgp[rgp]:::cmd
h5_partition -."-p".-> rgp

rgp_opts["
    --variable_gain
    --persistent_penalty
    --min_score
    --min_length
    --dup_margin
"]:::opt
rgp_opts -.-> rgp

rgp --> h5_rgp["pangenome.h5"]:::file

%% =====================
%% Spot
%% =====================
spot[spot]:::cmd
h5_rgp -."-p".-> spot

spot_opts["
    --set_size
    --overlapping_match
    --exact_match_size
    --spot_graph
    --graph_formats
"]:::opt
spot_opts -.-> spot

spot --> h5_spot["pangenome.h5"]:::file

%% =====================
%% Metrics
%% =====================
h5_partition -."-p".-> metrics[metrics]:::cmd
metrics -."--genome_fluidity".-> h5_metrics["pangenome.h5"]:::file

%% =====================
%% Draw
%% =====================
h5_partition -."-p".-> draw[draw]:::cmd

draw_opts["
    --nocloud
    --add_dendrogram
    --add_metadata
"]:::opt -.-> draw

draw -."--ucurve".-> uplot["uplot.html"]:::file
draw -."--tile_plot".-> tile["tile_plot.html"]:::file

%% =====================
%% Rarefaction
%% =====================
h5_partition -."-p".-> rarefaction[rarefaction]:::cmd

rarefaction_opts["
    --depth 30
    --min 1
    --max 100
"]:::opt -.-> rarefaction

rarefaction --> rare["rarefaction.html"]:::file

%% =====================
%% info
%% =====================
info[info]:::cmd

info_opts["
    --status
    --parameters
    --content
    --metadata
"]:::opt
info_opts -.-> info

%% =====================
%% Subgraphs
%% =====================
%% subgraph all
%%     annotate
%%     h5_annotate
%%     cluster_opts
%%     cluster
%%     h5_cluster
%%     graph_opts
%%     graf
%%     h5_graph
%% end
