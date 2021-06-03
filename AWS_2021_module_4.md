---
layout: tutorial_page
permalink: /aws_2021_module_4
title: AWS 2021 - Part 4
header1: Workshop Pages for Students
header2: AWS and Unix Intro - Module 4
image: /site_images/CBW_bigdata_icon.jpg
---

# Module 4: Searching and sorting files

## Preamble

The exercies here are taken from the [Software Carpentry Unix shell lesson](http://swcarpentry.github.io/shell-novice). Licensed under CC-BY 4.0 2018–2021 by [The Carpentries](https://carpentries.org).

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

We can now send the content of `lengths.txt` to the screen using `cat lengths.txt`. The cat command gets its name from ‘concatenate’ i.e. join together, and it prints the contents of files one after another. There’s only one file in this case, so cat just shows us what it contains:

```bash
$ cat lengths.txt
  20 cubane.pdb
  12 ethane.pdb
   9 methane.pdb
  30 octane.pdb
  21 pentane.pdb
  15 propane.pdb
 107 total
```

## Sorting

If we run `sort` on a file containing the following lines:

```
10
2
19
22
6
```

the output is:

```
10
19
2
22
6
```

If we run `sort -n` on the same input, we get this instead:

```
2
6
10
19
22
```
Explain why `-n` has this effect.

<details>
    <summary>
    **Solution** (click here)
    </summary>
The -n option specifies a numerical rather than an alphanumerical sort.
</details>

We will also use the `-n` option to specify that the sort is numerical instead of alphanumerical. This does not change the file; instead, it sends the sorted result to the screen:

```bash
$ sort -n lengths.txt
  9  methane.pdb
 12  ethane.pdb
 15  propane.pdb
 20  cubane.pdb
 21  pentane.pdb
 30  octane.pdb
107  total
```

We can put the sorted list of lines in another temporary file called `sorted-lengths.txt` by putting `> sorted-lengths.txt` after the command, just as we used `> lengths.txt` to put the output of `wc` into `lengths.txt`. Once we’ve done that, we can run another command called `head` to get the first few lines in sorted-lengths.txt:

```bash
$ sort -n lengths.txt > sorted-lengths.txt
$ head -n 1 sorted-lengths.txt
  9  methane.pdb
```

Using `-n 1` with head tells it that we only want the first line of the file; `-n 20` would get the first 20, and so on. Since `sorted-lengths.txt` contains the lengths of our files ordered from least to greatest, the output of head must be the file with the fewest lines.

### What Does >> Mean?

We have seen the use of `>`, but there is a similar operator `>>` which works slightly differently. We’ll learn about the differences between these two operators by printing some strings. We can use the echo command to print strings e.g.

```
$ echo The echo command prints text
The echo command prints text
```

Now test the commands below to reveal the difference between the two operators:

```
$ echo hello > testfile01.txt
```

and:

```
$ echo hello >> testfile01.txt
```

### Running Commands Together

If you think this is confusing, you’re in good company: even once you understand what `wc`, `sort`, and `head` do, all those intermediate files make it hard to follow what’s going on. We can make it easier to understand by running sort and head together:

```
$ sort -n lengths.txt | head -n 1
  9  methane.pdb
```

The vertical bar, `|`, between the two commands is called a pipe. It tells the shell that we want to use the output of the command on the left as the input to the command on the right.

Nothing prevents us from chaining pipes consecutively. That is, we can for example send the output of `wc` directly to sort, and then the resulting output to head. Thus we first use a pipe to send the output of `wc` to `sort`:

```
$ wc -l *.pdb | sort -n
   9 methane.pdb
  12 ethane.pdb
  15 propane.pdb
  20 cubane.pdb
  21 pentane.pdb
  30 octane.pdb
 107 total
```

And now we send the output of this pipe, through another pipe, to head, so that the full pipeline becomes:

```
$ wc -l *.pdb | sort -n | head -n 1
   9  methane.pdb
```

The redirection and pipes used in the last few commands are illustrated below:

![redirects_and_pipes](http://swcarpentry.github.io/shell-novice/fig/redirects-and-pipes.svg)

### Piping Commands Together

In our current directory, we want to find the 3 files which have the least number of lines. Which command listed below would work?

```
$ wc -l * > sort -n > head -n 3
$ wc -l * | sort -n | head -n 1-3
$ wc -l * | head -n 3 | sort -n
$ wc -l * | sort -n | head -n 3
```

This idea of linking programs together is why Unix has been so successful. Instead of creating enormous programs that try to do many different things, Unix programmers focus on creating lots of simple tools that each do one job well, and that work well with each other. This programming model is called ‘pipes and filters’. We’ve already seen pipes; a filter is a program like `wc` or `sort` that transforms a stream of input into a stream of output. Almost all of the standard Unix tools can work this way: unless told to do otherwise, they read from standard input, do something with what they’ve read, and write to standard output.

The key is that any program that reads lines of text from standard input and writes lines of text to standard output can be combined with every other program that behaves this way as well. You can and should write your programs this way so that you and other people can put those programs into pipes to multiply their power.

### Pipe Reading Comprehension

A file called `animals.txt` (in the `data-shell/data` folder) contains the following data:

```
2012-11-05,deer
2012-11-05,rabbit
2012-11-05,raccoon
2012-11-06,rabbit
2012-11-06,deer
2012-11-06,fox
2012-11-07,rabbit
2012-11-07,bear
```

What text passes through each of the pipes and the final redirect in the pipeline below?

```
$ cat animals.txt | head -n 5 | tail -n 3 | sort -r > final.txt
```

<details>
    <summary>
    **Solution** (click here)
    </summary>

<p>
The head command extracts the first 5 lines from `animals.txt`. Then, the last 3 lines are extracted from the previous 5 by using the tail command. With the `sort -r` command those 3 lines are sorted in reverse order and finally, the output is redirected to a file `final.txt`. The content of this file can be checked by executing `cat final.txt`. The file should contain the following lines:

```
2012-11-06,rabbit
2012-11-06,deer
2012-11-05,raccoon
```
</p>

</details>

