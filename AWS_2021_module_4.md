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

### Pipe Construction

For the file `animals.txt` from the previous exercise, consider the following command:

```
$ cut -d , -f 2 animals.txt
```

The `cut` command is used to remove or ‘cut out’ certain sections of each line in the file, and `cut` expects the lines to be separated into columns by a Tab character. A character used in this way is a called a delimiter. In the example above we use the `-d` option to specify the comma as our delimiter character. We have also used the `-f` option to specify that we want to extract the second field (column). This gives the following output:

```
deer
rabbit
raccoon
rabbit
deer
fox
rabbit
bear
```

The `uniq` command filters out adjacent matching lines in a file. How could you extend this pipeline (using `uniq` and another command) to find out what animals the file contains (without any duplicates in their names)?

<details>
    <summary>
    **Solution** (click here)
    </summary>

`$ cut -d , -f 2 animals.txt | sort | uniq`

</details>

### Which Pipe?

The file `animals.txt` contains 8 lines of data formatted as follows:

```
2012-11-05,deer
2012-11-05,rabbit
2012-11-05,raccoon
2012-11-06,rabbit
...
```

The `uniq` command has a `-c` option which gives a count of the number of times a line occurs in its input. Assuming your current directory is `data-shell/data/`, what command would you use to produce a table that shows the total count of each type of animal in the file?

1. `sort animals.txt | uniq -c`
2. `sort -t, -k2,2 animals.txt | uniq -c`
3. `cut -d, -f 2 animals.txt | uniq -c`
4. `cut -d, -f 2 animals.txt | sort | uniq -c`
5. `cut -d, -f 2 animals.txt | sort | uniq -c | wc -l`


<details>
    <summary>
    **Solution** (click here)
    </summary>

Option 4. is the correct answer. If you have difficulty understanding why, try running the commands, or sub-sections of the pipelines (make sure you are in the `data-shell/data` directory).

</details>

## Checking Files

Let's say our analysis has created 17 files in the `north-pacific-gyre/2012-07-03` directory described earlier. As a quick check, starting from the `data-shell` directory, if we type:

```
$ cd north-pacific-gyre/2012-07-03
$ wc -l *.txt
```

The output is 18 lines that look like this:

```
300 NENE01729A.txt
300 NENE01729B.txt
300 NENE01736A.txt
300 NENE01751A.txt
300 NENE01751B.txt
300 NENE01812A.txt
...
```

Now if we run

```
$ wc -l *.txt | sort -n | head -n 5
 240 NENE02018B.txt
 300 NENE01729A.txt
 300 NENE01729B.txt
 300 NENE01736A.txt
 300 NENE01751A.txt
```

Whoops: one of the files is 60 lines shorter than the others. When we goes back and checks it, we sees that assay at 8:00 on a Monday morning — someone was probably in using the machine on the weekend, and forgot to reset it. Before re-running that sample, lets checks to see if any files have too much data:

```
$ wc -l *.txt | sort -n | tail -n 5
```

```
 300 NENE02040B.txt
 300 NENE02040Z.txt
 300 NENE02043A.txt
 300 NENE02043B.txt
5040 total
```

Those numbers look good — but what’s that ‘Z’ doing there in the third-to-last line? All of her samples should be marked ‘A’ or ‘B’; by convention, her lab uses ‘Z’ to indicate samples with missing information. To find others like it, she does this:

```
$ ls *Z.txt
NENE01971Z.txt    NENE02040Z.txt
```

It turns outt hat there’s no depth recorded for either of those samples. Since it’s too late to get the information any other way, we must exclude those two files from our analysis. We could delete them using rm, but there are actually some analyses we might do later where depth doesn’t matter, so instead, we'll have to be careful later on to select files using the wildcard expression *[AB].txt. As always, the * matches any number of characters; the expression [AB] matches either an ‘A’ or a ‘B’, so this matches all the valid data files she has.

### Wildcard Expressions

Wildcard expressions can be very complex, but you can sometimes write them in ways that only use simple syntax, at the expense of being a bit more verbose. Consider the directory `data-shell/north-pacific-gyre/2012-07-03` : the wildcard expression *[AB].txt matches all files ending in `A.txt` or `B.txt`. Imagine you forgot about this.

Can you match the same set of files with basic wildcard expressions that do not use the [] syntax? Hint: You may need more than one command, or two arguments to the ls command.

If you used two commands, the files in your output will match the same set of files in this example. What is the small difference between the outputs?

If you used two commands, under what circumstances would your new expression produce an error message where the original one would not?

<details>
    <summary>
    **Solution** (click here)
    </summary>

1:

A solution using two wildcard commands:

`$ ls *A.txt` and then `$ ls *B.txt`

A solution using one command but with two arguments:

`$ ls *A.txt *B.txt`

2: The output from the two new commands is separated because there are two commands.
3: When there are no files ending in `A.txt`, or there are no files ending in `B.txt`, then one of the two commands will fail.
</details>


## Key Points

- `cat` displays the contents of its inputs.
- `head` displays the first 10 lines of its input.
- `tail` displays the last 10 lines of its input.
- `sort` sorts its inputs.
- `wc` counts lines, words, and characters in its inputs.
- `command > [file]` redirects a command’s output to a file (overwriting any existing content).
- `command >> [file]` appends a command’s output to a file.
- `[first] | [second]` is a pipeline: the output of the first command is used as the input to the second.
- The best way to use the shell is to use pipes to combine simple single-purpose programs (filters).









