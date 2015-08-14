#Workshop: Automating Tasks  
A demonstration of how to reduce repetitive taks with the UNIX shell

##My script

    #!/bin/bash

    #This script will compare the contents of two weekly Argo reports on hydrus items and report back all the newly-accessioned items that I am interested in. See details below.

    #To run this
    #bash argo_comparisons.sh [name of first report file for comparison] [name of second report file for comparison]

    #Assign current date to a variable.
    today=$(date +"20%y%m%d")

    #Select only accessioned content from the two files you want to compare and dump the output from both into a single file
    grep -i "accessioned" $1 > final_file.csv
    grep -i "accessioned" $2 >> final_file.csv

    #For the new combined set of data, sort the remaining content and 
    #remove all entries that are not unique. 
    #Resort -- I used to think this step was necessary but I have since removed it without any problems -- and
    #remove content from University Archives staff (those where the users are dhartwig and jejohns).
    sort final_file.csv | uniq -u | grep -vE "(dhartwig|jejohns)"  > results$today.csv

    #When finished, remove the temporary final_file.csv
    rm final_file.csv

##Overview
Note that most of this is documentation and not the code itself.  
Note somewhere how we end up not touching the data files at all -- the originals are totally unaltered, whereas it's easy to get things messed up in the spreadsheets.

Outline of the code:
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

##Original workflow  
$grep -i "accessioned" DATE1hydrus.csv > DATE1hydrus_accessioned.csv  
$grep -i "accessioned" DATE2hydrus.csv > DATE2hydrus_accessioned.csv  
$cat DATE1hydrus_accessioned.csv > DATEfinal_file.csv  
$cat DATE2hydrus_accessioned.csv >> DATEfinal_file.csv  
$sort DATEfinal_file.csv > sorted.csv  
$uniq -u sorted.csv > unique.csv  
$grep -vE "(dhartwig|jejohns)" unique.csv > DATEresults.csv  

DATE1 and DATE2 correspond to the dates the two different hydrus reports were downloaded.  
DATE refers to the date of the analysis (usually the same as DATE2). These should all be in the format YYYYMMDD.  

##The problem
I wanted to be able to compare the content of two separate files to identify new information that I had determined was of interest to me. I wanted to do this on a weekly basis, comparing this week's report with last week's report.

##Spreadsheets
Would it be possible to demonstrate how I might go about doing a task like this in a spreadsheet?    
1. Open a the first file in a spreadsheet and rename to "datafile." Import content of second spreadsheets. Delete second header row.  
2. Sort all the content by PURL, but still hard to see.  
3. Data > Filter. Start with only stuff that is accessioned. Then try to eliminate the stuff I'm not interested in from Spec. Show how this would take a really long time to uncheck all the individual boxes.  
4. Then I still need to go through the spreadsheet by hand to identify the duplicates. With conditional formatting you might be able to identify some things, but...  

Let's try the shell!    

##UNIX Shell
Not going to cover everything, just want to introduce a few basic things that will let us do this particular task.

### Get the data from the two files into one file.

I first want to eliminate everything that hasn't yet finished processing; I only want to see the done stuff.

    COMMAND: grep
    Let's you identify search for contents within a file.
    Flag: -i
    Does this in a case-insensitive way
    
    grep -i "accessioned" file1.csv
    
Output goes to the screen. We want this to go to a file so we can do something with the output. Output that goes to the screen isn't saved anywhere. We'll save everything into files as we go, so we can double check things if we want to, or go back a step instead of to the beginning if we mess up.

    COMMAND: >
    This redirects content into a file instead of to your screen. 
    Caution: 
    This will overwrite the content of an existing file without warning!

    grep -i "accessioned" file1.csv > file1_noacc.csv
    
And then do the same thing for the second file.

    grep -i "accessioned" file2.csv > file2_noacc.csv
    
But wait. We wanted this to be in the same file, right? We can do that when we do the filtering on the second file.

    COMMAND: cat
    This command reads the content of the file.
    
    COMMAND: >>
    This appends the redirected content to an existing file, instead of overwriting it.

    cat file1_noacc.csv > nacc.csv
    cat file2_noacc.csv >> noacc.csv
    
Great. We have all the output for all the finished content in the same file.
    
Now we're at the point where we would like to identify all the lines that are new since the last time we ran the report. So we want to compare the two and just get the lines that appear once.

    COMMAND: uniq
    This reports or filters out repeated adjacent lines in a file
    FLAG: -u
    This only outputs lines that are not repeated in the file

This is great, but it means we need to sort the file first so that lines that are identical are next to each other. 

    COMMAND: sort
    This command sorts the lines of a text file.
    
To sort our file and save it as another file, we would write:

    sort noacc.csv > sorted.csv
    
Then we can run the `uniq` command to get the new stuff.

    uniq -u sorted.csv > unique.csv
    
The last thing I still need to do is eliminate all the lines with a status that includes "dhartwig" or "jejohns". `grep' can do this for us if we use two special flags.

    FLAG: -v
    This will invert the results and output only the things that *don't* match the pattern
    FLAG: -E
    This tells the program that we are going to be using a regular expression for the pattern that it needs to match
    
We aren't going to get into regular expressions here, but they let you do some sophisticated pattern matching. Note that we can stack up the flags.

    grep -vE "(dharwig|jejohns)" unique.csv > results.csv
    
And now we have all our results in this final file. But that was a lot of steps and a lot of typing! And once we're done we have a lot of intermediate files lying around that we non longer need.

##Automate with a script! 

**Define what a script is here. We'll talk about how to run it later.

We can start by condensing all the above commands together.

    grep -i "accessioned" file1.csv > file1_noacc.csv
    grep -i "accessioned" file2.csv > file2_noacc.csv
    cat file1_noacc.csv > noacc.csv
    cat file2_noacc.csv >> noacc.csv
    sort noacc.csv > sorted.csv
    uniq -u sorted.csv > unique.csv
    grep -vE "(dharwig|jejohns)" unique.csv > results.csv

Now let's start eliminating all the stuff we don't need if we are running this all at one time. 

First off, I'm noticing that we don't really need the file1_noacc.csv and file2_noacc.csv files. We could just direct that content directly into noacc.csv. Let's do that.

    grep -i "accessioned" file1.csv > noacc.csv
    grep -i "accessioned" file2.csv >> noacc.csv
    sort noacc.csv > sorted.csv
    uniq -u sorted.csv > unique.csv
    grep -vE "(dharwig|jejohns)" unique.csv > results.csv

How about those other intermediate files. Can we eliminate some of them?

    COMMAND: |
    This allows you to redirect the output from one command directly into the next command as input.
    
Let's redirect the output from `sort` into the `uniq` function, and the output from `uniq` into the `grep` function.

    sort noacc.csv | uniq -u | grep -vE "(dharwig|jejohns)" > results.csv

That's nice! Now the entire thing is:

    grep -i "accessioned" file1.csv > noacc.csv
    grep -i "accessioned" file2.csv >> noacc.csv
    sort noacc.csv | uniq -u | grep -vE "(dharwig|jejohns)" > results.csv
    
We can save this in a file with the extension ".sh", put it in the directory where our data files are, and then run it.

    COMMAND: bash <filename>
    
Several things to do here still:
we want to be able to change on the fly what the input files are and not hard code those
we want to be able to change the name of the results file by appending the current date.
