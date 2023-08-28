# VAP (Visualization of Read Alignments with Pangenomes)
A programme for visualization of reads alignment and path navigation in graphical pan-genome, The mapping stage of read to graph can be processed by vg giraffe/map or other similar softwares. The .bam file generated from the alignment was the file of input in VAP.
![main](https://user-images.githubusercontent.com/46209789/213981873-bc18ff74-93ff-4001-8ecd-3dfc1ed5992c.jpg)

##Version in Window Os was online, which can be downloaded in https://ricegenomichjx.xiaomy.net/VAP/VAPallgui.exe.

##The detailed description and usages can be found in documents in https://lipingfangs.github.io/VAPreadme.html. 


***Install***

**Dependency*

Pysam, matplotlib, mpld3

Vg, Samtools, Bedtools, mosdepth(to display reads coverage), seqkit (to display snp)

php environment (for web), apache (for web)

**Command tools*

```
cd VAP
chmod 700 graphsamtools
python setup.py install
cd script 
export PATH=$PATH:$(pwd)
chmod 700 *py
```

**web-server*
All pages are packaged in the webserver index. You can directly copy the page to the login index of the server to complete the deployment and make it accessible. If you have any difficulty, Please feel free to contact the author (lpf_bio@foxmail.com); The example platform was accessible with https://ricegenomichjx.xiaomy.net/VAP/sequenceextraction.php

***Usage***

**Generated the info.file contained graph tracks information*

```
For the graph generated from minigraph or Cactus-minigraph
gfatools gfa2fa -s <graph>.gfa > <graph>.fa
python VAP/runVAP.py --mode index --rfa <graph>.fa > <info.file>  
#The bug for complex region  was fixed in the lastest verion. no error should be reported in this stages.
#error tend to be reported in this step which will not affect the user in next step if the info file was generated; That was attriubute to the compliacted branch. It will be fixed in next version.

For the graph with individual nodes information, which means the SN/SO/SR information are lost in graph which contain the information in P line from each indiviual in graph construction
python VAP/runVAP.py --mode convey --gfa <graph>.gfa --ref <selected reference> > <graph>.r.fa 
python VAP/runVAP.py --mode index --rfa <graph>.r.fa > <info.file>  
```

**Extraction of related main and branch paths based on a certain reference genome interval*

The bam file can be generated from vg giraffe or other similar software, which should be sorted with samtools sort and build the index.

```
From .bam file
graphsamtools <info.file> <chromosome> <start posistion> <end posistion> <bam file> <out dir>

From .gam file (Longer time may used for the running of Vg)(The file of <index.xg> was generated from vg autoindex with -w map paramter)
graphsamtools   <info.file> <chromosome> <start posistion> <end posistion>  gam  <gam file> <index.xg> <out dir>  <out dir>
```

In snp mode within 2000bp:
```
graphsamtools <info.file> <chromosome> <start posistion> <end posistion> <bam file> <out dir> <graph>.fa
 
```
**Only display the Graph*

Large interval scales are supported(eg. a chromosome)

```
graphsamtools <info.file>  <chromosome> <start posistion> <end posistion> onlytrack <out dir>
```

**Population mode*

Please keep the script freqacq.py in the same index of graphsamtools

```
graphsamtools <info.file> <chromosome> <start posistion> <end posistion> population <population 1>,<population 1 name> <population 2>,<population 2 name> <out dir>

```
**For upload and visualized in Web-sever*

```
tar zcvf <out dir>.tar.gz <out dir>
```
Upload the <out dir>.tar.gz.

**For drawing and graph navigation with command lines*

```

usage:python runVAP.py -h (for more help) 

VSAG is a software for the visualization of short read alignment of graphical pan-genome

optional arguments:
  -h, --help            show this help message and exit
  --inindex ININDEX     the index of your data after process of graphsamtools
  --out OUT             The output file name of the image
  --geneinfo GENEINFO   bed file contained gene info
  --gff GFF             Annotation file of the graph genome
  --fa FA               Phase the sequence of reliable tracks
  --drawtype DRAWTYPE   Types of your data you want to visualization (onlytrack/read/coverage/mutiplesamples/Popultaion)
  --anntracks ANNTRACKS
                        High light the pair-end supported tracks(pathways), default not (0)
  --pairend PAIREND     Display the pair end information or not, default not (0)
  --pairendrange PAIRENDRANGE
                        The search range in the main track, default: 200
  --pairendtheraold PAIRENDTHERAOLD
                        The selected theraold in the main track, default: 1
  --gaingene GAINGENE   gain the gene from the gff file, default: 0
  --fl FL               The filter the track lengths below
  --td TD               draw the track direction or not
  --rd RD               draw the read direction or not
  --rn RN               draw the read name or not
  --legend LEGEND       draw the legend or not
  --legendheight LEGENDHEIGHT
                        The height of legend
  --snp SNP             draw the snp information or not (interval<2000bp)
  --onlysv ONLYSV       Only display the SV large than thersold (default for >5bp) or not
  --onlysvthersold ONLYSVTHERSOLD
                        Thersold of length only display the SV large than thersold or not
  --coveragesteplength COVERAGESTEPLENGTH
                        Length of step with coverage (default for 100bp)
  --middle MIDDLE       Middle the track and read, default yes (1)
  --trackcolor TRACKCOLOR
                        Track colors including main track and the branches default:#CDCD00,#00BFFF
  --readcolor READCOLOR
                        Read colors default:#FFC1C1
  --pairendcolor PAIRENDCOLOR
                        Colors of pair end link default:yellow
  --coveragecolor COVERAGECOLOR
                        coverage colors default:#FF7F50
  --anncolor ANNCOLOR   Read colors default:#D02090
  --genecolor GENECOLOR
                        Gene colors default:black
  --mutilplesamplecolor MUTILPLESAMPLECOLOR
                        Mutilple sample colors default:#FFDEAD,#FFA54F
  --sx SX               Size of X
  --sy SY               Size of Y
  --xlabel XLABEL       Label of your Y axi
  --ylabel YLABEL       Label of your X axi
  --ppi PPI             The dpi of your image
  --imtype IMTYPE       The image type of output;default:png; Support:pdf,svg,jpg
  --dw DW               web (.html output or not)


```
***Command example***

**Only display the graph*
```
python  runVAP.py --inindex   <dir generated from graphsamtools> --drawtype onlytrack
```
**Draw the alignment of read*

```
python runVAP.py  --inindex <dir generated from graphsamtools> 
```

**Draw the coverage of reads alignment*

```
python runVAP.py  --inindex <dir generated from graphsamtools>  --drawtype coverage
```

  **Draw the  alignment with split read information and predict the reliable segements(nodes)*

```
The dir <dir generated from graphsamtools> should be generated from gam mode in graphsamtools
python VAP/runVAP.py  --inindex <dir generated from graphsamtools> --mode gam --readspilt 1  --anntrack 1

```

 **Draw the  alignment with read pair information and predict the reliable segements(nodes)*

```

python runVAP.py  --inindex <dir generated from graphsamtools>  --pairend 1 --anntrack 1

```

 

 **Draw the distriubution of gene with .gff file (extraction based interval was realized)*

```
python runVAP.py  --inindex <dir generated from graphsamtools> --gaingene 1 --gff <annotationfile.gff>
```
 
**Draw the distriubution of coverage between population*

```
python runVAP.py  --inindex <dir generated from graphsamtools> --drawtype populationfreq
```
 
 **Draw the distriubution of coverage between population*


***Contacts and cite***

IF you have any problem or comment in usage, please feel free to contact the aurthor (fangping.li@scau.edu.cn) who will reply on time!

IF the software participate in your researech , please cite the paper: Visualization and review of reads alignment on the graphical pan-genome with VAP
Fangping Li, Haifei Hu, Zitong Xiao, Jingming Wang, Jieying Liu, Deshu Zhao, Yu Fu, Yijun Wang, Xue Yuan, Suhong Bu, Xiaofan Zhou, Junliang Zhao, Shaokui Wang
bioRxiv 2023.01.20.524849; doi: https://doi.org/10.1101/2023.01.20.524849, Thank you so much

