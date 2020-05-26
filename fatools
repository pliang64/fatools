# Rodrigo Vega 2020
# adapted from Ping Liang's perl version of the fatools 2019
import getopt
import sys
import re
# import only necessary when running with python 2.7 or lower
from string import maketrans

argv = sys.argv[2:]
# count
cnt = -1
# to break from fatools def
brk = False
# for usage in chunk command
fa_list = []
tlen, seqnum, gap10, gap100, gap500, gap1k, gap10k, gapTotal, gapseq, field, redntID, gapPattern = 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0
delimeter, argSs, optN_n_M, opt1 = False, False, False, False


id_del_set = set()
IDlen = 0

fasta = ''
n50raw = []
seenID = []
seenIDd = {}
commands = {'search', 'split', 'filter', 'report', 'extract'}


def get_options(command):
    """ Gets the options for command passed as argument """
    cmmnd = False
    if command == 'search':
        print("\nUsage: " + sys.argv[0] + """ search [options] inputfasta | - (stdin)
        search options: 
        -s  string: search for entries containing "string" in the sequence
        -d  string: search for entries containing "string" in the defline: Default is for exact match, use
            "/string" for flexibility                    
        -F  file: search for sequences for a list of IDs in the file. 
            Can use -D to specify delimiter, default is space or '|' or end of line; 
            use -i to specify the field number, default is 1.
        -1  find single match for -d and -s
        -v  use with -s, -d or -f to negate the search\n
         """)
    elif command == 'split':
        print("\nUsage: " + sys.argv[0] + """ split [options] inputfasta | - (stdin)  
        split options:
        -G N split the sequences as non-gap framments, N is the number of consecutive Ns, default 1
         Use -G with -t to  print gap positions only, default 0 
        -n N divide into chunks containing N# of sequences 
        -N N divide sequences into N chunks
        -M N divide sequences into chunks ~N MB (million bp) in size
        -o "file" output file\n
        """)
    elif command == 'extract':
        print("\nUsage: " + sys.argv[0] + """ extract [options] inputfasta | - (stdin)
        extract options:
        -I N extract sequence from N bp to the end (1-based), works with a single fasta entry
        -E N extract sequence up to to N bp (1-based), works with a single fasta entry
            use '-I N -E M' to extract sequence from 'N to N+M' bp (inclusive)
        -F N extract the first N fasta entries
        -B N extract from the Nth entry to the last entry
           use '-B N -F M' to extract sequences from N to N+M (inclusive)
        Options can be used with -R to return reverse compliment\n
        """)
    elif command == 'report':
        print("\nUsage: " + sys.argv[0] + """ report [options] inputfasta | - (stdin)
        report options:
        -f print fasta as in the original
        -F print fasta with sequence in one line
        -n print sequence without define
        -d print short defline before the first space
        -D print the original defline     
        -c count the number of fasta entries
        -l print short defline\\tlength
        -L print original defline\\tlength
        -s report sequence summary statistics including N50
        -S report sequence summary statistics plus detailed gap info
        -v take revevrse compliment
        -t turn all non-ACGT letters to N
        -m remove all non-ACGT\n
        """)
    elif command == 'filter':
        print("\nUsage: " + sys.argv[0] + """ filter [options] inputfasta | - (stdin)
        filter options:
        -g N ignore sequences with N or more Ns (print to STDERR)
        -r 1/2 1: remove redundant entry based on ID, 2: keep redudant entries by adding 
         a random sting to the identical IDs)
        -R 1/N identify and remove identical entries (redudant entries in STDERR and NR entries on STDOUT) 
         by sequences based on 1: complete sequence; N: only the first and last N bases
        -l N sequence minimal length
        -L N sequence maximal length\n
        """)
    else:
        cmmnd = True
    return cmmnd


justCommands = "\nUsage: " + sys.argv[0] + """ command option(s) inputfasta | - (stdin)
 commands:
    search
    split
    extract
    report
    filter
    """

