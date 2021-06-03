---
layout: tutorial_page
permalink: /aws_2021_module_4
title: AWS 2021 - Part 4
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

`wc` is the ‘word count’ command: it counts the number of lines, words, and characters in files (from left to right, in that order).

If we run the command `wc *.pdb`, the `*` in `*.pdb` matches zero or more characters, so the shell turns `*.pdb` into a list of all .pdb files in the current directory:

```bash
$ wc *.pdb
  20  156 1158 cubane.pdb
  12   84  622 ethane.pdb
   9   57  422 methane.pdb
  30  246 1828 octane.pdb
  21  165 1226 pentane.pdb
  15  111  825 propane.pdb
 107  819 6081 total
```

Note that `wc *.pdb` also shows the total number of all lines in the last line of the output.

If we run wc -l instead of just wc, the output shows only the number of lines per file:

```bash
$ wc -l *.pdb
  20 cubane.pdb
  12 ethane.pdb
   9 methane.pdb
  30 octane.pdb
  21 pentane.pdb
  15 propane.pdb
 107 total
```

Which of these files contains the fewest lines? It’s an easy question to answer when there are only six files, but what if there were 6000? Our first step toward a solution is to run the command:

```bash
$ wc -l *.pdb > lengths.txt
```

The greater than symbol, `>`, tells the shell to redirect the command’s output to a file instead of printing it to the screen. (This is why there is no screen output: everything that wc would have printed has gone into the file `lengths.txt` instead.) The shell will create the file if it doesn’t exist. If the file exists, it will be silently overwritten, which may lead to data loss and thus requires some caution. `ls lengths.txt` confirms that the file exists:

```bash
$ ls lengths.txt
lengths.txt
```



