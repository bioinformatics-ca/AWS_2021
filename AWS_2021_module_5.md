---
layout: aws_tutorial_page
permalink: /AWS_2021/module_5
title: AWS 2021 - Part 5
header1: Workshop Pages for Students
header2: AWS and Unix Intro - Module 5
image: /site_images/CBW_bigdata_icon.jpg
home: /AWS_v2_2021.html
---

# Module 5: Shell Scripts

## Preamble

The exercies here are taken from the [Software Carpentry Unix shell lesson](http://swcarpentry.github.io/shell-novice). Licensed under CC-BY 4.0 2018–2021 by [The Carpentries](https://carpentries.org).

We are finally ready to see what makes the shell such a powerful programming environment. We are going to take the commands we repeat frequently and save them in files so that we can re-run all those operations again later by typing a single command. For historical reasons, a bunch of commands saved in a file is usually called a shell script, but make no mistake: these are actually small programs.

Not only will writing shell scripts make your work faster– you won’t have to retype the same commands over and over again– it will also make it more accurate (fewer chances for typos) and more reproducible. If you come back to your work later (or if someone else finds your work and wants to build on it) you will be able to reproduce the same results simply by running your script, rather than having to remember or retype a long list of commands.

Let’s start by going back to `data/genomes` and creating a new file, `get_tags.sh` which will become our shell script:

```bash
$ cd data/genomes
$ nano count_tags.sh
```

The command nano `get_tags.sh` opens the file `get_tags.sh` within the text editor ‘nano’ (which runs within the shell). If the file does not exist, it will be created. We can use the text editor to directly edit the file – we’ll simply insert the following line:

```bash
echo -n "atlanta.gbff: "
grep "/locus_tag=" atlanta.gbff | wc -l
```

Then we save the file (Ctrl-O in nano), and exit the text editor (Ctrl-X in nano). Check that the directory molecules now contains a file called `get_tags.sh`.

Once we have saved the file, we can ask the shell to execute the commands it contains. Our shell is called bash, so we run the following command:

```bash
$ bash count_tags.sh
```

```
atlanta.gbff 12237
```
{:.output}

Sure enough, our script’s output is exactly what we would get if we ran that pipeline directly.

I've noticed that each locus tag appears twice in the gbff file, so we're double-counting a lot of tags. Let's modify our script to only count unique tags:

```bash
$ nano count_tags.sh
```

```bash
echo -n "atlanta.gbff: "
grep "/locus_tag=" atlanta.gbff | sort | uniq | wc -l
```

Now when we run the script:

```bash
$ nano count_tags.sh
```

```
atlanta.gbff 6109
```
{:.output}


What if we want to count the number of locus tags in many files? Let's introduce a new concept: loopingOpen up our script again

```bash
$ nano count_tags.sh
```

```bash
for filename in atlanta.gbff london.gbff
do
  echo -n "$filename "
  grep "/locus_tag=" $filename | sort | uniq | wc -l
done
```

We feed the loop with two elements: the text "atlanta.gbff" and then with the text "london.gbff".
The loop executes all of the commands between `do` and `done` for each time the loop iterates, and each time, the variable "$filename" is replaced by either "atlanta.gbff" or "london.gbff"


What if we wanted to count the number of tags in other gbff file. At the moment, the filenames are hard-coded into our script. It only counts tags in atlanta.gbff and london.gbff. We can make the script a little bit more flexible by using the variable nams "$1" and "$2"

```bash
$ nano get_tags.sh
```

Now, within “nano”, replace the text "atlanta.gbff" with the special variable called $1:

```bash
for filename in $1 $2
do
  echo -n "$filename "
  grep "/locus_tag=" $filename | sort | uniq | wc -l
done
```

Inside a shell script, `$1` means ‘the first filename (or other argument) on the command line’. Similarly, `$2` is the second argument passed to the script. We can now run our script like this:

```bash
$ bash count_tags.sh atlanta.gbff london.gbff
```

```
atlanta.gbff 6109
london.gbff 5831
```
{:.output}


or on a different file like this:

```bash
$ bash count_tags.sh nevada.gbff texas.gbff
```

```
nevada.gbff 5691
texas.gbff 5424
```
{:.output}


In case the filename happens to contain any spaces, we surround $1 with double-quotes.

This is better, but our script still isn't quite as flexible as I'd like. It can only operate on two files at a time. What if I wanted to count the tags in three or four files at a time?

There is a special variable "$@" which holds all of the arguments passed to the script. Let's make another modification to our script:


```bash
$ nano get_tags.sh
```

```bash
for filename in $@
do
  echo -n "$filename "
  grep "/locus_tag=" $filename | sort | uniq | wc -l
done
```

The "$@" variable gets replaced with all of the arguments passed to our script. This allows us to run:

```bash
$ bash count_tags.sh *.gbff
```

```
atlanta.gbff 6109
braunschweig.gbff 5248
lab-strain.gbff 4609
london.gbff 5831
muenster.gbff 5176
nevada.gbff 5691
texas.gbff 5424
```
{:.output}


This works, but it may take the next person who reads `count_tags.sh` a moment to figure out what it does. We can improve our script by adding some comments at the top:

```bash
$ nano count_tags.sh
```

```bash
# Counts the number of unique locus tags in one or more gbff files.

for filename in $@
do
  echo -n "$filename "
  grep "/locus_tag=" $filename | sort | uniq | wc -l
done
```

A comment starts with a `#` character and runs to the end of the line. The computer ignores comments, but they’re invaluable for helping people (including your future self) understand and use scripts. The only caveat is that each time you modify the script, you should check that the comment is still accurate: an explanation that sends the reader in the wrong direction is worse than none at all.

Lastly, let's make our script executable. First we'll modify the file permissions. This can be done using the `chmod` command:

```bash
chmod +x count_tags.sh
```

We'll also add a "shebang" line to our script which tells the shell what program should be used to run our script. Modify our script to include this first line:

```bash
#!/bin/bash
# Counts the number of unique locus tags in one or more gbff files.

for filename in $@
do
  echo -n "$filename "
  grep "/locus_tag=" $filename | sort | uniq | wc -l
done
```

Exercise: Can you create a command that uses our count_tags.sh script to find the gbff file with the smallest number of locus tags?

## Key Points

1. Save commands in files (usually called shell scripts) for re-use.
2. `bash [filename]` runs the commands saved in a file.
3. `$@` refers to all of a shell script’s command-line arguments.
4. `$1`, `$2`, etc., refer to the first command-line argument, the second command-line argument, etc.
5. Place variables in quotes if the values might have spaces in them.
6. Letting users decide what files to process is more flexible and more consistent with built-in Unix commands.






















