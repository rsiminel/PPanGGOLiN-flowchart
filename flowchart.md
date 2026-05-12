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
    fasta["genomes.fasta.list"]:::file
    anno["genomes.gff / .gbff list"]:::file
    h5[(pangenome.h5)]:::file

    %% =====================
    %% High-level workflows
    %% =====================
    workflow[workflow]:::workflow
    panrgp[panrgp]:::rgpfill
    allcmd[all]:::workflow

    fasta --> workflow
    anno --> workflow

    fasta --> panrgp
    anno --> panrgp

    fasta --> allcmd
    anno --> allcmd

    %% =====================
    %% annotate
    %% =====================
    annotate[annotate]:::cmd

    fasta --> annotate
    anno --> annotate

    annotate_opts["--prodigal<br/>--norna<br/>--allow-overlap<br/>--kingdom XXX<br/>--use-fasta"]:::opt
    annotate_opts -.-> annotate

    annotate --> h5

    %% =====================
    %% cluster
    %% =====================
    cluster[cluster]:::cmd
    h5 --> cluster

    cluster_opts["--identity 0.8<br/>--coverage 0.8<br/>--mode 1/2/3<br/>--infer_singleton<br/>--no_defrag"]:::opt
    cluster_opts -.-> cluster

    clusters_tsv["clusters.tsv<br/>(--clusters)"]:::file
    clusters_tsv -.-> cluster

    cluster --> h5

    %% =====================
    %% graph
    %% =====================
    graph[graph]:::cmd
    h5 --> graph

    graph_opts["--remove_high_copy_number"]:::opt
    graph_opts -.-> graph

    graph --> h5

    %% =====================
    writep --> spotmods
