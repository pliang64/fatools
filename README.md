# fatools
A headless python tool package for working with fasta sequences

The script is written in python 2, so you will need to use python 2 to run the script.
Simply download the repository, navigate to the folder where fatools.py is located. To use the tools you have to start any command with 
`python fatools.py`. If you want to run the script without having to type 'python' or run it from any directory in your terminal, follow the instructions under **Make fatools available from any directory**.

### List of commands
<details>
  <summary> search </summary>
  
  **-s  string:** search for entries containing "string" in the sequence </br>
  
  **-d  string:** search for entries containing "string" in the defline: Default is for exact match, use "/string" for flexibility </br>     
  
  **-F  file:** search for sequences for a list of IDs in the file.
      Can use -D to specify delimiter, default is space or '|' or end of line. </br>
      use -i to specify the field number, default is 1. </br>
      
  **-1**  find single match for -d and -s </br>
  
  **-v**  use with -s, -d or -f to negate the search
  
  ---
</details>

<details>
  <summary> split </summary> 
  
 **-G N** split the sequences as non-gap framments, N is the number of consecutive Ns, default 1
 Use -G with -t to  print gap positions only, default 0 </br>
 
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
 
 **-B N** extract from the Nth entry to the last entry
     use '-B N -F M' to extract sequences from N to N+M (inclusive) </br> 
     
 ---
</details>

<details>
  <summary> report </summary>
  
  **-f** print fasta as in the original </br>
  
  **-F** print fasta with sequence in one line </br>
  
  **-n** print sequence without define </br>
  
  **-d** print short defline before the first space </br>
  
  **-D** print the original defline </br>
  
  **-c** count the number of fasta entries </br>
  
  **-l** print short defline\tlength </br>
  
  **-L** print original defline\tlength </br>
  
  **-s** report sequence summary statistics including N50 </br>
  
  **-S** report sequence summary statistics plus detailed gap info </br>
  
  **-v** take revevrse compliment </br>
  
  **-t** turn all non-ACGT letters to N </br>
  
  **-m** remove all non-ACGT letters
  
  ---
</details>

<details>
  <summary> filter </summary>
  
 **-g N** ignore sequences with N or more Ns (print to STDERR) </br>
 
**-r 1/2 1:** remove redundant entry based on ID, 2: keep redudant entries by adding 
 a a number to the identical IDs) </br>
 
**-R 1/N** identify and remove identical entries (redudant entries in STDERR and NR entries on STDOUT) 
 by sequences based on 1: complete sequence; N: only the first and last N bases </br>
 
**-l N** sequence minimal length </br>

**-L N** sequence maximal length </br>

---
</details>

To explore the different tools under a specific command in the terminal simply type
`python fatools.py command` and a list of all the tools available under that command will be displayed.

Make fatools available from any directory
------
#### Linux
1. Open the terminal and navigate to where you have downloaded fatools.py
2. Find where python2 is installed in your system `which python`. Usually you would get something like /usr/bin/python
3. Copy this output to the beggining of fatools.py as `#!/usr/bin/python`.
4. Run the following command to make the script executable `chmod +x fatools.py`
5. Add fatools.py to your bin directory
6. You should be able to run fatools.py from anywhere!

#### Windows
1. Type 'control panel' in the Windows search bar
2. Go to System > Advanced System settings > Environment variables
3. Under system variables, selecth 'Path' and click 'Edit' and then 'New'
4. Add the path of where you have fatools.py located and click OK
5. You should be able to run fatools.py from anywhere!


Examples
------
Navigate to the exampleFiles directory in this repository. In there, a fasta file (exampleFasta.fa) and a file containing a list of IDs (IDlist.txt) from exampleFasta.fa.

To extract the fasta sequences from exampleFasta.fasta based on the list of IDs:</br>
`fatools.py search -F IDlist.txt exampleFasta.fa` </br>

<code>>NP_001245510.1 notch, isoform B [Drosophila melanogaster]
NNMQSQRSRRRSRAPNTWICFWINKMHAVASLPASLPLLLLTLAFANLPNTVRGTDTALVAASCTSVGCQNG
GTCVTQLNGKTYCACDSHYVGDYCEHRNPCNSMRCQNGGTCQVTFRNGRPGISCKCPLGFDESLCEIAVP
NACDHVTCLNGGTCQLKTLEEYTCACANGYTGERCETKNLCASSPCRNGATCTALAGSSSFTCSCPPGFT... </code>

<code>>DY343456.1 Macropus rufus BRCA1 (BRCA1) gene, partial cds
CAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAGTCTGGATGAAAGTAAGGAAATATGTAGTGCTGGA
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGTAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCA </code>

<code>>FY343456.1 Macropus rufus BRCA1 (BRCA1) gene, partial cds
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGCAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAG
TCTGGATGAAAGTAAGGAAATATGTAGTGCTGGAAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCAT </code>

To report summary statistics:</br>
`fatools.py report -s exampleFasta.fa`</br>

`Total 2,868 bps from qualified 5 sequences (5 total); length average: 573 (210-1262) bp; N50: 698 bp`

To get fasta sequences with a maximum length

`python fatools.py filter -L250 exampleFasta.fa` </br>

<code>>DY343456.1
CAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAGTCTGGATGAAAGTAAGGAAATATGTAGTGCTGGA
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGTAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCA </code>

<code>>FY343456.1
TGTGGCACAGATGCTCGTGCCACCTCATTACTTCCTGAAACCACCAGCTTATCGCCCAACACAGACCGAA
TGAATGTAGAAAAGGCTGAACTCTGTAATAAAAGCAAACAGCCTGGCTTAGCAAAAAACCAACAGAGCAG
TCTGGATGAAAGTAAGGAAATATGTAGTGCTGGAAAGACCCTGGGTGCCCATGAGCTGAATGCCCATCAT </code>



