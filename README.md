# Shell scripting notes<a name="TOP"></a> 


# Shell scripting notes from Datacamp #
This repository contains my shell scripting notes based on DataCamp shell scripting course.
pwd = current working directory
cd  = change working directory i.e cd /home/repl
cp  = copy files
mv  = move files
rm  = remove files(not directories) IMPORTANT: before removing double check is needed as there is no undo. 
rmdir  = remove directories only when directory is empty for safety of work purposes. 

**If a directory starts with / then it is absolute path. Otherwise it is a relative path.**
# How can I list everything below a directory?
In order to see everything underneath a directory, no matter how deeply nested it is, you can give ls the flag -R (which means "recursive"). If you use ***ls -R*** in your home directory, you will see something like this:

backup          course.txt      people          seasonal

./backup:

./people:
agarwal.txt

./seasonal:
autumn.csv      spring.csv      summer.csv      winter.csv
This shows every file and directory in the current level, then everything in each sub-directory, and so on.

# How can I get help for a command?
To find out what commands do, people used to use the man command (short for "manual"). For example, the command man head brings up this information:

HEAD(1)               BSD General Commands Manual              HEAD(1)

NAME
     head -- display first lines of a file

SYNOPSIS
     head [-n count | -c bytes] [file ...]

DESCRIPTION
     This filter displays the first count lines or bytes of each of
     the specified files, or of the standard input if no files are
     specified.  If count is omitted it defaults to 10.

     If more than a single file is specified, each file is preceded by
     a header consisting of the string ``==> XXX <=='' where ``XXX''
     is the name of the file.

SEE ALSO
     tail(1)
man automatically invokes less, so you may need to press spacebar to page through the information and :q to quit.

The one-line description under NAME tells you briefly what the command does, and the summary under SYNOPSIS lists all the flags it understands. Anything that is optional is shown in square brackets [...], either/or alternatives are separated by |, and things that can be repeated are shown by ..., so head's manual page is telling you that you can either give a line count with -n or a byte count with -c, and that you can give it any number of filenames.

The problem with the Unix manual is that you have to know what you're looking for. 

The command tail **-n +7 seasonal/spring.csv** is a shell script that uses the tail command to display the contents of the file seasonal/spring.csv, starting from the 7th line to the end of the file.

# How can I select columns from a file?
head and tail let you select rows from a text file. If you want to select columns, you can use the command cut. It has several options (use man cut to explore them), but the most common is something like:

cut -f 2-5,8 -d , values.csv
which means "select columns 2 through 5 and columns 8, using comma as the separator". cut uses -f (meaning "fields") to specify columns and -d (meaning "delimiter") to specify the separator. You need to specify the latter because some files may use spaces, tabs, or colons to separate columns.


# How can I repeat commands?
One of the biggest advantages of using the shell is that it makes it easy for you to do things over again. If you run some commands, you can then press the up-arrow key to cycle back through them. You can also use the left and right arrow keys and the delete key to edit them. Pressing return will then run the modified command.

Even better, history will print a list of commands you have run recently. Each one is preceded by a serial number to make it easy to re-run particular commands: just type !55 to re-run the 55th command in your history (if you have that many). You can also re-run a command by typing an exclamation mark followed by the command's name, such as !head or !cut, which will re-run the most recent use of that command.

# How can I select lines containing specific values?
head and tail select rows, cut selects columns, and grep selects lines according to what they contain. In its simplest form, grep takes a piece of text followed by one or more filenames and prints all of the lines in those files that contain that text. For example, grep bicuspid seasonal/winter.csv prints lines from winter.csv that contain "bicuspid".

grep can search for patterns as well; we will explore those in the next course. What's more important right now is some of grep's more common flags:

* -c: print a count of matching lines rather than the lines themselves
* -h: do not print the names of files when searching multiple files
* -i: ignore case (e.g., treat "Regression" and "regression" as matches)
* -l: print the names of files that contain matches, not the matches
* -n: print line numbers for matching lines
* -v: invert the match, i.e., only show lines that don't match