try:
    opts, args = getopt.getopt(argv, 'd:D:W:s:S:n:N:M:o:G:F:B:I:i:E:p:l:L:r:R:g:tvftmhc1')
    for o, a in opts:
        if o == '-h':
            print(justCommands)
            sys.exit()
except:
    print(justCommands)
    sys.exit()

# check for validity of first argument
try:
    if sys.argv[1] not in commands:
        print(justCommands)
        sys.exit()
# no argument was provided
except IndexError:
    print(justCommands)
    sys.exit()
# check for validity of second argument
try:
    sys.argv[2]
# print options if argument is not valid
except:
    get_options(sys.argv[1])
    sys.exit(2)

def read_file_bytes(file, option):
    """ Reads a file in bytes that containing entries and sends an individual entry to
    readFile().
    """
    last_seq_chunk = ''
    parent_of_orphan_chunk = ''
    data = file.read(50000000)
    bytes_read = len(data)
    bytes_tobe_read = 50000000
    sequences = ''
    fasta = ''
    seq = ''
    while data:
        sequences = data.split('\n>')
        generator = (fasta for fasta in sequences)
        first_chunk = True
        for fasta in generator:
            last_seq_chunk = readFile(fasta, sequences, last_seq_chunk, first_chunk, False, option)
            # we want to keep the parent of a chunk that might end up being read in different chunks
            if bytes_tobe_read <= bytes_read:
                parent_of_orphan_chunk = last_seq_chunk
            seq = fasta
            first_chunk = False
        data = file.read(50000000)
        bytes_read = bytes_read + len(data)
        bytes_tobe_read = bytes_tobe_read + 50000000

    if len(sequences) == 1:
        last_seq_chunk = parent_of_orphan_chunk
    if last_seq_chunk != '' or (not last_seq_chunk.isspace()):
        sequences = [None]
        if last_seq_chunk == fasta:
            last_seq_chunk = ''
        readFile(seq, sequences, last_seq_chunk, True, True, option)


def readFile(seq, sequences, last_seq_chunk, first_chunk, is_last_seq, option):
    """Makes sure the entries read in bytes are put together correctly"""
    # if last sequence in chunk
    if seq == sequences[-1]:
        last_seq_chunk = seq
        return last_seq_chunk
    if first_chunk:
        if last_seq_chunk != '' or (not last_seq_chunk.isspace()):
            seq = "{}{}".format(last_seq_chunk, seq)

    last_seq_chunk = ''
    if option[0] == 1:
        fatools(seq, is_last_seq)
    else:
        searchByID(seq, option[1], option[2])
    return last_seq_chunk


def reversecom(sequence):
    """ Returns the reverse compliment of a sequence """
    translation = maketrans('ACTGactg', 'TGACtgac')
    # take the compliment of the sequence
    sequence = sequence.translate(translation)
    # reverse the sequence
    sequence = sequence[::-1]
    return (sequence)


def acgtton(sequence):
    """ Changes all non-ACTG characters to N's and returns the sequence """
    sequence.strip(r'\W')
    sequence = re.sub(r'[^ACTGactg]', 'N', sequence)
    return sequence


def ignoren(sequence):
    """ Searches the sequence for the number of Ns specified and returns true if found and prints
    sequence to stderr """
    optg = False
    global opts
    global fasta
    s = False
    for o, a in opts:
        if o == '-g':
            optg = True
            match_g = re.search(r'[Nn]{' + a + ',}', sequence)
    if optg:
        if match_g:
            print >> sys.stderr, fasta  # , file = sys.stderr)
            s = True
    else:
        get_options(sys.argv[1])
        sys.exit()
    return s


