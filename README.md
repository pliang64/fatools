# fatools
A headless python tool package for working with fasta sequences

The script is written in python 2, so you will need to use python 2 to run the script.
Simply download the repository, navigate to the folder where fatools.py is located. To use the tools you have to start any command with 
`python fatools.py`. If you want to run the script without having to type 'python' or run it from any directory in your terminal, follow the instructions under **Make fatools available from any directory**.

### List of commands
* search
* split
* extract
* report
* filter

To explore the different tools under a specific command simply type
`python fatools.py command` and a list of all the tools available under that command will be displayed.

### Make fatools available from any directory
---
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


### Examples
---
Navigate to the exampleFiles directory in this repository. In there, a fasta file (exampleFasta.fa) and a file containing a list of IDs (IDlist.txt) from exampleFasta.fa.

To extract the fasta sequences from exampleFasta.fasta based on the list of IDs:</br>
`fatools.py search -F IDlist.txt exampleFasta.fa` </br>
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
`fatools.py report -s exampleFasta.fa`</br>
Which yields:</br>
`Total 2,868 bps from qualified 5 sequences (5 total); length average: 573 (210-1262) bp; N50: 698 bp`


