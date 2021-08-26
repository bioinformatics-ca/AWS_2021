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
$ nano get_tags.sh
```

The command nano `get_tags.sh` opens the file `get_tags.sh` within the text editor ‘nano’ (which runs within the shell). If the file does not exist, it will be created. We can use the text editor to directly edit the file – we’ll simply insert the following line:

```bash
grep "/locus_tag=" atlanta.gbff | head -n 5
```

This is a variation on the pipe we constructed earlier: it selects lines 11-15 of the file `atlanta.gbff`. Remember, we are not running it as a command just yet: we are putting the commands in a file.

Then we save the file (Ctrl-O in nano), and exit the text editor (Ctrl-X in nano). Check that the directory molecules now contains a file called `get_tags.sh`.

Once we have saved the file, we can ask the shell to execute the commands it contains. Our shell is called bash, so we run the following command:

```bash
$ bash get_tags.sh
```

```
    /locus_tag="AYP09_RS32715"
    /locus_tag="AYP09_RS32715"
    /locus_tag="AYP09_RS32720"
    /locus_tag="AYP09_RS32720"
    /locus_tag="AYP09_RS00575"
```
{:.output}

Sure enough, our script’s output is exactly what we would get if we ran that pipeline directly.

What if we want to select lines from an arbitrary file? We could edit `get_tags.sh` each time to change the filename, but that would probably take longer than typing the command out again in the shell and executing it with a new file name. Instead, let’s edit `get_tags.sh` and make it more versatile:

```bash
$ nano get_tags.sh
```

Now, within “nano”, replace the text "atlanta.gbff" with the special variable called $1:

```bash
grep "/locus_tag=" $1 | head -n 5
```

Inside a shell script, `$1` means ‘the first filename (or other argument) on the command line’. We can now run our script like this:

```bash
$ bash get_tags.sh atlanta.gbff
```

```
    /locus_tag="AYP09_RS32715"
    /locus_tag="AYP09_RS32715"
    /locus_tag="AYP09_RS32720"
    /locus_tag="AYP09_RS32720"
    /locus_tag="AYP09_RS00575"
```
{:.output}


or on a different file like this:

```
$ bash get_tags.sh london.gbff
```

```
    /locus_tag="C4R22_RS00005"
    /locus_tag="C4R22_RS00005"
    /locus_tag="C4R22_RS00010"
    /locus_tag="C4R22_RS00010"
    /locus_tag="C4R22_RS00015"
```
{:.output}


In case the filename happens to contain any spaces, we surround $1 with double-quotes.

Currently, we need to edit `get_tags.sh` each time we want to adjust the range of lines that is returned. Let’s fix that by configuring our script to instead use three command-line arguments. After the first command-line argument ($1), each additional argument that we provide will be accessible via the special variables `$1`, `$2`, `$3`, which refer to the first, second, third command-line arguments, respectively.

Knowing this, we can use additional arguments to define the range of lines to be passed to head and tail respectively:

```bash
$ nano get_tags.sh
```

Edit the file so that it now contains the lines:

```bash
head -n "$2" "$1" | tail -n "$3"
```

We can now run:

```bash
$ bash get_tags.sh atlanta.gbff 15 5
```

```
    /locus_tag="AYP09_RS00590"
    /locus_tag="AYP09_RS00590"
    /locus_tag="AYP09_RS00600"
    /locus_tag="AYP09_RS00600"
    /locus_tag="AYP09_RS00605"
```
{:.output}

By changing the arguments to our command we can change our script’s behaviour:

```bash
$ bash get_tags.sh atlanta.gbff 20 5
```

```
    /locus_tag="AYP09_RS00605"
    /locus_tag="AYP09_RS00610"
    /locus_tag="AYP09_RS00610"
    /locus_tag="AYP09_RS00615"
    /locus_tag="AYP09_RS00615"
```
{:.output}

This works, but it may take the next person who reads `get_tags.sh` a moment to figure out what it does. We can improve our script by adding some comments at the top:

```bash
$ nano get_tags.sh
```

```bash
# Extract locus tags from gbff file.
# Usage: bash get_tags.sh filename end_line num_lines
grep "/locus_tag=" "$1" | head -n "$2" | tail -n "$3"
```

A comment starts with a `#` character and runs to the end of the line. The computer ignores comments, but they’re invaluable for helping people (including your future self) understand and use scripts. The only caveat is that each time you modify the script, you should check that the comment is still accurate: an explanation that sends the reader in the wrong direction is worse than none at all.

What if we want to process many files in a single pipeline? For example, if we want to sort our .gbff files by the number of locus tag annotations they contain? We might type

```bash
$ grep -c "/locus_tag" *.gbff | sort -t ':' -k2 -n
```

because `grep -c` counts the number of occurences of the search string and `sort -n` sorts things numerically. We also tell sort that the output is delimited by a colon `-t ":"` and we want to sort by the second column `-k2`. We could put this in a file, but then it would only ever sort a list of .gbff files in the current directory. If we want to be able to get a sorted list of other kinds of files, we need a way to get all those names into the script. We can’t use `$1`, `$2`, and so on because we don’t know how many files there are. Instead, we use the special variable `$@`, which means, ‘All of the command-line arguments to the shell script’. We also should put `$@` inside double-quotes to handle the case of arguments containing spaces ("`$@`" is special syntax and is equivalent to "`$1`" "`$2`" …).

Here’s an example:

```bash
$ nano sorted.sh
```

```bash
# Sort files by the number of locus tags.
# Usage: bash sorted.sh one_or_more_filenames
grep -c "/locus_tag" "$@" | sort -t ':' -k2 -n
```

Now we can run

```bash
$ bash sorted.sh *.gbff
```

```
lab-strain.gbff:9135
muenster.gbff:10353
braunschweig.gbff:10498
texas.gbff:10864
nevada.gbff:11400
london.gbff:11671
atlanta.gbff:12237
```
{:.output}

## Key Points

1. Save commands in files (usually called shell scripts) for re-use.
2. `bash [filename]` runs the commands saved in a file.
3. `$@` refers to all of a shell script’s command-line arguments.
4. `$1`, `$2`, etc., refer to the first command-line argument, the second command-line argument, etc.
5. Place variables in quotes if the values might have spaces in them.
6. Letting users decide what files to process is more flexible and more consistent with built-in Unix commands.






















