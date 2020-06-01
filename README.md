# fatools: A utillity package written in python for working with fasta sequences.

A total of 27 utilities/options for working with fasta sequences are currently organized under 5 main subcommands.
The utilities range from searching specific sequence entries from a large set of fasta sequences based on 
ID or a text string in the defline or a sequence motif to spliting a large sequence file into small chuncks, 
reporting summary stats of a genome assembly, and filtring sequences by length or gap size. Furthermore, by allowing input from and output to stdout, multiple processes can be done sequencially in one line of commpands via use of pipe (|). 

### List of commands
<details>
  <summary> search </summary>
  
  **-s  string:** search for entries containing "string" in the sequence </br>
  
  **-d  string:** search for entries containing "string" in the defline: Default is for exact match, use "/string" to search for entries with "string" as part of the ID </br>     
  
  **-F  file:** search for sequences for a list of IDs in the file.
      Can use -D to specify delimiter in the defline. Default is space or '|' or end of line. </br>
      use -i to specify the field number, default is 1. </br>
      
  **-1**  find single match for -d and -s. </br>
  
  **-v**  use with -s, -d or -f to negate the search
  
  ---
</details>

<details>
  <summary> split </summary> 
  
 **-G N** split the sequences as non-gap framments. "N" is the number of consecutive "N/n" base, default value is 1.
 Use -G with -t to  print gap positions only. -t is deactived in default.  </br>
 
**-n N** divide into chunks containing N# of sequences </br>

**-N N** divide sequences into N chunks </br>

**-M N** divide sequences into chunks ~N MB (million bp) in size </br>

**-o file:** output file\n

---
</details>

<details>
  <summary> extract </summary>
  
**-I N** extract sequence from N bp to the end (1-based), works with a single fasta entry. </br>

**-E N** extract sequence up to to N bp (1-based), works with a single fasta entry
   use '-I N -E M' to extract sequence from 'N to N+M' bp (inclusive) </br>
   
 **-F N** extract the first N fasta entries </br>
 
 **-B N** extract from the Nth entry to the last entry</br>
 
 use '-B N -F M' to extract sequences from N to N+M (inclusive) </br>
 
These utilities were designed to work with single long sequences, even though it will with multiple sequences but with the same applying to all sequences. </br>
     
 ---
</details>

<details>
  <summary> report </summary>
  
  **-f** print fasta as in the original </br>
  
  **-F** print fasta with sequence in one line </br>
  
  **-nd** print sequence without define </br>
  
  **-d** print short defline before the first space </br>
  
  **-D** print the original defline </br>
  
  **-c** count the number of fasta entries </br>
  
  **-l** print short defline\tlength </br>
  
  **-L** print original defline\tlength </br>
  
  **-s** report sequence summary statistics including N50 </br>
  
  **-S** report sequence summary statistics plus detailed gap info </br>
  
  **-rc** print sequence in revevrse compliment </br>
  
  **-N** convert all non-ACGT letters to N </br>
  
  **-r** remove all non-ACGT letters
  
  ---
</details>

<details>
  <summary> filter </summary>
  
 **-g N** ignore sequences with N or more Ns by printing to STDERR. </br>
 
**-r 1/2** 1: remove redundant entry based on ID, 2: keep redudant entries by adding 
 a serial number to the identical IDs.</br>
 
**-R 1/N** identify and remove identical entries based on sequence by printing redundant entries to STDERR. 
  1: use complete sequence; N: only the first and last N bases. </br>
 
**-l N** print sequences with a minimal length of N bp </br>

**-L N** print sequences with a maximal length of N bp</br>

<p>use -l N -L M for printing sequences with length from N to M bp</p>

---
</details>

Typing 'fatools subcommand' displays the help menu for the subcommand with a list of specific utilities and options.

Making fatools executable and available from any directory
------
#### Linux
1. Open the terminal and navigate to where you have downloaded fatools
2. Find where python2 is installed in your system `which python`. Usually you would get something like /usr/bin/python
3. Copy this output to the beggining of fatools as `#!/usr/bin/python`.
4. Run the following command to make the script executable `chmod +x fatools`
5. Add fatools to your bin directory or anyother directories included in your $PATH
6. You should be able to run fatools from anywhere!

#### Windows
1. Type 'control panel' in the Windows search bar
2. Go to System and Security > System > Advanced System settings > Environment variables
3. Under system variables, select 'Path' and click 'Edit' and then 'New'
4. Add the path of where you have fatools located and click OK
5. You should be able to run fatools from anywhere!



Examples
------
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

`Total 2,868 bps from qualified 5 sequences (5 total); length average: 573 (210-1262) bp; N50: 698 bp`

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

fatools extract -F3 sequenceTesting2.txt  | fatools report -l -

```
AY211956.1Macropus(BRCA1)gene,partialcds       698 
NP_001245510.1  1262 
DY343456.1      210
``` 

Or extract the sequences from a large sequence set for a list of IDs and then search sequences with a specific sequence by using

fatools search -F IDlist.txt exampleFasta.fa |fatools -S AAATAAA -
