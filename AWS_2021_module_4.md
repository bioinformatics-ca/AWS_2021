---
layout: tutorial_page
permalink: /AWS_2021_module_4
title: AWS 2021
header1: Workshop Pages for Students
header2: AWS and Unix Intro - Module 4
image: /site_images/CBW_bigdata_icon.jpg
---

# Module 4: Searching and sorting files

## Setup

The first step will be to download some example data. First move into your home directory, download the example data as a zip file and then unzip the file:

```bash
cd ~
wget http://swcarpentry.github.io/shell-novice/data/data-shell.zip
unzip data-shell.zip
cd data-shell
```

## Data Exploration

We'll begin by looking at files are in Protein Data Bank format, a simple text format that specifies the type and position of each atom in the molecule.

```
$ ls molecules/
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
```

Let’s go into that directory with `cd` and run an example command `wc cubane.pdb`:

```
$ cd
$ wc cubane.pdb
  20  156 1158 cubane.pdb
```


