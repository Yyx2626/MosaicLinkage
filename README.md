# MosaicLinkage
MosaicLinkage is a java tool kit to explore local haplotypes based on stretched reads in a bam file.



## Usage:

    java -jar MosaicLinkage.jar <command> <one.bam> <ref.fa> ...

  command:
  
                 pileup          pileup a specified reference region
                 scanSnv         scan SNVs
                 linkage         count haplotypes of two specified regions
                 retrieve        retrieve read's mapping blocks
                 refRepeat       detect local repeats on reference sequence

Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI



### command 'pileup':

    java -jar MosaicLinkage.jar pileup
        <one.bam> <ref.fa> <query_chr> <query_start> <query_end>

Options:

    -help                               print command-line help message
    -version                            print the version information

    -pileupStart <int>                  the ref start position for pileup (default: queryStart)
    -pileupEnd <int>                    the ref end position for pileup (default: queryEnd)

    -regionsFile <String>               input regions from file instead (default: '')
    -regionsFileHasHeader               does the file has headline? (default: false)

    -outputFlag <int>                   the option flags of output tables, details see below (default: 12)
    -shouldQueryMate                    should I query mate reads (default: false)

    -maxMateDist <int>                  the max distance allowed for querying mate (default: 100000)
    -querySegmentFlag <int>             the option flags of querying segment, details see below (default: 0)

    outputFlag: 0x01  each read situation
                0x02  simple allele count table
                0x04  strand allele count table
                0x08  simple variant call table
                0x10  blat support table

    querySegmentFlag:   0x01  make insertion lowercase
                        0x02  remove deletion block
                        0x04  baseQ < minBaseQ lowercase (default: discard)
                        
  filteres related options:
  
    -requireFlag <int>                  reads require these flags to be processed (default: 0x0)
    -filterFlag <int>                   reads with these flags will be filtered (default: 0x700)
    -minBaseQ <int>                     discard segments containing baseQ < ? (default: 0)
    -minMapQ <int>                      discard segments containing mapQ < ? (default: 0)
    -minReadEndDist <int>               discard segments with distance to read end < ? (default: 0)
    -minIndelDist <int>                 discard segments with distance to read's indel < ? (default: 0)

  simple variant call (outputFlag 0x08) related options:
  
    -backgroundErrorRate <double>       the error rate for background binom.test (default: 0.01)
    -backgroundPValueCutoff <double>    the p-value cutoff for background binom.test (default: 0.05)
    -foregroundErrorRate <double>       the error rate for foreground binom.test (default: 0.05)
    -foregroundPValueCutoff <double>    the p-value cutoff for foreground binom.test (default: 0.05)

    -followingInsignifAlleleNum <int>   print how many alleles below foreground significant (default: 3)
    -notPrintAlleleCountHistogram       not print allele count histogram? (default: false)

  smartRepeat related options:
  
    -smartRepeatSlop                    slop the region based on refRepeat with its default setting (default: false)
    -smartRepeatMoreSlop <int>          any more slop flanking region near smartRepeat (default: 5)

  blat (outputFlag 0x10) related options:
  
    -blatPath <String>                  the path to blat (default: '')
    -blatOptions <String>               blat options (default: '-stepSize=5 -repMatch=2253 -minScore=0 -minIdentity=0.5 -noHead')
    -blatDeltaScore <int>               count multiple blat alignments with the delta score below the max score (default: 1)
    -blatMinOverlapPercentage <double>  the threshold of overlap for bam & blat alignment (default: 0.9)
    -blatReferenceFilename <String>     <ref_for_blat.fa> (default: <ref.fa>)
    -blatLiftOverFilename <String>      <*.chain> lift .bam to ref_for_blat.fa (default: null)
    -blatNeedTrimChr                    should I replace 'chr' to '' in chr name for comparison? (default: true)
    -blatTmpDirname <String>            the temp dir path (should already exists) (default: 'tmp')
    -blatKeepFiles                      should I keep intermediate files (default: false)


Input:

        <queryChr> <queryStart> <queryEnd> and -pileupStart -pileupEnd
          or use -regionsFile and ignore them (you can set them to 0)
        the 1st column of each line in the regionsFile
            should be in format chr:start-end
            or chr:queryStart-queryEnd:pileupStart-pileupEnd

Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI



### command 'scanSnv':

    java -jar MosaicLinkage.jar scanSnv
        <one.bam> <ref.fa> <query_chr> <query_start> <query_end>

Options:

    -help                               print command-line help message
    -version                            print the version information

    -scanStart <int>                    the ref start position for scanning SNVs (default: -1(mateStart))
    -scanEnd <int>                      the ref end position for scanning SNVs (default: -1(mateEnd))

    -maxMateDist <int>                  the max distance allowed for querying mate (default: 100000)
    -querySegmentFlag <int>             the option flags of querying segment, details see below (default: 0)

    -requireFlag <int>                  reads require these flags to be processed (default: 0x0)
    -filterFlag <int>                   reads with these flags will be filtered (default: 0x700)
    -minBaseQ <int>                     discard segments containing baseQ < ? (default: 0)
    -minMapQ <int>                      discard segments containing mapQ < ? (default: 0)
    -minReadEndDist <int>               discard segments with distance to read end < ? (default: 0)
    -minIndelDist <int>                 discard segments with distance to read's indel < ? (default: 0)

    -backgroundErrorRate <double>       the error rate for background binom.test (default: 0.01)
    -backgroundPValueCutoff <double>    the p-value cutoff for background binom.test (default: 0.05)
    -foregroundErrorRate <double>       the error rate for foreground binom.test (default: 0.05)
    -foregroundPValueCutoff <double>    the p-value cutoff for foreground binom.test (default: 0.05)

    -followingInsignifAlleleNum <int>   print how many alleles below foreground significant (default: 3)
    -notPrintAlleleCountHistogram       not print allele count histogram? (default: false)


    querySegmentFlag:   0x01  make insertion lowercase
                        0x02  remove deletion block
                        0x04  baseQ < minBaseQ lowercase (default: discard)

Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI



