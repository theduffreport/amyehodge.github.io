#Workshop: Automating Tasks  
The UNIX shell is a great tool for automating relatively simple tasks. This workshop will walk you through an example of how to divide up a particular task into its component parts, use shell programs to complete those individual tasks, string together those individual tasks, and then run the entire workflow using a single command. This is not a hands-on workshop, but the files used in the workshop are contained in this directory so that you can attempt to duplicate this later on your own.


##The problem
I wanted to be able to compare the content of two separate reports run on different days (file1.csv and file2.csv) to identify content that was of interest to me. These reports contain information on cummulative activity over time, and I am just interested in a specific subset of that data:  
* new since last time  
* not still processing (Status should only be listed as "Accessioned")  
* not from one particular group (this corresponds to two particular users who are specified within the Source ID field: dhartwig and jejohns).  

Also, I wanted to do this on a weekly basis, comparing this week's report with last week's report.

##Spreadsheets
I first considered whether it would be possible to do this in a spreadsheet.     
1. You could try comparing the two spreadsheets to each other, but 4500+ lines is a lot to compare.
2. You could try sorting it first, which might make it easier.
3. You could try putting all of the content from both sheets into the same sheet, maybe sorting on that and trying to elimiate the duplicates.
4. You could use some filters on this combined sheet to eliminate some things, but this is also slow.
5. I might be able to identify duplicates with conditional formatting, but this is not easy to do when you need to compare the entire row.
6. In the end I still need to go through the spreadsheet by hand to eliminate duplicates. 

Let's try the shell!    

##UNIX Shell
The UNIX shell is a command-line interpreter that provides an interface for the UNIX operating system. It interprets the text that is typed into it by the user and translates that into instructions for the operating system to execute. We interact with the shell at the command line.

In this workshop we aren't going to cover everything about the shell. I'm just going to introduce a few basic things to you that will let us do this particular task, so that you can get a feel for what this can do.

### Select the data that is done processing.

I started by eliminating everything that hasn't yet finished processing, so that's anything that does not have "Accessioned" in the status column.

    COMMAND: grep
    Let's you search for contents within a file.
    Flag: -i
    Does this in a case-insensitive way
    
The grep command identifies lines within your file that match a pattern. This is exactly what I want: I want to keep the entire line, when a particular word ("accessioned") is identified within that line. I'm not 100% sure that the word is always capitalized so I'm going to use a flag.

Flags are additional options that alter the default behavior of a command. The default behavior of this command is case sensistive. I want to make it case insensitive.

    grep -i "accessioned" file1.csv

Output goes to the screen. We want this to go to a file so we can do something with the output. Output that goes to the screen isn't saved anywhere. We'll save everything into files as we go, so we can double check things if we want to, or go back a step instead of to the beginning if we mess up.

    COMMAND: >
    This redirects content into a file instead of to your screen. 
    WARNING: 
    This will overwrite the content of an existing file without warning!  

    grep -i "accessioned" file1.csv > file1_accessioned.csv
    
And then do the same thing for the second file.

    grep -i "accessioned" file2.csv > file2_accessioned.csv
    
That takes care of our first requirement of only getting the stuff that has finished processing. Let's move on to the second step.

### Select the data that is new since last time.

Now we're at the point where we would like to identify all the lines that are new since the last time we ran the report. So we want to compare the two and just get the lines that appear once.

    COMMAND: uniq
    This reports or filters out repeated adjacent lines in a file
    FLAG: -u
    This only outputs lines that are not repeated in the file

This command will identify adjacent lines that are the same, but it only does that for content within a single file. 

The default behavior of this command is to output one copy of each unique line in the file; basically a non-redundant union of the content. I only want to see the ones that don't have duplicates. The flag `-u` will direct this program only to output lines that are not repeated -- so the new stuff. 

But first, we need to get our content into a single file. 

    COMMAND: cat
    This command reads the content of the file.
    
The cat command reads the content of the file and then does with it whatever I instruct. I would like to put the data from file1_accessioned.csv and file2_accessioned.csv into the same file together called accessioned.csv. 

If I use `>` in combination with `cat`, I can get the computer put the content of file1_acc.csv into a new file.

    cat file1_accessioned.csv > accessioned.csv
    
Then I want to add in the data from the second file. But check the warning above! `>` will overwrite my file. I don't want to do that; I just want to append the content to the file.

    COMMAND: >>
    This appends the redirected content to an existing file, instead of overwriting it.

Great! Now I can just add the content of file2_accessioned.csv into my combined.csv file.

    cat file2_accessioned.csv >> accessioned.csv
    
Now I have a single file with all my content. But remember that for the `uniq` command, we needed the identical lines to be adjacent. So, we need to sort our file somehow.

    COMMAND: sort
    This command sorts the lines of a text file.
    
To sort our file and save it as another file, we would write:

    sort accessioned.csv > sorted.csv
    
Then we can run the `uniq` command to get the new stuff.

    uniq -u sorted.csv > unique.csv

That finishes off the second requirement. Let's move on to the last one!

### Eliminate content from "dhartwig" and "jejohns"

