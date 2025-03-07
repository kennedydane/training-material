---
layout: tutorial_hands_on

title: Analysis of molecular dynamics simulations
zenodo_link: 'https://zenodo.org/record/2537734'
level: Intermediate
questions:
- Which analysis tools are available?
objectives:
- Learn which analysis tools are available.
- Analyse a protein and discuss the meaning behind each analysis. 
requirements:
  -
    type: "internal"
    topic_name: computational-chemistry
    tutorials:
      - setting-up-molecular-systems
      - md-simulation-namd

time_estimation: 1H
key_points:
- Multiple analyses including timeseries, RMSD, PCA are available
- Analysis tools allow a further chemical understanding of the system
follow_up_training:
  -
    type: "internal"
    topic_name: computational-chemistry
    tutorials:
      - analysis-md-simulations
contributors:
  - chrisbarnettster
  - tsenapathi
  - simonbray

---


# Introduction
{:.no_toc}

Molecular dynamics simulations return highly complex data. The Cartesian positions of each atom of the system (thousands or even millions) are recorded at every time step of the trajectory; this may again be thousands to millions of steps in length. Therefore, some kind of further analysis is needed to extract useful information from the data.

In this tutorial, we illustrate some of the analytical tools able to investigate conformational changes by analysis of a typical short protein simulation, such as for CBH1.

There are other analysis tools available; you are encouraged to try these out too. 

> ### Agenda
>
> In this tutorial, we will cover:
>
> 1. TOC
> {:toc}
>
{: .agenda}

# Get data

The data required can be generated by completing the [NAMD simulation tutorial]({{ site.baseurl }}{% link topics/computational-chemistry/tutorials/md-simulation-namd/tutorial.md %}).  Access it from your history. Alternatively, download the data from the Zenodo link provided.

