---
layout: default
title: Tutorial - Basic Analysis Pipeline
nav_order: 1
parent: Tutorials
usemathjax: true
---

<style type="text/css">
.center {
    display: block;
    margin-left: auto;
    margin-right: auto;
}

.step {
    background-color: #f5f6fa;
    /* color: #7253ed; */
}
</style>

<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
    TeX: { equationNumbers: { autoNumber: "AMS" } }
    });
</script>
<script type="text/javascript" async src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

# Tutorial - Basic Analysis Pipeline
{: .no_toc }

This is a step by step demonstration of Cellar's basic functionalities. A
related video tutorial can be accessed
[here](https://www.youtube.com/watch?v=J61itSMezFI).
{: .fs-6 .fw-300 }

---

We will use a lymph node dataset generated by [HuBMAP](https://portal.hubmapconsortium.org/browse/dataset/617aa0f0fc9a4ad38ecd96afad012575). This dataset can be found
on Cellar under the name *HBMP1_lymph_node_1_counts*.

<div class="code-example step" markdown="1">
- Expand the data panel by clicking `Load Data` and select
    *HBMP1_lymph_node_1_counts* from the dropdown menu. Hit `Load`.
    The dataset has now been loaded into memory.

    <img src="../../images/tut1-dataset.png" class="center"/>
</div>

We can see at the top left corner that this dataset contains 14,348 cells
and 16,075 features (genes). Next, we apply some basic filtering to remove
cells with too few expressed genes, but also remove low count genes since
these are typically non-informative. We also total count normalize the
expression matrix and log-transform it. Finally each gene is scaled to unit
variance.

<div class="code-example step" markdown="1">
- Expand the preprocessing panel by clicking `Preprocessing`. Make sure
   `Filter Cells by Genes` and `Filter Genes by Cells` are ticked. We set
   the min and max values for each to $$100$$ and $$3000$$, respectively.
   Tick `Highly Variable Genes`. This will identify highly variable genes
   and filter the rest.
   Under `Misc`, tick `Normalize Total`. `Log1p` and `Scale`. Leave their
   settings at defaults. Click `Run`.

    <img src="../../images/tut1-prep.png" class="center"/>
</div>

A notification saying "Finished preprocessing" should appear. We can now see
that the shape of the data has changed. The number of cells remained the same,
but we now only have 570 highly variable genes to work with.

Next, we reduce the data. To understand what method to use and when, please
refer to [Dimensionality Reduction](/docs/ui-components/sidebar/dimensionality-reduction).
Applying PCA, followed by UMAP for 2D embeddings has proven to be a good
combination when analyzing scRNA-seq data.

<div class="code-example step" markdown="1">
- Close the preprocessing and data panels if they are open. Under the
  dimensionality reduction panel, select PCA and UMAP and click `Run`.

    <img src="../../images/dim-reduce.png" width="400" class="center"/>
</div>

Once Cellar finishes processing, you should see a 2D representation of
your data in the form of a scatter plot.

<img src="../../images/tut1-dim-reduce.png" width="400" class="center"/>

Next, we cluster the data. We will use Leiden clustering.

<div class="code-example step" markdown="1">
- Under the clustering panel, select Leiden and click `Run`.

    <img src="../../images/clustering.png" width="400" class="center"/>
</div>

This will color each cell by the cluster it has been assigned to.

<img src="../../images/tut1-cluster.png" width="400" class="center"/>

Let us identify some differentially expressed genes for Cluster 0
(emerald green).

<div class="code-example step" markdown="1">
- Under the DE analysis panel, select `Cluster 0` under the first dropdown
  menu and leave the second menu to `rest`. Click `Find DE Genes`.

    <img src="../../images/tut1-de.png" width="400" class="center"/>
</div>

Shortly after, you should see a table of DE genes, sorted by log2fc.

<img src="../../images/tut1-de-genes.png" width="400" class="center"/>

Clicking on any gene will paste that gene to the feature visualization
input box. Let us view the expression value of the top gene in the list `SOCS3`.

<div class="code-example step" markdown="1">
- Click on the `SOCS3` gene. You should see that gene show up under feature
  visualization. Next click `Plotting` and select `Plot Expression`.

    <img src="../../images/socs3.png" width="400" class="center"/>
</div>

The scatter plot should update to show the expression levels of the gene as below.

<img src="../../images/socs3-exp.png" width="400" class="center"/>

Let's now look at a heatmap of multiple genes and their average expression
levels for each cluster.

<div class="code-example step" markdown="1">
- Under `Feature Visualization` click `DE`. All 10 genes from the visible page
  will be parsed into the input box. Under `Plotting` select `Heatmap`.
</div>

<img src="../../images/tut1-heatmap.png" width="400" class="center"/>

Next we take a look at some possible cell types by using a curated
cell type gene set that is part of Cellar.

<div class="code-example step" markdown="1">
- Under `Enrichment Analysis` click `Run`.

    <img src="../../images/tut1-enrich.png" class="center"/>
</div>

This will give us a table of possible cell type assignments for Cluster 0,
sorted by corrected p-value.

<img src="../../images/tut1-enrich-result.png" class="center"/>

Let us annotate this cluster with the label "CD8+ T Cell".

<div class="code-example step" markdown="1">
- Under the `Annotations` panel select `Cluster 0`. Type "CD8+ T Cell" in
  the input box, and click "Store".

    <img src="../../images/tut1-annotation.png" width="400" class="center"/>
</div>

The annotation has been stored in the anndata file. We were very naive
about the assignment as we only looked at the top entry returned by
the analysis. In a real scenario, domain knowledge and further exploration
of DE and marker genes must be considered.

Once we are happy with our cell type assignments, we can export the session
file for later use.

<div class="code-example step" markdown="1">
- Under the `Session` panel click "Export Session". This will generate
  an `.h5ad` file for download.

    <img src="../../images/session.png" width="400" class="center"/>
</div>