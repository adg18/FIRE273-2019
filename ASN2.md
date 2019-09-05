*Note: This assignment has a number of associated questions you'll find as you go through this that need to be submitted on ELMS. Some of these ask you to explain or guess why certain things may be the way that they are. I am not grading on correctness here, rather I am grading based on whether you have completed the assignment and put some thought into the answers. Feel free to discuss these with your group or with me in lab. We will likely go over some of the more important material during class as well.

# 1-i. Connecting to our computing resources.

## Connecting to the command line

Since the datasets we will be using are quite large (entire experiments can take up almost a terabyte of space uncompressed) substantial parts of the analysis cannot easily be done on personal computers. We have a computer set up in the lab with the software we will need pre-installed with plenty of storage space. 

For the majority of the work, we will be connected to the command line interface remotely. We will be using a method called SSH or Secure SHell to do so. If you already have opinions or preferences about what software to use for this, feel free to use whichever method you like. 

For simplicity, I recommend downloading a free program called Termius https://termius.com/ to connect. 

To get started connecting to our server, after installing you should go to the "Hosts" menu and choose "+ NEW HOST". Once you do that you can fill in the Label, Address, and Username fields. Your username will be your university Directory ID. Unfortunately, we could not get integration with the university systems, so your password will not match. I've set your password to be your UID number. Since this is predictable, we'll have you change this immediately. This also happens to be the first grade point available for ASN2!

![Termius host menu](Images/Termius1.png)

Finally, click the save button and the connection will be stored in the list of Hosts. Double click on the TOD-Compute option and Termius should prompt you for your password (currently your UID). Enter that and you should be connected! If it shows a prompt mentioning that the host key is new you can accept this, this should only happen the first time you connect and the program will remember these details later.

![Termius connection](Images/Termius2.png)

Once you are connected, the first thing you should do is change your password. This is as simple as running one command:

```bash

passwd

```

Running `passwd` will prompt your for your current password, then ask you to enter your new password twice. For security, this program doesn't show your your password or even asterisks as you are typing it, so don't worry when nothing shows up.

Successfully connecting to `tod-compute` and changing your account password so that I cannot login with your old password gets 1/4 points for ASN2. If you would like you can go back and edit the TOD-Compute host entry in Termius to save your password so that you do not need to type it in every time you connect.

Some notes: This machine has a fast 1 TB solid-state drive and a slower network-connected 8 TB shared drive. Be default any files you download or create onto this machine will be on the SSD. Since we are going to be working with huge datasets at times if everyone tries to store everything on the main disk we will rapidly run out of space. 

![Storage folder](Images/Storage.png)

When you log in you should already have one folder created in your home directory named `storage`. This is a link to a folder on the large shared drive which you can store large files in. More on this later on!

