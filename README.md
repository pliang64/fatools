# fatools
a headless python tool package for working with fasta sequences

The script is written in python 2, so you will need to use python 2 to run the script.
Simply download the repository, navigate to the folder where fatools.py is located. To use the tools you have to start any command with 
`python fatools.py`

### List of commands
* search
* split
* extract
* report
* filter

To explore the different tools under a specific command simply type
`python fatools.py command` and a list of all the tools available will be displayed.

### Examples
Navigate to the exampleFiles directory in this repository. In there, a fasta file (exampleFasta.fa) and a file containing a list of IDs (IDlist.txt) from exampleFasta.fa.

To extract the fasta sequences from exampleFasta.fasta based on the list of IDs:</br>
`python fatools.py search -F IDlist.txt exampleFasta.fa` </br>
Output: </br>
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
`python fatools.py report -s exampleFasta.fa`</br>
Which yields:</br>
`Total 2,868 bps from qualified 5 sequences (5 total); length average: 573 (210-1262) bp; N50: 698 bp`


