<h2>fatools: A utillity package written in python for working with fasta sequences.</h2>

A total of 30 utilities/options for common operarations with fasta sequences are currently organized under 6 main subcommands.
The utilities range from searching specific sequence entries from a large set of fasta sequences based on 
ID or a text string in the defline or a sequence motif to spliting a large sequence file into small chuncks, 
reporting summary stats of a genome assembly, and filtering sequences by length or gap size or redundant entries based on ID or sequences. Furthermore, by allowing input from and output to stdout, multiple processes can be done sequencially in one line of commpands via use of pipe (|). Make sure you have python 2 installed to run fatools.

### List of subcommands

Typing 'fatools ' displays the list of subcomands; typing 'fatools <subcommand>' displays the detailed utilities/options for the subcommand.

<details>
  <summary> convert </summary>
  
  **-r** print sequence in revevrse compliment. </br>
  
  **-N** convert all non-ACGT letters to N.</br>
  
  **-R** remove all non-ACGT letters.</br>

  **-U** to upper case </br>

  **-u** to lower case
	
  
  ---
</details>

<details>
  <summary> extract </summary>

 **-F N** extract the first N fasta entries. </br>
 
 **-B N** extract from the Nth entry to the last entry.</br>
 
 use '-B N -F M' to extract sequences from N to M enries (inclusive). </br>
 
 **-I N** extract sequence from N bp to the end (1-based). </br>

 **-E N** extract sequence up to to N bp (1-based).</br>

 use '-I N -E M' to extract sequence from 'N to M' bp (inclusive). </br>
   
 Note: The -I and -E options were designed for working with a single long sequences, even though they will work for multiple sequences by applying the same operation to all sequences. </br>
     
 ---
</details>

<details>
  <summary> filter </summary>
  
 **-g N** skip sequences with N or more Ns. </br>
 
 **-r 1/2** 1: skip redundant entry based ID; 2: keep redundant entries by adding 
  a serial number to the identical IDs to make each ID unique.</br>
 
 **-R 1/N** skip redundant entries based on sequence. 
  1: use the entire sequence; N: use only the first and last N bases. </br>
 
 **-l N** skip sequences shorter than N bp. </br>

 **-L N** skip sequences longer than N bp.</br>

 use -l N -L M for sequences with length from N to M bp (inclusive).</br>

In all options, '-e' can be added to print the skipped entries in STDERR, which can be captured using 2>[skipped.fa].

---
</details>

<details>
  <summary> report </summary>
  
  **-f** print fasta entries as in the input. </br>
  
  **-F** print fasta entries with all sequence in one line. </br>
  
  **-n** print sequences without the define. </br>
  
  **-d** print deflines in short form (part before the first space). </br>
  
  **-D** print deflines in the original form. </br>
  
  **-c** print the total number of fasta entries in the input. </br>
  
  **-l** print short defline +[\t] length. </br>
  
  **-L** print original defline +[\t] length. </br>
  
  **-s** print sequence summary statistics including N50. </br>
  
  **-S** print sequence summary statistics plus detailed gap info. </br>
  
  ---
</details>

<details>
  <summary> search </summary>
  
  **-s  string:** search for entries containing "string" in the sequence. </br>
  
  **-d  string:** search for entries containing "string" in the defline: Default is for exact match; use "/string" to search for entries with "string" as part of the ID. </br>     
  
  **-F  file:** search for sequences based on a list of IDs in the file (one ID/line).</br>
      Can use -D to specify delimiter in the defline. Default is space or '|' or end of line; </br>
      use -i to specify the field number, default is 1. </br>
      
  **-1**  print only the 1st match for -d and -s. </br>
  
  **-v**  use with -s, -d or -f to negate the search.</br>
  
  ---
</details>

<details>
  <summary> split </summary> 
  
 **-G N** split each of the sequences in the input file as non-gap fragments. </br> 
 "N" is the number of consecutive "N/n" base, default value is 1; </br>
 Use -G N with -t to  print just the gap positions.  </br>
 
 **-n N** split the input sequences into chunks, each containing N fasta entries (the last chunk may be less). </br>

 **-N N** split the input sequences into N chunks, each containing equal number of entries (last one may be smaller). </br>

 **-M N** split the input sequences into chunks at ~N MB (million bp) in size (last chunk may be smaller). </br>

 **-o file:** prefix for output files (serial numbers added to prefix; required).

---
</details>

<p><h2>Making fatools executable and available from any directory</h2>

#### Linux
1. Open the terminal and navigate to where you have downloaded fatools
2. Find where python2 is installed in your system `which python`. Usually you would get something like /usr/bin/python
3. Copy this output to the beggining of fatools as `#!/usr/bin/python`.
4. Run the following command to make the script executable `chmod +x fatools`
5. Add fatools to your bin directory or any other directories included in your $PATH
6. You should be able to run fatools from anywhere!

#### Windows
1. Type 'control panel' in the Windows search bar
2. Go to System and Security > System > Advanced System settings > Environment variables
3. Under system variables, select 'Path' and click 'Edit' and then 'New'
4. Add the path of where you have fatools located and click OK
5. You should be able to run fatools from anywhere!


<p><h2>Examples</h2>

Navigate to the exampleFiles directory in this repository. In there, a fasta file (exampleFasta.fa) and a file containing a list of IDs (IDlist.txt) from exampleFasta.fa.

To extract the fasta sequences from exampleFasta.fasta based on the list of IDs:</br>
`fatools search -F IDlist.txt exampleFasta.fa` </br>

```
NP_001245510.1 notch, isoform B [Drosophila melanogaster]
NNMQSQRSRRRSRAPNTWICFWINKMHAVASLPASLPLLLLTLAFANLPNTVRGTDTALVAASCTSVGCQNG
GTCVTQLNGKTYCACDSHYVGDYCEHRNPCNSMRCQNGGTCQVTFRNGRPGISCKCPLGFDESLCEIAVP
NACDHVTCLNGGTCQLKTLEEYTCACANGYTGERCETKNLCASSPCRNGATCTALAGSSSFTCSCPPGFT... 

DY343456.1 Macropus rufus BRCA1 (BRCA1) gene, partial cds
CAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAGTCTGGATGAAAGTAAGGAAATATGTAGTGCTGGA
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGTAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCA 

FY343456.1 Macropus rufus BRCA1 (BRCA1) gene, partial cds
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGCAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAG
TCTGGATGAAAGTAAGGAAATATGTAGTGCTGGAAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCAT 
```

To report summary statistics:</br>
`fatools report -s exampleFasta.fa`</br>

```
Total 2,868 bps from qualified 5 sequences (5 total); length average: 573 (210-1262) bp; N50: 698 bp
```

To get fasta sequences with a specific maximum length filter

`python fatools filter -L250 exampleFasta.fa` </br>

```
DY343456.1
CAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAGTCTGGATGAAAGTAAGGAAATATGTAGTGCTGGA
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGTAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCA

FY343456.1
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGCAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAG
TCTGGATGAAAGTAAGGAAATATGTAGTGCTGGAAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCAT 
```

You can combine multiple utilities using pipe "|". Let's say you want to see the short defline and the length of the first 3 fasta sequences in a fasta file.

``fatools extract -F3 sequenceTesting2.txt  | fatools report -l -``

```
AY211956.1Macropus(BRCA1)gene,partialcds       698 
NP_001245510.1  1262 
DY343456.1      210
``` 

Or extract the sequences from a large sequence set for a list of IDs and then search sequences with a specific sequence by using

``fatools search -F IDlist.txt exampleFasta.fa |fatools -S AAATAAA -``