# Why isn't it always safe to treat data as text?
The SEE ALSO section of the manual page for cut refers to a command called paste that can be used to combine data files instead of cutting them up.

Read the manual page for paste, and then run paste to combine the autumn and winter data files in a single table using a comma as a separator. 


# How can I store a command's output in a file?
All of the tools you have seen so far let you name input files. Most don't have an option for naming an output file because they don't need one. Instead, you can use redirection to save any command's output anywhere you want. If you run this command:

head -n 5 seasonal/summer.csv
it prints the first 5 lines of the summer data on the screen. If you run this command instead:

head -n 5 seasonal/summer.csv > top.csv
nothing appears on the screen. Instead, head's output is put in a new file called top.csv. You can take a look at that file's contents using cat:

cat top.csv
The greater-than sign > tells the shell to redirect head's output to a file. It isn't part of the head command; instead, it works with every shell command that produces output.

# How can I use a command's output as an input?
Suppose you want to get lines from the middle of a file. More specifically, suppose you want to get lines 3-5 from one of our data files. You can start by using head to get the first 5 lines and redirect that to a file, and then use tail to select the last 3:

head -n 5 seasonal/winter.csv > top.csv
tail -n 3 top.csv
A quick check confirms that this is lines 3-5 of our original file, because it is the last 3 lines of the first 5.

# What's a better way to combine commands?
Using redirection to combine commands has two drawbacks:

It leaves a lot of intermediate files lying around (like top.csv).
The commands to produce your final result are scattered across several lines of history.
The shell provides another tool that solves both of these problems at once called a *pipe*. Once again, start by running head:

head -n 5 seasonal/summer.csv
Instead of sending head's output to a file, add a vertical bar and the tail command without a filename:

head -n 5 seasonal/summer.csv | tail -n 3
The pipe symbol tells the shell to use the output of the command on the left as the input to the command on the right.

# How can I count the records in a file?
The command wc (short for "word count") prints the number of characters, words, and lines in a file. You can make it print only one of these using -c, -w, or -l respectively.

# How can I specify many files at once?
Most shell commands will work on multiple files if you give them multiple filenames. For example, you can get the first column from all of the seasonal data files at once like this:

cut -d , -f 1 seasonal/winter.csv seasonal/spring.csv seasonal/summer.csv seasonal/autumn.csv
But typing the names of many files over and over is a bad idea: it wastes time, and sooner or later you will either leave a file out or repeat a file's name. To make your life better, the shell allows you to use wildcards to specify a list of files with a single expression. The most common wildcard is *, which means "match zero or more characters". Using it, we can shorten the cut command above to this:

cut -d , -f 1 seasonal/*
or:

cut -d , -f 1 seasonal/*.csv

# What other wildcards can I use?
The shell has other wildcards as well, though they are less commonly used:

* ? matches a single character, so 201?.txt will match 2017.txt or 2018.txt, but not 2017-01.txt.
* [...] matches any one of the characters inside the square brackets, so 201[78].txt matches 2017.txt or 2018.txt, but not 2016.txt.
* {...} matches any of the comma-separated patterns inside the curly brackets, so {*.txt, *.csv} matches any file whose name ends with .txt or .csv, but not
files whose names end with .pdf.

# How can I remove duplicate lines? #
Another command that is often used with sort is uniq, whose job is to remove duplicated lines. More specifically, it removes adjacent duplicated lines. If a file contains:

2017-07-03
2017-07-03
2017-08-03
2017-08-03
then uniq will produce:

2017-07-03
2017-08-03
but if it contains:

2017-07-03
2017-08-03
2017-07-03
2017-08-03
then uniq will print all four lines. The reason is that uniq is built to work with very large files. In order to remove non-adjacent lines from a file, it would have to keep the whole file in memory (or at least, all the unique lines seen so far). By only removing adjacent duplicates, it only has to keep the most recent unique line in memory.


<script>
// Function to scroll to the top of the page when the "Go To Top" link is clicked
function goToTop() {
  window.scrollTo({ top: 0, behavior: 'smooth' });
}
</script>
 [Go To TOP](goToTop) 
