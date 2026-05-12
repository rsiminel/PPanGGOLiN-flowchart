flowchart TD

    %% =====================
    %% Styles
    %% =====================
    classDef cmd fill:#ffffff,stroke:#111111,stroke-width:2px,color:#111111;
    classDef file fill:#eef4ff,stroke:#3366cc,stroke-width:1.5px,color:#2244aa;
    classDef opt fill:#f3fff1,stroke:#4c9a2a,stroke-width:1.5px,color:#2f6f1e;
    classDef workflow fill:#fff3d6,stroke:#c89a00,stroke-width:2px,color:#7a5a00;
    classDef rgpfill fill:#eaf9ea,stroke:#4f9d69,stroke-width:2px,color:#2f6f1e;
    classDef modulefill fill:#fdebf2,stroke:#c94f7c,stroke-width:2px,color:#7d2146;

    %% =====================
    %% Inputs
    %% =====================
    fasta[(genomes.fasta.list)]:::file
    anno[(genomes.gbff list)]:::file

    %% =====================
    %% Annotate
    %% =====================
    annotate[annotate]:::cmd

    fasta -."--fasta".-> annotate
    anno -."--anno".-> annotate

    annotate_opts["
        --prodigal
        --norna
        --allow-overlap
        --kingdom XXX
        --use-fasta
    "]:::opt
    annotate_opts --> annotate

    annotate --> h5_annotate["pangenome.h5"]:::file

    %% =====================
    %% Cluster
    %% =====================
    cluster[cluster]:::cmd
    h5_annotate -."-p".-> cluster

    clusters_tsv["clusters.tsv"]:::file
    clusters_tsv -."--clusters".-> cluster

    cluster_opts["
        --identity 0.8
        --coverage 0.8
        --mode 1/2/3
        --infer_singleton
        --no_defrag
    "]:::opt
    cluster_opts -.-> cluster

    cluster --> h5_cluster["pangenome.h5"]:::file

    %% =====================
    %% Graph
    %% =====================
    graf[graph]:::cmd
    h5_cluster -."-p".-> graf

    graph_opts["
        --remove_high_copy_number
    "]:::opt
    graph_opts -.-> graf

    graf --> h5_graph["pangenome.h5"]:::file

    %% =====================
    %% Partition
    %% =====================
    partition[partition]:::cmd
    h5_graph -."-p".-> partition

    partition_opts["
        --nb_of_partitions X
    "]:::opt
    partition_opts -.-> partition

    partition --> h5_partition["pangenome.h5"]:::file

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

    spot_opts["--set_size
        --overlapping_match
        --exact_match_size
        --spot_graph
        --graph_formats
    "]:::opt
    spot_opts -.-> spot

    spot --> h5_spot["pangenome.h5"]:::file

    %% =====================
    %% Draw
    %% =====================
    draw[draw]:::cmd
    h5_partition -."-p".-> draw

    draw_opts["--ucurve
        --tile_plot
        --nocloud
        --add_dendrogram
        --add_metadata
        --draw_spots
    "]:::opt
    draw_opts -.-> draw

    %% =====================
    %% rarefaction
    %% =====================
    rarefaction[rarefaction]:::cmd

    rarefaction_opts["
        --depth 30
        --min 1
        --max 100
    "]:::opt
    rarefaction_opts -.-> rarefaction

    %% =====================
    %% metrics
    %% =====================
    metrics[metrics]:::cmd

    metrics_opts["--genome_fluidity"]:::opt
    metrics_opts -.-> metrics

    metrics --> h5

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
    %% write_pangenome
    %% =====================
    writep[write_pangenome]:::cmd

    write_opts["
        --stats
        --gexf
        --light_gexf
        --json
        --regions
        --spots
        --borders
        --spot_modules
    "]:::opt
    write_opts -.-> writep

    %% =====================
    %% Main outputs
    %% =====================
    genome_stats["genome_statistics.tsv"]:::file
    mean_dup["mean_persistent_duplication.tsv"]:::file
    rtab["gene_presence_absence.Rtab"]:::file

    gexf["XXX.gexf"]:::file
    lightgexf["XXX_light.gexf"]:::file
    json["XXX.json"]:::file

    regions["regions_of_genomic_plasticity.tsv"]:::file
    spots["spots.tsv"]:::file
    sumspots["summarize_spots.tsv"]:::file
    borders["borders.tsv"]:::file
    spotmods["modules_spots.tsv
    modules_RGP_lists.tsv"]:::file

    writep --> genome_stats
    writep --> mean_dup
    writep --> rtab

    writep --> gexf
    writep --> lightgexf
    writep --> json

    writep --> regions
    writep --> spots
    writep --> sumspots
    writep --> borders
    writep --> spotmods