The last thing I still need to do is eliminate all the lines with a status that includes "dhartwig" or "jejohns". `grep' can do this for us if we use two special flags.

    FLAG: -v
    This will invert the results and output only the things that *don't* match the pattern
    FLAG: -E
    This tells the program that we are going to be using a regular expression for the pattern that it needs to match
    
We aren't going to get into regular expressions here, but they let you do some sophisticated pattern matching. Note that we can stack up the flags.

    grep -vE "(dhartwig|jejohns)" unique.csv > results.csv
    
And now we have all our results in this final file. But that was a lot of steps and a lot of typing! And once we're done we have a lot of intermediate files lying around that we non longer need.

##Automate with a script! 

A UNIX script is just a file that contains UNIX commands, like the ones we've been writing. We're going to combine all our steps together and put them into a single script that we only have to type once!

We can start by condensing all the above commands together.

    grep -i "accessioned" file1.csv > file1_accessioned.csv
    grep -i "accessioned" file2.csv > file2_accessioned.csv
    cat file1_accessioned.csv > acc.csv
    cat file2_accessioned.csv >> acc.csv
    sort accessioned.csv > sorted.csv
    uniq -u sorted.csv > unique.csv
    grep -vE "(dhartwig|jejohns)" unique.csv > results.csv

Now let's start eliminating all the stuff we don't need if we are running this all at one time. 

First off, I'm noticing that we don't really need the file1_accessioned.csv and file2_accessioned.csv files. We could just direct that content directly into accessioned.csv. Let's do that.

    grep -i "accessioned" file1.csv > accessioned.csv
    grep -i "accessioned" file2.csv >> accessioned.csv
    sort accessioned.csv > sorted.csv
    uniq -u sorted.csv > unique.csv
    grep -vE "(dhartwig|jejohns)" unique.csv > results.csv

How about those other intermediate files. Can we eliminate some of them?

    COMMAND: |
    This allows you to redirect the output from one command directly into the next command as input.
    
Let's redirect the output from `sort` into the `uniq` function, and the output from `uniq` into the `grep` function.

    sort accessioned.csv | uniq -u | grep -vE "(dhartwig|jejohns)" > results.csv

That's nice! Now the entire thing is:

    grep -i "accessioned" file1.csv > accessioned.csv
    grep -i "accessioned" file2.csv >> accessioned.csv
    sort accessioned.csv | uniq -u | grep -vE "(dhatrwig|jejohns)" > results.csv
    
We can save this in a file with the extension ".sh", put it in the directory where our data files are, and then run it. Let's save it as compare.sh.

    COMMAND: bash <filename>
    $bash compare.sh
    

### Using variables

This is great, but there are a couple of inconvenient things about this script. Every time we run this script, we will need to open it up and change the names of the files we want to compare. We are also going to want to change the name of the results file so we aren't overwriting that every time we run this.

We can do these things in the script using variables.

    VARIABLES: $1 and $2
    These are special variables (actually $1 - $9) that are built into the UNIX system and correspond to the arguments passed to the program when it is run. 
    
In our case, we are going to use these to pass the filenames to the program instead of hard coding those into the script.

    grep -i "accessioned" $1 > accessioned.csv
    grep -i "accessioned" $2 >> accessioned.csv

I'm also going to assign a new variable of my own called `today`. I'm going to assign it today's date.

    today=$(date +"20%y%m%d")
    
And I'm going to append that variable to the name of the final results file so that every time I run the script it creates a new, dated file.

    sort accessioned.csv | uniq -u | grep -vE "(dhartwig|jejohns)"  > results$today.csv
    
###Tidying up

Lastly, once I'm done I don't need that accessioned.csv file I created any longer. Let's get rid of it.

    COMMAND: rm
    Removes an entry from a directory.
    
    rm final_file.csv
    
###Shebang

In order for the script to run, you need to put a line of text at the very beginning that tells it which shell it will be using to interpret the file. This is called the "shebang." It tells the computer which interpreter to use to run the script (we are using the bash shell here, which is very common).

    #!/bin/bash

##My script

So, here is the final script. This is stored in a file called script1.sh. Notice that this script actually contains more descriptive text than UNIX commands. Comments and other text will be ignored by UNIX if you precede the lines with a hashtag (`#`). By providing a lot of description, you -- and others who may use your script later -- will easily be able to figure out what the script does and why.

Also, note that by setting this up as an automated script, we do not have to physically open or modify the data files themselves. This helps to keep the original files raw and unadulterated, making it easier for us to change our processes later by starting with the original files.

Note as well that there is a line of instruction on how to run the script.

    #!/bin/bash

    #This script will compare the contents of two weekly Argo reports on hydrus items and report back all the newly-accessioned items that I am interested in. See details below.

    #To run this
    #bash argo_comparisons.sh [name of first report file for comparison] [name of second report file for comparison]

    #Assign current date to a variable.
    today=$(date +"20%y%m%d")

    #Select only accessioned content from the two files you want to compare and dump the output from both into a single file
    grep -i "accessioned" $1 > accessioned.csv
    grep -i "accessioned" $2 >> accessioned.csv

    #For the new combined set of data, sort the remaining content and 
    #remove all entries that are not unique and
    #remove content from a specific group (those where the users are dhartwig and jejohns).
    sort accessioned.csv | uniq -u | grep -vE "(dhartwig|jejohns)"  > results$today.csv

    #When finished, remove the temporary final_file.csv
    rm final_file.csv

###Review of the code:

1. Shebang  
2. What the code does overall  
3. How to run the code  
4. Create a variable and assign the current date  
5. Look for content in each of the two files that meets a certain criteria. Move the identified content into another file together  
6. Sort remaining content  
7. Remove duplicates in order to identify all the new stuff  
8. Remove new items from certain users that I'm not interested in  
9. Dump results into another file  
10. Remove the intermediate file  