def extractFromSeq(I, Ia, E, Ea, seq_len, seq_old, defn_old, rvrs):
    """Extracts the specified number of base pairs in a sequence"""
    if I and E:
        if int(Ia) > int(Ea):
            print("Error: Argument for -I can't be greater than argument for -E")
            sys.exit()
        seq = seq_old[int(Ia) - 1:int(Ea)]
        defn = defn_old + ':' + Ia + ':' + Ea
    elif I:
        seq = seq_old[int(Ia) - 1:]
        defn = defn_old + ':' + Ia + ':' + str(seq_len)
    elif E:
        seq = seq_old[:int(Ea)]
        defn = defn_old + ':1' + ':' + Ea

    if I or E:
        if rvrs:  # reverse compliment
            seq = reversecom(seq)
        print(defn + '\n' + seq)


def countGaps(gap):
    global gapTotal, gap10, gap100, gap500, gap1k, gap10k
    gaplen = len(gap.group())
    gapTotal += gaplen
    if gaplen <= 10:
        gap10 += 1
    elif gaplen <= 100:
        gap100 += 1
    elif gaplen <= 500:
        gap500 += 1
    elif gaplen <= 1000:
        gap1k += 1
    else:
        gap10k += 1


def fatools(record, lastEntry):
    """Tools to manipulate FASTA sequences"""
    global brk, redntID, tlen, seqnum, optN_n_M, argSs, cnt, gapTotal, gapseq, opt1
    global gap10, gap100, gap500, gap1k, gap10k
    cnt = cnt + 1

    next = False
    if brk:
        sys.exit(0)

    fasta = record
    lines = fasta.split('\n', 1)

    try:
        defn = ">{}".format(lines[0].strip('>'))
        defn_old = defn  # copy of old defn
        seqf = lines[1]
    except(IndexError):
        # Empty Sequence!
        return

    seq = lines[1].strip('\t\n\r').replace('\n', '')
    seq_old = seq  # copy of old seq
    seq_len = len(seq)

    if sys.argv[1] == 'search':
        next = False
        negate, opt_d, opt_s = False, False, False
        for o, a in opts:
            if o == '-v':
                negate = True
            if o == '-d':
                opt_d = True
                if "/" in a:
                    look_for_str = a.strip('/').strip()
                else:
                    look_for_str = r'\b' + a + r'\b'
            if o == '-s':
                opt_s = True
                look_for_str = a

        if not opt_d and not opt_s and not opt1:
            get_options(sys.argv[1])
            sys.exit()
        if o == '-1':
            opt1 = True
        if (opt_d and negate and re.search(look_for_str, defn, re.IGNORECASE)) or (opt_s and negate and re.search(look_for_str, seq, re.IGNORECASE)):
            next = True
        elif (opt_d and not re.search(look_for_str, defn, re.IGNORECASE) and not negate) or (
                opt_s and not re.search(look_for_str, seq, re.IGNORECASE) and not negate):
            next = True

        if next:
            return
        print("{}\n{}".format(defn, seqf))
        if opt1:
            brk = True

    if sys.argv[1] == 'split':
        optt = False
        optG = False
        optN_n_M = False
        sectn = 1
        sectn_s = 0
        sectnlgth_temp = 0
        for o, a in opts:
            # divide into chunks options
            if (o == '-N') or (o == "-n") or (o == "-M"):
                fa_dict = {'D': defn, 'S': seq}
                fa_list.append(fa_dict)
                optN_n_M = True
                break
            # split as non-gap fragments option
            elif o == '-G':
                optG = True
                num = a
            # print gap position only
            elif o == '-t':
                optt = True
        if not optN_n_M and not optt and not optG:
            get_options(sys.argv[1])
            sys.exit()
        if optN_n_M and not lastEntry:
            return

        if optG:
            match_g = re.search(r'[Nn]{' + num + ',}', seq)
            if match_g:
                match_g = re.finditer(r'[Nn]{' + num + ',}', seq)
                for gap in match_g:
                    s = gap.start() + 1
                    e = gap.end()
                    l = len(gap.group())
                    # print gap positions only
                    if optt:
                        print(defn, '\t', s, '\t', e)
                    else:
                        nongap = re.split(r'[Nn]{' + num + ',}', seq)
                        length = len(nongap)
                        sectnlgth = len(nongap[sectn - 1])
                        sectnlgth_temp = sectnlgth
                        sectn_e = sectn_s + sectnlgth_temp
                        print(defn, '|', sectn, '/', length, '|', sectn_s, ':', sectn_e, '|gap|', l, '|', s, ':', e)
                        print(nongap[sectn - 1])
                        sectn += 1
                        sectn_s += sectnlgth + l
                if not optt:
                    sectnlgth = len(nongap[sectn - 1])
                    sectnlgth_temp += sectnlgth
                    sectn_e = sectn_s + sectnlgth_temp
                    print(defn, '|', sectn, '/', length, '|', sectn_s, ':', sectn_e, '|gap|-1')
                    print(nongap[sectn - 1])
            # print the original if no gap
            else:
                print("{}\n{}".format(defn, seqf))
            return
    if sys.argv[1] == 'extract':
        optf = False
        optb = False
        next = False
        rvrs = False
        for o, a in opts:
            if o == '-R':
                rvrs = True
            if o == '-t':
                seqf = acgtton(seqf)
            if o == '-F':  # extract the first N sequence entries
                optf = True
                if cnt + 1 > int(a):
                    brk = True
                    next = True
                    break
            elif o == '-B':  # extract from the Nth entry to the last
                optb = True
                if cnt + 1 < int(a):
                    next = True
                    break

        I = False  # sequence part ###
        E = False
        # optI = False
        # optE = False
        optg = False
        Ia, Ea = '', ''
        for o, a in opts:
            # extract sequence from N position, 1 based
            if o == '-I':
                I = True
                Ia = a
            # extract sequence up to to N position, 1 based
            if o == '-E':
                E = True
                Ea = a

        if optf or optb:
            # reverse compliment
            if rvrs:
                seqf = reversecom(seqf)
            if next:
                return  # continue
            elif I or E:
                pass
            else:
                print(defn + '\n' + seqf + '\n')
        extractFromSeq(I, Ia, E, Ea, seq_len, seq_old, defn_old, rvrs)

        if not optf and not optb and not I and not E:
            get_options(sys.argv[1])
            sys.exit()

    if sys.argv[1] == 'report':
        tlen += seq_len
        seqnum += 1
        n50raw.append(seq_len)
        optl, optL, optdD, optfF, optnd, optC = False, False, False, False, False, False
        validOpt = False
        global gapPattern
        for o, a in opts:
            if o == '-c':
                optC = True
                validOpt = True
                break
            # summary
            if (o == '-S') or (o == '-s'):
                if argSs == False:
                    gapPattern = re.compile(r'[Nn]{1,}')
                argSs = True
                validOpt = True
            # summary with gap info
            if o == '-S':
                if 'N' in seq or 'n' in seq:
                    match_su = gapPattern.finditer(seq)
                    gapseq += 1
                    [countGaps(gaps) for gaps in match_su]

            if o == '-l':
                optl = True
                validOpt = True
            if o == '-L':
                optL = True
                validOpt = True

            if o == '-f':
                print("{}\n{}".format(defn, seqf))
                optfF = True
                validOpt = True
            elif o == '-F':
                print(defn + '\n' + seq + '\n')
                optfF = True
                validOpt = True
            elif o == '-n':
                print(seqf + '\n')
                optnd = True
                validOpt = True

            if o == '-d':
                optdD = True
                defn = defn.strip('>')
                defn = re.search(r'[^\s]*', defn)
                defn = defn.group()
                validOpt = True

            elif o == '-D':
                optdD = True
                defn = defn.strip('>')
                validOpt = True

            # turn all non-ACTG letters to N
            if o == '-t':
                seq.strip(r'\W')
                seq = acgtton(seq)
                print(defn + '\n' + seq)
                validOpt = True

            # Remove all non ACTG letters
            elif o == '-m':
                seq.strip(r'\W')
                seq = re.sub(r'[^ACTGactg]', '', seq)
                print(defn + '\n' + seq)
                validOpt = True

            # reverse compliment of sequence
            if o == '-v':
                seq = reversecom(seq)
                print(defn + '\n' + seq)
                validOpt = True

        if optC and lastEntry:
            print("{} fastq entries found".format(format(seqnum, ",d")))
        if optl or optL:
            if optl:
                defn = defn.strip('>')
                defn = re.search(r'[^\s]*', defn)
                defn = defn.group()
            elif optL:
                defn = defn.strip('>')
            # print(defn + '\t' + str(seq_len))
            print("{}\t{}").format(defn, seq_len)
        if optdD:
            print(defn)
        if not validOpt:
            get_options(sys.argv[1])
            sys.exit()

    if sys.argv[1] == 'filter':
        defn = re.search(r'[^\s]*', defn)
        defn = defn.group()
        optr1 = False
        optr2 = False
        optR1 = False
        optR2 = False
        optl = False
        optL = False
        optg = False
        for o, a in opts:
            if o == '-r' and a == '1':  # remove redundant entry
                optr1 = True
            elif o == '-r' and a == '2':  # keep redundant entry by adding a random string
                optr2 = True
            elif o == '-R':
                if a == '1':  # remove redundant entries based on sequence
                    optR1 = True
                elif int(a) > 1:  # remove redundant entries based on the first and last N bases
                    optR2 = True
                    num = a
            if o == '-l':  # sequence minimal length
                optl = True
                minimal = int(a)
            elif o == '-L':  # sequence maximal length
                optL = True
                maximum = int(a)
            if o == '-g':  # ignore sequences with N or more Ns
                optg = True
        if not optr1 and not optr2 and not optR1 and not optR2 and not optl and not optL and not optg:
            get_options(sys.argv[1])
            sys.exit()
        if optg:
            if ignoren(seq):
                return
            else:
                print("{}\n{}".format(defn, seqf))
        if optl or optL:
            if optl and seq_len < minimal:
                return
            elif optL and seq_len > maximum:
                return
            print("{}\n{}".format(defn, seqf))
        if optr1 or optr2 or optR1 or optR2:
            if optr2:
                if defn not in seenID:
                    seenID.append(defn)
                    print(defn + '\n' + seqf)
                    return
                else:
                    redntID += 1
                    while defn in seenID:
                        defn = defn + '.' + str(redntID)
                    seenID.append(defn)
                    print(defn + '\n' + seqf)
                    return
            elif optr1:
                if defn in seenID:
                    return
                else:
                    seenID.append(defn)
                    print("{}\n{}".format(defn, seqf))
                    return
            elif optR1:
                key = seq
            elif optR2:
                left = seq[:int(num)]
                last = seq[-int(num):]
                key = left + last
            if key in seenIDd.keys():
                if seenIDd[key] == seq_len:
                    print >> sys.stderr, "{}\n{}".format(defn, seqf)
                    return
                else:
                    seenIDd[key] = seq_len
            else:
                print("{}\n{}".format(defn, seqf))
                seenIDd[key] = seq_len

    # operations that depend on the information collected from the previous sequences
    if lastEntry:
        # divide into chunks options
        if optN_n_M:
            outfile = ''
            optM = False
            for o, a in opts:
                if o == '-n':
                    chunksize = int(a)
                elif o == '-N':
                    chunknum = a
                    chunksize = int((len(fa_list) - 1) / int(chunknum))
                # divide into chunks of ~(chunksize)MB
                elif o == '-M':
                    optM = True
                    chunksize = int(a)
                if o == '-o':
                    outfile = a
            outfile = outfile + '_sub'
            count, n = 0, 1
            chunkfile = open(outfile + str(n) + '.fa', 'w')
            while (fa_list):
                s = fa_list.pop(0)
                if optM:
                    # sequence length in MB
                    count += len(s['S'])/1048576.0
                else:
                    count += 1
                if count > chunksize:
                    n += 1
                    count = 0
                    chunkfile.close()
                    chunkfile = open(outfile + str(n) + '.fa', 'w')
                chunkfile.write(s['D'] + "\n" + s['S'])
                chunkfile.write('\n')
            chunkfile.close()
        # summary
        if argSs:
            Tlen = 0
            avg = int(tlen / seqnum)
            # sorting lengths from smallest to largest
            n50raw.sort()
            max = n50raw[-1]
            min = n50raw[0]
            for lenn in n50raw:
                Tlen += lenn
                if Tlen >= tlen / 2:
                    N50s = lenn
                    break
            tmpall = cnt + 1
            mssg = '\nTotal {} bps from qualified {} sequences ({} total); length average: {} ({}-{}) bp; N50: {} bp'.format(
                format(tlen, ",d"), format(seqnum, ",d"), tmpall, avg, min, max, format(N50s, ",d"))
            print(mssg)
            # sys.stderr.write(mssg)
            for o, a in opts:
                if o == '-S':
                    non_gap = tlen - gapTotal
                    print(
                        'Gap info: {} sequences with gaps for a total of {} estimated bps; non-gap: {} bps; gap size: <11bp: {}; <101bp: {}; 500bp: {}; <1kp: {}; >1kp: {}.'.format(
                            gapseq, format(gapTotal, ",d"), format(non_gap, ",d"), gap10, gap100, gap500, gap1k, gap10k))
                    break