At some points we will also want to be able to transfer files to and from out personal computers and the server. Unfortunately, I am not a fan of any of the free programs that will work on all computers. I will suggest you downloading and install WinSCP if your personal computer runs Windows (https://winscp.net/eng/download.php) or Fetch if it runs macOS (https://fetchsoftworks.com/fetch/download/). WinSCP is free to use, but Fetch requires a license. Fortunately this is free for Academic purposes, so once you get Fetch installed you should apply for the free license (https://fetchsoftworks.com/fetch/free).

### Connecting with WinSCP

Connecting with either program in similar to connecting with Termius. When you open WinSCP it will automatically open the Login window and default the connection method to "SFTP" which we will be using.

![WinSCP connection](Images/WinSCP1.png)

You can save this connection in the same way. Once you have this all set up, you can hit "Login" at the bottom. This again may warn you about the host key, go ahead and accept that again in this program.

This will open up a two-panel window similar to Windows Explorer, with your computer/files on the left side and the remote server files on the right side. Transfering files is as simple and clicking and dragging where you would like them to go.

### Connecting with Fetch

Connecting with Fetch should be nearly identical, on first run the program will open up the New Connection window where you can put the hostname and username. Fetch defaults to the "FTP" connection mode which is insecure and not enable on our server. Simply use the dropdown and select "SFTP" and connect. Unlike WinSCP Fetch has only a single side showing the remote server files. To transfer files to or from the server you can click and drag from a Finder window.

![Fetch connection](Images/Fetch.png)

At this point we should be set up with everything we need to use the computing resources (at least until we get to differential expression analysis and visualization in a few weeks!)

# 1-ii. Understanding our reference genome

To begin, we want to get more familiar with the genome and transcriptome we will be studying initially. In our case, this main genome is the human reference genome. We will be using the most recent version, version 38 from NCBI Ensembl (GRCh38). We will be interacting with the data in a few ways.

- Directly on the raw sequence data at the whole-genome, chromosome, or individual transcript levels
- Using annotated versions of the genomic data where genes, regulatory elements, or other features are listed by location
- With a web-based interacting database allowing us to search and explore a wide range of database resources from one location

To save some time and space during these steps, you'll be using only one chromosome at a time for this exercise, chromosome 22.

## Reference genome sequences

The web-based database for the genome assembly is available at the Ensembl website (https://useast.ensembl.org/Homo_sapiens/Info/Index). Another resource for viewing this information is available on the UCSC website and GRCh38/hg38 (https://genome.ucsc.edu/cgi-bin/hgGateway).

The most current human genome sequences are available on the EMBL-EBI FTP (ftp://ftp.ensembl.org/pub/release-86/fasta/homo_sapiens/dna/). These are compressed FASTA files containing raw assembled genome sequence. There are three variants of the files, one that is labeled "dna" and contains the full sequence. The other two are labeled "dna_rm" and "dna_sm". These are the same sequence, but with some repetive or duplicated elements removed or "masked" out. We will be using the main "dna" files.

First, we should make a directory to do our work for this assignment in. You can name this whatever you like or put it wherever you like.

```bash

cd ~
mkdir ASN2
cd ASN2

```

We can now download the sequence for chromosome 22 directly from the EMBL-EBI FTP.

```bash

wget ftp://ftp.ensembl.org/pub/release-86/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.chromosome.22.fa.gz
ls -lh

```

Since this file is compressed, we won't be able to work with it directly. The `.gz` suffix indicates that it is a "gzip" compressed file. We can decompress this file with the command `gzip -d [filename]`. 

Q1) What does this do the the file and file name? (Check with `ls -lh` before and after)

Take a look inside the chr22 sequence file to look at the general format of the FASTA files:

```bash

head Homo_sapiens.GRCh38.dna.chromosome.22.fa

```

Then to look at a sort of summary view, use the `wc` command to count the number of lines, "words", and characters in the file:

```bash 

wc Homo_sapiens.GRCh38.dna.chromosome.22.fa

```

Q2) How many lines and characters are there in our sequence file? Use either the Ensembl or UCSC genome browsers to search for chr22 and find out how many DNA base pairs are present in chromosome 22. How well does this match up with what we find from the file and why might they be different?

The beginning of our sequence file may look a little funny. Use the line count you got from the `wc` command to take a look in the middle of the file. To easily pull out some lines in the middle, you can use a combination of the `head` and `tail` commands to get the front of the file up to a certain point, and then the end of that section. Aim for a number of lines roughly halfway through the file.

```bash

head -n [# of lines to middle] | tail -n [# of lines to print]

```

Q3) Do the beginning and middle of the file look similar? Why might one part be different than another?

Q4) How many of each base (A, C, G, T, or N) are present in chromsome 22? There are multiple ways to answer this. Hint: using `grep` with the `-o` flag outputs every match on a new line instead of entire lines.

Advanced question worth an extra half hour of lab time: AQ1) Restriction sites are DNA elements targeted by special enzymes known as "restriction enzymes" which cut DNA. How many EcoRI sites are present in chromosome 22?

## Reference transcriptome

## Reference genome annotation

# 1-iii. Obtaining transcriptome quantification data