### command 'linkage':

    java -jar MosaicLinkage.jar linkage
        <one.bam> <ref.fa> <queryChr> <queryStart> <queryEnd>
        <siteStart1> <siteEnd1> <siteStart2> <siteEnd2>

Options:

    -help                               print command-line help message
    -version                            print the version information

    -maxMateDist <int>                  the max distance allowed for querying mate (default: 100000)
    -querySegmentFlag <int>             the option flags of querying segment, details see below (default: 0)

    -requireFlag <int>                  reads require these flags to be processed (default: 0x0)
    -filterFlag <int>                   reads with these flags will be filtered (default: 0x700)
    -minBaseQ <int>                     discard segments containing baseQ < ? (default: 0)
    -minMapQ <int>                      discard segments containing mapQ < ? (default: 0)
    -minReadEndDist <int>               discard segments with distance to read end < ? (default: 0)
    -minIndelDist <int>                 discard segments with distance to read's indel < ? (default: 0)

    -backgroundErrorRate <double>       the error rate for background binom.test (default: 0.01)
    -backgroundPValueCutoff <double>    the p-value cutoff for background binom.test (default: 0.05)
    -foregroundErrorRate <double>       the error rate for foreground binom.test (default: 0.05)
    -foregroundPValueCutoff <double>    the p-value cutoff for foreground binom.test (default: 0.05)

    -followingInsignifAlleleNum <int>   print how many alleles below foreground significant (default: 3)
    -notPrintAlleleCountHistogram       not print allele count histogram? (default: false)
    -shouldPrintEachRead                should print each read? (default: false)


    querySegmentFlag:   0x01  make insertion lowercase
                        0x02  remove deletion block
                        0x04  baseQ < minBaseQ lowercase (default: discard)

Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI



### command 'retrieve':

    java -jar MosaicLinkage.jar retrieve
        <one.bam> <ref.fa> <queryChr> <queryStart> <queryEnd>
        <readName1> [readName2] [readName3] ...

Options:

    -help                       print command-line help message
    -version                    print the version information

    -fuzzyRegex                 allow fuzzy regex match (default: false)
    -segStart <int>             the ref start position of retrived segment (default: queryStart)
    -segEnd <int>               the ref end position of retrived segment (default: queryEnd)

    -maxMateDist <int>          the max distance allowed for querying mate (default: 100000)
    -querySegmentFlag <int>     the option flags of querying segment, details see below (default: 0)

    -requireFlag <int>          reads require these flags to be processed (default: 0x0)
    -filterFlag <int>           reads with these flags will be filtered (default: 0x700)
    -minBaseQ <int>             discard segments containing baseQ < ? (default: 0)
    -minMapQ <int>              discard segments containing mapQ < ? (default: 0)
    -minReadEndDist <int>       discard segments with distance to read end < ? (default: 0)
    -minIndelDist <int>         discard segments with distance to read's indel < ? (default: 0)

    -debugQuerySegment          should debug querying segment? (default: false)


    querySegmentFlag:   0x01  make insertion lowercase
                        0x02  remove deletion block
                        0x04  baseQ < minBaseQ lowercase (default: discard)

Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI



### command 'refRepeat':

    java -jar MosaicLinkage.jar refRepeat
        <ref.fa>
        [positions1] [positions2] ...

Input:

        [positions1] [positions2] ...
        or the 1st column of each line in the positionsFile
            should be in format chr:pos1[,pos2,...]
Options:

    -help                       print command-line help message
    -version                    print the version information

    -positionsFile <String>     input positions from file instead (default: '')
    -positionsFileHasHeader     does the file has headline? (default: false)

    -slop <int>                 how many bp up/downstream of <pos> should be examined (default: 50)
    -minKmerLen <int>           min kmer length (default: 1)
    -maxKmerLen <int>           max kmer length (default: 6)

    -scoreShift <int>           score for shifting from <pos> (default: -15)
    -scoreMatch <int>           score for mathing (default: 10)
    -scoreGap <int>             score for gapping (default: -50)
    -stopExtendScoreThres <int> score threshold to stop extending (default: -80)

    -maxTopReportNum <int>      max num of reporting top repeats (default: 5)

    -perPosSummary              just give one-line summary per position (default: false)

    -smartRepeatSlop            give one-line smartRepeat for all positions (default: false)
    -smartRepeatMoreSlop <int>  any more slop flanking region near smartRepeat (default: 5)
    -outputNoHeader             should I skip header for smartRepeat output (default: false)


Version: 0.3.5 (2016-09-13)
Author: Adam Yongxin Ye @ CBI