def searchByID(entry, IDset, search_negate):
    """Searches for entries based on a list of IDs"""
    global id_del_set, IDlen
    delim_opt = False
    delimeter = ''
    IDtoSearch = None
    field = 0

    entry = entry.split('\n', 1)

    try:
        defline = ">{}".format(entry[0].strip('>'))
        # copy of old defn
        defline_old = defline
        seq = entry[1]
    except(IndexError):
        # Empty Sequence!
        return

    for o, a in opts:
        if o == '-D':
            delim_opt = True
            delimeter = a
        if o == '-i':
            field = int(a) - 1
            pass

    # if delimeter was specified
    if delim_opt:
        try:
            if delimeter in defline:
                IDtoSearch = defline.split(delimeter)[field].strip('>').strip()
        except:
            pass

    else:
        IDtoSearch = defline.strip().strip('>')
        if "|" in IDtoSearch:
            IDtoSearch = IDtoSearch.split("|")[field]
        elif " " in IDtoSearch:
            IDtoSearch = IDtoSearch.split(" ")[field]

    if IDtoSearch in IDset:
        if not search_negate:
            print(defline_old + '\n' + seq)
    else:
        if search_negate:
            print(defline_old + '\n' + seq)


if __name__ == "__main__":
    searchf = False
    search_negate = False
    if sys.argv[1] == 'search':
        # search and print fasta sequences based on an ID list
        for o, a in opts:
            # extract fasta sequences based on ID file
            if o == '-F':
                searchf = True
                database = sys.argv[-1]
                # list of ID's
                list_ID = a
            if o == '-v':
                search_negate = True

    if searchf:
        IDset = set()
        with open(list_ID, 'r') as IDs:
            for line in IDs:
                IDlen = IDlen + 1
                IDset.add(line.strip())

        if database == "-":
            d = sys.stdin
            read_file_bytes(d, [2, IDset, search_negate])
        else:
            try:
                with open(database, 'rb') as d:
                    read_file_bytes(d, [2, IDset, search_negate])
            except IOError:
                print('{} is not a file'.format(database))
    else:
        if sys.argv[-1] == "-":
            # read input from stdin
            pipe_input = sys.stdin
            read_file_bytes(pipe_input, [1])
        else:
            # read file in byte chunks
            try:
                with open(sys.argv[-1], 'rb') as f:
                    read_file_bytes(f, [1])
            except IOError:
                print('{} is not a file'.format(sys.argv[-1]))