> ### {% icon hands_on %} Hands-on: Upload cellulose simulation trajectory
>
> 1. Create a new history
> 2. Import the files from [Zenodo](https://zenodo.org/record/2537734), or from your history, if you completed the previous [NAMD simulation tutorial]({{ site.baseurl }}{% link topics/computational-chemistry/tutorials/md-simulation-namd/tutorial.md %}):
>
>    ```
>    https://zenodo.org/record/2537734/files/cbh1test.dcd
>    ```
>
>    {% include snippets/import_via_link.md %}
>
> 3. Rename the file 'CBH1 trajectory'
>
>    {% include snippets/rename_dataset.md %}
>
{: .hands_on}


# Analysis with BIO3D

We'll carry out some basic analysis by calculating RMSD, RMSF and PCA. The tools use the [Bio3D package](http://thegrantlab.org/bio3d/index.php), developed by the [Grant lab](http://thegrantlab.org/).


## RMSD

RMSD, or root-mean-square deviation, is a standard measure of structural distance between coordinates. It measures the average distance between a group of atoms (e.g. backbone atoms of a protein). If we calculate RMSD between two sets of atomic coordinates - for example, two time points from the trajectory - the value is a measure of how much the protein conformation has changed. [Wikipedia](https://en.wikipedia.org/wiki/Root-mean-square_deviation_of_atomic_positions) provides more information.

> ### {% icon hands_on %} Hands-on: Calculate RMSD
>
> **RMSD Analysis** {% icon tool %} with the following parameters:
>    - {% icon param-file %} *"dcd trajectory input"*: Trajectory file
>    - {% icon param-file %} *"pdb input"*: Structure file
>    - *"Select domains"*: `Calpha` (calculate RMSD only for the C-alpha domain of the protein)
>
{: .hands_on}

![Snapshot of RMSD plot]({{ site.baseurl }}{% link topics/computational-chemistry/images/RMSD_plot_33.png %} "RMSD plot for a short CBH1 simulation")
![Snapshot of RMSD histogram]({{ site.baseurl }}{% link topics/computational-chemistry/images/RMSD_Histogram_Plot_34.png %} "RMSD histogram for a short CBH1 simulation")

> > ### {% icon question %} Question
> >
> > What do the features in the RMSD plot tell us?
> >
> > > ### {% icon solution %} Solution
> > > The increase in the RMSD plot with time shows the protein steadily deviates from its original conformation.
> > >
> > > The three peaks visible in the histogram suggests the presence of three main conformations which are accessed during the trajectory.
> > {: .solution}
> {: .question}


## RMSF

The root-mean-square fluctuation (RMSF) measures the average deviation of a particle (e.g. a protein residue) over time from a reference position (typically the time-averaged position of the particle). Thus, RMSF analyzes the portions of structure that are fluctuating from their mean structure the most (or least). 

> ### {% icon hands_on %} Hands-on: Calculate RMSF
>
> 1. **RMSF Analysis** {% icon tool %} with the following parameters:
>    - {% icon param-file %} *"dcd trajectory input"*: Trajectory file
>    - {% icon param-file %} *"pdb input"*: Structure file
>    - *"Select domains"*: `Calpha` (calculate RMSF only for the C-alpha domain of the protein)
>
{: .hands_on}

![Snapshot of RMSF plot]({{ site.baseurl }}{% link topics/computational-chemistry/images/rmsf_plot_23.png %} "RMSF plot for a short CBH1 simulation")

> ### {% icon question %} Question
>
> What can we learn from the features in the RMSF plot?
>
> > ### {% icon solution %} Solution
> > Higher RMSF values most likely are loop regions with more conformational flexibility, where the structure is not as well defined.
> >
> > This allows a link with experimental spectroscopic techniques which detect the secondary structure of a protein.
> {: .solution}
{: .question}


## PCA

Principal component analysis (PCA) converts a set of correlated observations (movement of all atoms in protein) to a set of principal components which are linearly independent (or uncorrelated). Mathematically, it is a transformation of the data to a new coordinate system, in which the first coordinate represents the greatest variance, the second coordinate represents the second most variance, and so on.

You can read more about PCA on [Wikipedia](https://en.wikipedia.org/wiki/Principal_component_analysis). In a nutshell, PCA takes a complex dataset with many variables and tries to distill the variables down to a few 'principal components' which still preserve most of the differences between the data.

In summary:
- The **PCA** {% icon tool %} tool will calculate and return a PCA to determine the relationship between statistically meaningful conformations (major global motions) sampled during the trajectory. THe tool returns several images of the PCA and the raw data in tab-separated format.
- The **PCA visualization** {% icon tool %} tool will carry out PCA and return a trajectory of the selected principle component. This trajectory is useful for visualisation and further investigating the interesting modes and changes that occur within a selected principle component. 


> ### {% icon hands_on %} Hands-on: Calculate PCA
>
> 1. **PCA** {% icon tool %} with the following parameters:
>    - {% icon param-file %} *"dcd trajectory input"*: Trajectory file
>    - {% icon param-file %} *"pdb input"*: Structure file
>    - *"Use singular value decomposition (SVD) instead of default eigenvalue decomposition ?"*: `No`
>    - *"Select domains"*: `Calpha`
>
> 1. **PCA visualization** {% icon tool %} with the following parameters:
>    - {% icon param-file %} *"dcd trajectory input"*: Trajectory file
>    - {% icon param-file %} *"pdb input"*: Structure file
>    - *"Use singular value decomposition (SVD) instead of default eigenvalue decomposition ?"*: `No`
>    - *"Select domains"*: `Calpha`
>    - *"Principal component id"*: `Calpha`
>
> PCA visualisation: This tool can generate small trajectories of the first three principal components. The .pdb of the .nc files can be visualized using a visualization software such as VMD.
>
{: .hands_on}

![Snapshot of PCA plot]({{ site.baseurl }}{% link topics/computational-chemistry/images/PCA_plot_46.png %} "PCA plot for a short CBH1 simulation")

> > ### {% icon question %} Question
> >
> > What do the features in the RMSD plot tell us? Do the principal coordinates have a meaning?
> >
> > > ### {% icon solution %} Solution
> > > Here, PCA shows the statistically meaningful conformations in the CBH1 trajectory. The principal motions within the trajectory and the vital motions needed for conformational changes can be identified. Two distinct groupings along the PC1 plane, indicating a non-periodic conformational change, are identified. The groupings along the PC2 and PC3 planes do not completely cluster separately, implying that these global motions are periodic. The PC1 is linked to an active site motion that limits the motion to a key glycosidic bond.
> > {: .solution}
> {: .question}

## Workflow vs. individual tools
You can choose to use the tools one by one as described above, or alternatively combine into a single analysis using the workflow provided.
![Snapshot of conformational analysis workflow]({{ site.baseurl }}{% link topics/computational-chemistry/images/workflow_confanalysis.png %} "A simple analysis workflow")

> ### {% icon hands_on %} Upload a workflow
>
> 1. Click on 'Workflow' in the toolbar at the top of the main Galaxy page. In the upper right corner of the central pane, click the 'Upload or import workflow' icon.
>
> 2. Enter the 'Archived workflow URL' and click 'Import workflow'.
>    ```
>    https://raw.githubusercontent.com/galaxyproject/training-material/master/topics/computational-chemistry/tutorials/analysis-md-simulations/workflows/main_workflow.ga
>    ```
>
{: .hands_on}


# Further analysis

Further analyses are available; try out the [MDAnalysis workflow](https://github.com/galaxyproject/training-material/tree/master/topics/computational-chemistry/tutorials/analysis-md-simulations/workflows/advanced_workflow.ga), which includes a Ramachandran plot and various timeseries.

![MDAnalysis workflow]({{ site.baseurl }}{% link topics/computational-chemistry/images/workflow_mdanalysis.png %} "MD analysis workflow")

# Conclusion
{:.no_toc}

