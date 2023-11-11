Session-03-04 Sample Symportal run and downstream analyses
================

# Objects, classes & variables

- Everything in R is an **object**–data, function, etc.

- The blueprint of an object is its **class**–description of the object.

- A **variable** is an R object that contains an object.

``` r
1 # is an object,
class(1) # a numeric object or class numeric

'one' # is an object,
class('one') # a string object

'1' # what is this?
class('1') # a string object

a # why does this not show? # it has nothing attached to it
'a' # why is this okay?, it is designated as a string object

# Note that any alphabet including "." and "_" can be used as a variable name (as long as it is preceded by an alphabet)

a <- 1 # assigning an object to a variable
```

# Functions

An R function is an object containing statements that are run together
in an order.

Can be a built-in (a base package \[no importation needed, included in R
when you downloaded it\]) or user defined 🤓

Algebra days… 🍎  
f(x) = x \* x (the function of “f” at x)

For example, x = 3, the result will be 9. We know that this function
squares any numeric object.

``` r
square_a_stuff <- 
  function(x){
            squared_stuff = x * x
            return(squared_stuff)
            }
```

How do we use a function?

bash scripting: `conda` **activate** *qiime2.2023.7*

``` r
square_a_stuff(8)
```

    [1] 64

# Import packages

Before starting, it is important to import the important packages that
will be used in a run. 📖

For this tutorial, the main packages are: `tidyverse` and `vegan`.

Note that there are many more packages that can be used but for the
purpose of this workshop… we’ll just be using these two. 🤓

First, you need to download the packages.

``` r
install.packages(c('tidyverse, vegan'))
```

Then import/call them.

``` r
library('tidyverse',
        'vegan',
        'ape')
```

    Warning in library("tidyverse", "vegan", "ape"): 'ape' not found on search
    path, using pos = 2

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ✔ purrr     1.0.1     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

# Import and clean data

## Metadata

Import.

``` r
metadata <- 
  read_csv('../data/Session-03-04-metadata.csv')
```

    Rows: 190 Columns: 14
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: ","
    chr (13): folder_name, sample_name, batch, uniq_id, fastq_fwd_file_name, fas...
    dbl  (1): age_years

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Clean.

``` r
metadata1 <- 
  metadata %>% 
  filter(coi_validated_species == 'Ts'| coi_validated_species == 'Tg') # Ts and Tg are wanted
```

## Symportal ITS2 sequences results

Import.

``` r
its2seq <- 
  read_tsv('../data/Session-03-04-symportal-its2seq.txt')
```

    Rows: 191 Columns: 500
    ── Column specification ────────────────────────────────────────────────────────
    Delimiter: "\t"
    chr  (16): sample_uid, sample_name, fastq_fwd_file_name, fastq_fwd_sha256_fi...
    dbl (484): data_set_uid, raw_contigs, post_qc_absolute_seqs, post_qc_unique_...

    ℹ Use `spec()` to retrieve the full column specification for this data.
    ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# Check dataframe
head(its2seq)
```

    # A tibble: 6 × 500
      sample_uid sample_name   fastq_fwd_file_name      fastq_fwd_sha256_file_hash  
      <chr>      <chr>         <chr>                    <chr>                       
    1 156651     camiguin_ts_1 Camiguin_Ts_1_1.fastq.gz 6cbd1678cf2dc83b5488b0ffba5…
    2 156652     camiguin_ts_3 Camiguin_Ts_3_1.fastq.gz 1f8fa8b7bebfd2ed9023796d5d3…
    3 156608     bohol_ts_2    Bohol_Ts_2_1.fastq.gz    d2759f8dbbc4d0f2be34d64d26d…
    4 156609     bohol_ts_3    Bohol_Ts_3_1.fastq.gz    8b54021b44b21783ae391b307ec…
    5 156654     mab_tg_1      MAB-Tg-1_1.fastq.gz      ddf427a038711ecde5b9259b1a1…
    6 156607     bohol_ts_1    Bohol_Ts_1_1.fastq.gz    550107335ba7347ddca5a19e5dc…
    # ℹ 496 more variables: fastq_rev_file_name <chr>,
    #   fastq_rev_sha256_file_hash <chr>, data_set_uid <dbl>, data_set_name <chr>,
    #   raw_contigs <dbl>, post_qc_absolute_seqs <dbl>, post_qc_unique_seqs <dbl>,
    #   post_taxa_id_absolute_symbiodiniaceae_seqs <dbl>,
    #   post_taxa_id_unique_symbiodiniaceae_seqs <dbl>,
    #   size_screening_violation_absolute <dbl>,
    #   size_screening_violation_unique <dbl>, …

``` r
tail(its2seq)
```

    # A tibble: 6 × 500
      sample_uid    sample_name   fastq_fwd_file_name      fastq_fwd_sha256_file_h…¹
      <chr>         <chr>         <chr>                    <chr>                    
    1 156636        camiguin_tc_1 Camiguin_Tc_1_1.fastq.gz 73acf5495ad6a7102f0b942d…
    2 156576        apo_b_td_1    APO-B-Td-1_1.fastq.gz    54e95ca75c5f324f426c3e18…
    3 156566        apo_a_tc_6    APO-A-Tc-6_1.fastq.gz    397539dbc3e27101cdcd6c82…
    4 156627        cal_p_ts_3    CAL-P-Ts-3_1.fastq.gz    726c4f3000e1731f6383b491…
    5 156597        bohol_hh_3    Bohol_Hh_3_1.fastq.gz    6c31e9fb720c5a4273f7d4ad…
    6 seq_accession <NA>          <NA>                     <NA>                     
    # ℹ abbreviated name: ¹​fastq_fwd_sha256_file_hash
    # ℹ 496 more variables: fastq_rev_file_name <chr>,
    #   fastq_rev_sha256_file_hash <chr>, data_set_uid <dbl>, data_set_name <chr>,
    #   raw_contigs <dbl>, post_qc_absolute_seqs <dbl>, post_qc_unique_seqs <dbl>,
    #   post_taxa_id_absolute_symbiodiniaceae_seqs <dbl>,
    #   post_taxa_id_unique_symbiodiniaceae_seqs <dbl>,
    #   size_screening_violation_absolute <dbl>, …

Clean.

``` r
its2seq1 <- 
  its2seq %>% 
  slice(1:190)

colnames(its2seq1) # Large number of
```

      [1] "sample_uid"                                    
      [2] "sample_name"                                   
      [3] "fastq_fwd_file_name"                           
      [4] "fastq_fwd_sha256_file_hash"                    
      [5] "fastq_rev_file_name"                           
      [6] "fastq_rev_sha256_file_hash"                    
      [7] "data_set_uid"                                  
      [8] "data_set_name"                                 
      [9] "raw_contigs"                                   
     [10] "post_qc_absolute_seqs"                         
     [11] "post_qc_unique_seqs"                           
     [12] "post_taxa_id_absolute_symbiodiniaceae_seqs"    
     [13] "post_taxa_id_unique_symbiodiniaceae_seqs"      
     [14] "size_screening_violation_absolute"             
     [15] "size_screening_violation_unique"               
     [16] "post_taxa_id_absolute_non_symbiodiniaceae_seqs"
     [17] "post_taxa_id_unique_non_symbiodiniaceae_seqs"  
     [18] "post_med_absolute"                             
     [19] "post_med_unique"                               
     [20] "noName Clade A"                                
     [21] "noName Clade B"                                
     [22] "noName Clade C"                                
     [23] "noName Clade D"                                
     [24] "noName Clade E"                                
     [25] "noName Clade F"                                
     [26] "noName Clade G"                                
     [27] "noName Clade H"                                
     [28] "noName Clade I"                                
     [29] "sample_type"                                   
     [30] "host_phylum"                                   
     [31] "host_class"                                    
     [32] "host_order"                                    
     [33] "host_family"                                   
     [34] "host_genus"                                    
     [35] "host_species"                                  
     [36] "collection_latitude"                           
     [37] "collection_longitude"                          
     [38] "collection_date"                               
     [39] "collection_depth"                              
     [40] "A3"                                            
     [41] "A6b"                                           
     [42] "A6e"                                           
     [43] "A3g"                                           
     [44] "A3ck"                                          
     [45] "A6d"                                           
     [46] "A3bx"                                          
     [47] "35200_A"                                       
     [48] "A6c"                                           
     [49] "22387_A"                                       
     [50] "A3bb"                                          
     [51] "498914_A"                                      
     [52] "492445_A"                                      
     [53] "A3ai"                                          
     [54] "477175_A"                                      
     [55] "73541_A"                                       
     [56] "A3ab"                                          
     [57] "22460_A"                                       
     [58] "875055_A"                                      
     [59] "363610_A"                                      
     [60] "473921_A"                                      
     [61] "A3aa"                                          
     [62] "473916_A"                                      
     [63] "A6h"                                           
     [64] "363683_A"                                      
     [65] "364454_A"                                      
     [66] "701263_A"                                      
     [67] "701375_A"                                      
     [68] "A3s"                                           
     [69] "A3bf"                                          
     [70] "701110_A"                                      
     [71] "A3bs"                                          
     [72] "A3ao"                                          
     [73] "A3v"                                           
     [74] "875056_A"                                      
     [75] "A3am"                                          
     [76] "701701_A"                                      
     [77] "869370_A"                                      
     [78] "875028_A"                                      
     [79] "A3an"                                          
     [80] "A3r"                                           
     [81] "869124_A"                                      
     [82] "A3q"                                           
     [83] "364516_A"                                      
     [84] "363143_A"                                      
     [85] "869125_A"                                      
     [86] "477177_A"                                      
     [87] "875031_A"                                      
     [88] "875033_A"                                      
     [89] "363666_A"                                      
     [90] "A3bp"                                          
     [91] "869117_A"                                      
     [92] "34175_A"                                       
     [93] "364447_A"                                      
     [94] "A3bd"                                          
     [95] "366252_A"                                      
     [96] "34184_A"                                       
     [97] "A6g"                                           
     [98] "A3ar"                                          
     [99] "477174_A"                                      
    [100] "701081_A"                                      
    [101] "34300_A"                                       
    [102] "363604_A"                                      
    [103] "704184_A"                                      
    [104] "1236441_A"                                     
    [105] "701567_A"                                      
    [106] "875076_A"                                      
    [107] "366420_A"                                      
    [108] "1236442_A"                                     
    [109] "875062_A"                                      
    [110] "1236443_A"                                     
    [111] "700994_A"                                      
    [112] "1236434_A"                                     
    [113] "875032_A"                                      
    [114] "C93a"                                          
    [115] "C1"                                            
    [116] "C66"                                           
    [117] "C1c"                                           
    [118] "C93e"                                          
    [119] "C1b"                                           
    [120] "C1al"                                          
    [121] "C55a"                                          
    [122] "C116ac"                                        
    [123] "C72k"                                          
    [124] "C3"                                            
    [125] "C93n"                                          
    [126] "C93f"                                          
    [127] "C3ju"                                          
    [128] "C42.2"                                         
    [129] "C15gv"                                         
    [130] "C1w"                                           
    [131] "C91"                                           
    [132] "C15mf"                                         
    [133] "C1bh"                                          
    [134] "C3ew"                                          
    [135] "C1jx"                                          
    [136] "C66as"                                         
    [137] "C3sa"                                          
    [138] "C1br"                                          
    [139] "C66ac"                                         
    [140] "C15ph"                                         
    [141] "C15gw"                                         
    [142] "C1cu"                                          
    [143] "C93m"                                          
    [144] "869471_C"                                      
    [145] "C1lx"                                          
    [146] "C1bc"                                          
    [147] "61773_C"                                       
    [148] "C1cb"                                          
    [149] "875020_C"                                      
    [150] "C93g"                                          
    [151] "875021_C"                                      
    [152] "C93h"                                          
    [153] "C66a"                                          
    [154] "C93o"                                          
    [155] "869123_C"                                      
    [156] "644342_C"                                      
    [157] "C1my"                                          
    [158] "C1t"                                           
    [159] "21064_C"                                       
    [160] "869122_C"                                      
    [161] "70158_C"                                       
    [162] "C64b"                                          
    [163] "61977_C"                                       
    [164] "23092_C"                                       
    [165] "61772_C"                                       
    [166] "21513_C"                                       
    [167] "485802_C"                                      
    [168] "52614_C"                                       
    [169] "16573_C"                                       
    [170] "23091_C"                                       
    [171] "21059_C"                                       
    [172] "21508_C"                                       
    [173] "869115_C"                                      
    [174] "869940_C"                                      
    [175] "21085_C"                                       
    [176] "869770_C"                                      
    [177] "C42au"                                         
    [178] "C1j"                                           
    [179] "45636_C"                                       
    [180] "5429_C"                                        
    [181] "C66ad"                                         
    [182] "18551_C"                                       
    [183] "21053_C"                                       
    [184] "21042_C"                                       
    [185] "488884_C"                                      
    [186] "C3cy"                                          
    [187] "C66be"                                         
    [188] "21858_C"                                       
    [189] "17032_C"                                       
    [190] "C1h"                                           
    [191] "781255_C"                                      
    [192] "67304_C"                                       
    [193] "875023_C"                                      
    [194] "C1fo"                                          
    [195] "21069_C"                                       
    [196] "C1kq"                                          
    [197] "C1am"                                          
    [198] "21507_C"                                       
    [199] "36775_C"                                       
    [200] "8439_C"                                        
    [201] "C57y"                                          
    [202] "875024_C"                                      
    [203] "C116ai"                                        
    [204] "875025_C"                                      
    [205] "875029_C"                                      
    [206] "488956_C"                                      
    [207] "C116aj"                                        
    [208] "875043_C"                                      
    [209] "C1ao"                                          
    [210] "781248_C"                                      
    [211] "494295_C"                                      
    [212] "C3cm"                                          
    [213] "770706_C"                                      
    [214] "99675_C"                                       
    [215] "37251_C"                                       
    [216] "21077_C"                                       
    [217] "C66bm"                                         
    [218] "465748_C"                                      
    [219] "C91d"                                          
    [220] "C15mu"                                         
    [221] "592135_C"                                      
    [222] "C3gulf"                                        
    [223] "C116aw"                                        
    [224] "655122_C"                                      
    [225] "C1an"                                          
    [226] "C66d"                                          
    [227] "13929_C"                                       
    [228] "C57x"                                          
    [229] "21062_C"                                       
    [230] "C57w"                                          
    [231] "C1lc"                                          
    [232] "875059_C"                                      
    [233] "C1en"                                          
    [234] "21063_C"                                       
    [235] "C1x"                                           
    [236] "364853_C"                                      
    [237] "485589_C"                                      
    [238] "61886_C"                                       
    [239] "27335_C"                                       
    [240] "875045_C"                                      
    [241] "870236_C"                                      
    [242] "875046_C"                                      
    [243] "70122_C"                                       
    [244] "C1l"                                           
    [245] "737342_C"                                      
    [246] "1236439_C"                                     
    [247] "741538_C"                                      
    [248] "869928_C"                                      
    [249] "741578_C"                                      
    [250] "72695_C"                                       
    [251] "C15"                                           
    [252] "365081_C"                                      
    [253] "C3ld"                                          
    [254] "C42ca"                                         
    [255] "281694_C"                                      
    [256] "C42ao"                                         
    [257] "40017_C"                                       
    [258] "751691_C"                                      
    [259] "694_C"                                         
    [260] "C1hq"                                          
    [261] "27389_C"                                       
    [262] "1236440_C"                                     
    [263] "90652_C"                                       
    [264] "21087_C"                                       
    [265] "572965_C"                                      
    [266] "875030_C"                                      
    [267] "281_C"                                         
    [268] "875026_C"                                      
    [269] "C2a"                                           
    [270] "875022_C"                                      
    [271] "70161_C"                                       
    [272] "204587_C"                                      
    [273] "869952_C"                                      
    [274] "91090_C"                                       
    [275] "C3u"                                           
    [276] "C93r"                                          
    [277] "23049_C"                                       
    [278] "C3uu"                                          
    [279] "875063_C"                                      
    [280] "94886_C"                                       
    [281] "875060_C"                                      
    [282] "62115_C"                                       
    [283] "28368_C"                                       
    [284] "99779_C"                                       
    [285] "483841_C"                                      
    [286] "871_C"                                         
    [287] "869412_C"                                      
    [288] "700045_C"                                      
    [289] "875052_C"                                      
    [290] "282142_C"                                      
    [291] "875061_C"                                      
    [292] "869428_C"                                      
    [293] "23050_C"                                       
    [294] "869599_C"                                      
    [295] "875040_C"                                      
    [296] "383985_C"                                      
    [297] "262683_C"                                      
    [298] "27093_C"                                       
    [299] "15170_C"                                       
    [300] "C2b"                                           
    [301] "875053_C"                                      
    [302] "18656_C"                                       
    [303] "40562_C"                                       
    [304] "D5"                                            
    [305] "D4"                                            
    [306] "D9b"                                           
    [307] "D1"                                            
    [308] "D4u"                                           
    [309] "D1bq"                                          
    [310] "D5s"                                           
    [311] "D4c"                                           
    [312] "D1br"                                          
    [313] "D5r"                                           
    [314] "D5o"                                           
    [315] "D5n"                                           
    [316] "D5q"                                           
    [317] "D5p"                                           
    [318] "D4as"                                          
    [319] "D7"                                            
    [320] "D1fx"                                          
    [321] "D1c"                                           
    [322] "875042_D"                                      
    [323] "D4ak"                                          
    [324] "D2"                                            
    [325] "D9"                                            
    [326] "D6"                                            
    [327] "24902_D"                                       
    [328] "D10"                                           
    [329] "24338_D"                                       
    [330] "D4f"                                           
    [331] "23374_D"                                       
    [332] "D17m"                                          
    [333] "1236433_D"                                     
    [334] "173214_D"                                      
    [335] "23680_D"                                       
    [336] "D17j"                                          
    [337] "869111_D"                                      
    [338] "17449_D"                                       
    [339] "183662_D"                                      
    [340] "D1ji"                                          
    [341] "869119_D"                                      
    [342] "223597_D"                                      
    [343] "D1b"                                           
    [344] "700286_D"                                      
    [345] "D4ao"                                          
    [346] "332300_D"                                      
    [347] "333809_D"                                      
    [348] "D3b"                                           
    [349] "875047_D"                                      
    [350] "D5x"                                           
    [351] "700582_D"                                      
    [352] "23312_D"                                       
    [353] "D12"                                           
    [354] "237900_D"                                      
    [355] "241254_D"                                      
    [356] "24591_D"                                       
    [357] "333260_D"                                      
    [358] "D17n"                                          
    [359] "D17d"                                          
    [360] "333790_D"                                      
    [361] "1236430_D"                                     
    [362] "D6c"                                           
    [363] "25711_D"                                       
    [364] "D5k"                                           
    [365] "332124_D"                                      
    [366] "D5m"                                           
    [367] "D7a"                                           
    [368] "276153_D"                                      
    [369] "D1r"                                           
    [370] "1236444_D"                                     
    [371] "D1l"                                           
    [372] "68186_D"                                       
    [373] "700274_D"                                      
    [374] "D1h"                                           
    [375] "18999_D"                                       
    [376] "15804_D"                                       
    [377] "24530_D"                                       
    [378] "D2.2"                                          
    [379] "110604_D"                                      
    [380] "700461_D"                                      
    [381] "D1u"                                           
    [382] "25396_D"                                       
    [383] "869172_D"                                      
    [384] "332142_D"                                      
    [385] "875027_D"                                      
    [386] "1236428_D"                                     
    [387] "D5a"                                           
    [388] "869114_D"                                      
    [389] "2748281_D"                                     
    [390] "8840_D"                                        
    [391] "D13a"                                          
    [392] "333556_D"                                      
    [393] "702930_D"                                      
    [394] "70171_D"                                       
    [395] "50017_D"                                       
    [396] "23624_D"                                       
    [397] "4127_D"                                        
    [398] "333794_D"                                      
    [399] "700720_D"                                      
    [400] "D17e"                                          
    [401] "D1in"                                          
    [402] "D17c"                                          
    [403] "700797_D"                                      
    [404] "D1db"                                          
    [405] "875039_D"                                      
    [406] "876403_D"                                      
    [407] "440336_D"                                      
    [408] "1236436_D"                                     
    [409] "1236437_D"                                     
    [410] "5587_D"                                        
    [411] "D17l"                                          
    [412] "23812_D"                                       
    [413] "1766_D"                                        
    [414] "23537_D"                                       
    [415] "474175_D"                                      
    [416] "875044_D"                                      
    [417] "869110_D"                                      
    [418] "D17k"                                          
    [419] "875048_D"                                      
    [420] "1236438_D"                                     
    [421] "12743_D"                                       
    [422] "702873_D"                                      
    [423] "333311_D"                                      
    [424] "31653_D"                                       
    [425] "D1dl"                                          
    [426] "24868_D"                                       
    [427] "875049_D"                                      
    [428] "D4p"                                           
    [429] "876220_D"                                      
    [430] "184052_D"                                      
    [431] "876215_D"                                      
    [432] "1236431_D"                                     
    [433] "70139_D"                                       
    [434] "876020_D"                                      
    [435] "12883_D"                                       
    [436] "869120_D"                                      
    [437] "D17af"                                         
    [438] "875035_D"                                      
    [439] "875036_D"                                      
    [440] "23898_D"                                       
    [441] "700724_D"                                      
    [442] "875950_D"                                      
    [443] "70142_D"                                       
    [444] "700812_D"                                      
    [445] "869201_D"                                      
    [446] "131534_D"                                      
    [447] "875037_D"                                      
    [448] "875050_D"                                      
    [449] "875038_D"                                      
    [450] "240063_D"                                      
    [451] "24936_D"                                       
    [452] "875064_D"                                      
    [453] "875057_D"                                      
    [454] "869272_D"                                      
    [455] "875051_D"                                      
    [456] "2748608_D"                                     
    [457] "D1bu"                                          
    [458] "875065_D"                                      
    [459] "1236427_D"                                     
    [460] "24647_D"                                       
    [461] "209027_D"                                      
    [462] "1201100_D"                                     
    [463] "875058_D"                                      
    [464] "869121_D"                                      
    [465] "875066_D"                                      
    [466] "26173_D"                                       
    [467] "110629_D"                                      
    [468] "10400_D"                                       
    [469] "875067_D"                                      
    [470] "1165283_D"                                     
    [471] "875068_D"                                      
    [472] "19371_D"                                       
    [473] "D4i"                                           
    [474] "104982_D"                                      
    [475] "875069_D"                                      
    [476] "134818_D"                                      
    [477] "D3a"                                           
    [478] "700549_D"                                      
    [479] "875070_D"                                      
    [480] "15053_D"                                       
    [481] "105024_D"                                      
    [482] "14228_D"                                       
    [483] "446980_D"                                      
    [484] "1148018_D"                                     
    [485] "875072_D"                                      
    [486] "875071_D"                                      
    [487] "11298_D"                                       
    [488] "869312_D"                                      
    [489] "875041_D"                                      
    [490] "D4bc"                                          
    [491] "875073_D"                                      
    [492] "10936_D"                                       
    [493] "23698_D"                                       
    [494] "332156_D"                                      
    [495] "90532_D"                                       
    [496] "875075_D"                                      
    [497] "875074_D"                                      
    [498] "1462497_D"                                     
    [499] "131566_D"                                      
    [500] "1238215_G"                                     

Make it cleaner.

``` r
its2seq2 <- 
  its2seq1 %>% 
  select(2,40:500) %>% 
  filter(sample_name %in% pull(metadata1, transformed))
```

## Merge metadata and ITS2 sequences

Merge metadata and ITS2 sequences and clean. 🧹

``` r
its2seq_metadata <- 
  metadata1 %>%
  select(!sample_name) %>% 
  rename(sample_name = transformed) %>% 
  left_join(its2seq2, by = 'sample_name') %>% 
  select(!c(1:6)) %>% 
  select(!c(2,4,6,7))
```

# Data analysis

## Abundance plots

### Genus

Clean data.

``` r
gen_metadata <- 
  its2seq_metadata %>% 
  rowwise() %>% 
  mutate(A = sum(c_across(contains('A', ignore.case = F))),
         C = sum(c_across(contains('C', ignore.case = F))),
         D = sum(c_across(contains('D', ignore.case = F))),
         G = sum(c_across(contains('G', ignore.case = F)))) %>% 
  select(c(1:3, 465:468))

gen_metadata1 <- 
  gen_metadata %>% 
  pivot_longer(cols = c(4:7),
               names_to = 'Genus',
               values_to = 'Count') %>% 
  pivot_longer(cols = c(2:3),
               names_to = 'facet_param',
               values_to = 'x')
```

Make plot.

``` r
gen_metadata1_plot <- 
  gen_metadata1 %>% 
  mutate(x = str_replace(x, 'Tridacna', 'T.')) %>% 
  ggplot(aes(x = x,
             y = Count,
             fill = Genus)) +
  geom_bar(position="fill", 
           stat="identity") +
  facet_grid(cols = vars(facet_param),
             scales = 'free_x',
             space = 'free_x') +
  labs(y = 'Relative abundance (%)') +
  theme_bw() +
  theme(panel.grid = element_blank(),
        axis.title.x = element_blank())

ggsave(gen_metadata1_plot,
       filename = '../results/gen_metadata1_plot.png',
       width = 300,
       height = 150, 
       units = "mm",
       type = 'cairo',
       device = 'png')
```

    Warning: Using ragg device as default. Ignoring `type` and `antialias`
    arguments

### ITS2 sequence

clean data.

``` r
its2seq_metadata1 <- 
  its2seq_metadata %>% 
  pivot_longer(cols = !c(1:3),
               names_to = 'ITS2',
               values_to = 'Count') %>% 
  pivot_longer(cols = c(2:3),
               names_to = 'facet_param',
               values_to = 'x')
```

Make plot.

``` r
its2seq_metadata1_plot <- 
  its2seq_metadata1 %>% 
  mutate(x = str_replace(x, 'Tridacna', 'T.')) %>% 
  ggplot(aes(x = x,
             y = Count,
             fill = ITS2)) +
  geom_bar(position="fill", 
           stat="identity") +
  facet_grid(cols = vars(facet_param),
             scales = 'free_x',
             space = 'free_x') +
  labs(y = 'Relative abundance (%)') +
  theme_bw() +
  theme(panel.grid = element_blank(),
        axis.title.x = element_blank())
```

Get top 10.

``` r
its2seq_metadata2 <- 
  its2seq_metadata %>% 
  select(!c(2:3)) %>% 
  pivot_longer(cols = !1,
               names_to = 'ITS2',
               values_to = 'Count') %>% 
  group_by(ITS2) %>% 
  summarise(`Total count` = sum(Count)) %>% 
  arrange(desc(`Total count`)) %>% 
  mutate(`Relative abundance (%)` = round(`Total count`/6750449 * 100,2))

write_csv(its2seq_metadata2,
          '../results/its2seq_metadata2_relativeabundance.csv')
```

Make a plot.

``` r
its2seq_metadata1_plot <- 
  its2seq_metadata1 %>% 
  mutate(x = str_replace(x, 'Tridacna', 'T.')) %>% 
  filter(ITS2 %in% pull(slice(its2seq_metadata2, 1:10), ITS2)) %>% 
  ggplot(aes(x = x,
             y = Count,
             fill = ITS2)) +
  geom_bar(position="fill", 
           stat="identity") +
  facet_grid(cols = vars(facet_param),
             scales = 'free_x',
             space = 'free_x') +
  labs(y = 'Relative abundance (%)') +
  theme_bw() +
  theme(panel.grid = element_blank(),
        axis.title.x = element_blank())

ggsave(its2seq_metadata1_plot,
       filename = '../results/its2seq_metadata1_plot.png',
       width = 300,
       height = 150, 
       units = "mm",
       type = 'cairo',
       device = 'png')
```

    Warning: Using ragg device as default. Ignoring `type` and `antialias`
    arguments

Get relative abundance per species and site.

``` r
its2seq_metadata1_relativeabundance <- 
  its2seq_metadata1 %>% 
  group_by(x, ITS2) %>% 
  summarise(`Total count` = sum(Count)) %>% 
  mutate(group = case_when(str_detect(x, 'Tridacna') ~ 'GC species',
                           .default = 'Site'))
```

    `summarise()` has grouped output by 'x'. You can override using the `.groups`
    argument.

``` r
its2seq_metadata1_relativeabundance_site <- 
  its2seq_metadata1_relativeabundance %>% 
  filter(group == 'Site')  %>% 
  mutate('Relative abundance' = `Total count`/sum(`Total count`)*100)

write_csv(its2seq_metadata1_relativeabundance_site,
          '../results/its2seq_metadata2_relativeabundance_site.csv')

###

its2seq_metadata1_relativeabundance_gc <- 
  its2seq_metadata1_relativeabundance %>% 
  filter(group == 'GC species')  %>% 
  mutate('Relative abundance' = `Total count`/sum(`Total count`)*100)

write_csv(its2seq_metadata1_relativeabundance_gc,
          '../results/its2seq_metadata2_relativeabundance_gc.csv')
```

## Beta diversity

### Make matrices

Make a metadata matrix.

Note: most vegan packages use matrices as input, not matrix, error! ⚠️

``` r
metadata1_matrix <- 
  metadata1 %>% 
  select(!sample_name) %>% 
  rename(sample_name = transformed) %>% 
  select(sample_name, comple_species, region) %>% 
  arrange(desc(sample_name)) %>% 
  mutate(sample_name2 = sample_name) %>% 
  column_to_rownames('sample_name2')
```

Make an abundance table matrix.

``` r
its2seq2_matrix <- 
  its2seq_metadata %>% 
  select(!c(2,3)) %>% 
  arrange(desc(sample_name)) %>% 
  column_to_rownames('sample_name')
```

### Compute Bray-Curtis

Compute Bray-Curtis

``` r
its2seq2_matrix_bc <- 
  vegan::vegdist(its2seq2_matrix)

write.csv(its2seq2_matrix_bc %>% as.matrix(),
          '../results/its2seq2_matrix_bc.csv')
```

### PCoA

Bray-Curtis ➡️ PCoA.

``` r
its2seq2_matrix_bc_pcoa <- 
  ape::pcoa(its2seq2_matrix_bc)

its2seq2_matrix_bc_pcoa1 <- 
  ape::pcoa(its2seq2_matrix_bc) %>% 
  .$vectors %>% 
  as_data_frame() %>% 
  rownames_to_column('bc_name')
```

    Warning: `as_data_frame()` was deprecated in tibble 2.0.0.
    ℹ Please use `as_tibble()` (with slightly different semantics) to convert to a
      tibble, or `as.data.frame()` to convert to a data frame.

``` r
metadata1_matrix1 <- 
  metadata1_matrix %>% 
  mutate(bc_name = 1:n())

its2seq2_pcoa_meta <- 
  its2seq2_matrix_bc_pcoa1 %>% 
  type_convert() %>% 
  left_join(metadata1_matrix1,
            by = 'bc_name') %>% 
  relocate(36:38)
```


    ── Column specification ────────────────────────────────────────────────────────
    cols(
      bc_name = col_double()
    )

Plot PCoA for species comparison.

``` r
its2seq2_pcoa_meta_plot_species <- 
  its2seq2_pcoa_meta %>% 
  ggplot(aes(x = Axis.1,
             y = Axis.2,
             color = comple_species)) +
  geom_vline(xintercept = 0, linetype = 'dashed', color = 'gray') +
  geom_hline(yintercept = 0, linetype = 'dashed', color = 'gray') +
  geom_point(size=4) +
  stat_ellipse() +
  theme_bw() +
  theme(panel.grid = element_blank())
```

Plot PCoA for site.

``` r
its2seq2_pcoa_meta_plot_site <- 
  its2seq2_pcoa_meta %>% 
  ggplot(aes(x = Axis.1,
             y = Axis.2,
             color = region)) +
  geom_vline(xintercept = 0, linetype = 'dashed', color = 'gray') +
  geom_hline(yintercept = 0, linetype = 'dashed', color = 'gray') +
  geom_point(size=4) +
  theme_bw() +
  theme(panel.grid = element_blank())
```

### PERMANOVA

``` r
its2seq2_pcoa_permanova <- 
  vegan::adonis2(its2seq2_matrix_bc ~ `comple_species` * `region`, 
          data = metadata1_matrix1, 
          permutations = 9999)

write_csv(its2seq2_pcoa_permanova %>% rownames_to_column('Parameters'),
          '../results/its2seq2_pcoa_permanova.csv')
```

### PERMDISP

``` r
its2seq2_pcoa_permdisp_gc <- 
  vegan::betadisper(its2seq2_matrix_bc,
             metadata1_matrix1 %>% pull(comple_species)) %>% 
  anova() %>% 
  rownames_to_column('Parameters') %>% 
  as_tibble() %>% 
  drop_na() %>% 
  mutate('Group comparison' = 'Giant clam species') %>% 
  relocate('Group comparison')
```

    Warning in vegan::betadisper(its2seq2_matrix_bc, metadata1_matrix1 %>%
    pull(comple_species)): some squared distances are negative and changed to zero

``` r
write_csv(its2seq2_pcoa_permdisp_gc,
          '../results/its2seq2_pcoa_permdisp_gc.csv')
  
its2seq2_pcoa_permdisp_site <- 
  vegan::betadisper(its2seq2_matrix_bc,
             metadata1_matrix1 %>% pull(region)) %>% 
  anova() %>% 
  rownames_to_column('Parameters') %>% 
  as_tibble() %>% 
  drop_na() %>% 
  mutate('Group comparison' = 'Site') %>% 
  relocate('Group comparison')
```

    Warning in vegan::betadisper(its2seq2_matrix_bc, metadata1_matrix1 %>%
    pull(region)): some squared distances are negative and changed to zero

``` r
write_csv(its2seq2_pcoa_permdisp_gc,
          '../results/its2seq2_pcoa_permdisp_site.csv')
```

## Alpha diversity

Compute alpha diversity.

``` r
its2seq_metadata_alpha <- 
  its2seq_metadata %>% 
  pivot_longer(cols = !c(1:3),
               names_to = 'ITS2',
               values_to = 'Count') %>% 
  group_by(sample_name) %>% 
  summarise(observed_its2seqs = vegan::specnumber(Count),
            shannon = vegan::diversity(Count)) %>% 
  left_join(metadata1_matrix,
            by = 'sample_name')
```

### Boxplots

Clean data.

``` r
its2seq_metadata_alpha_plot <- 
  its2seq_metadata_alpha %>% 
  mutate(comple_species = str_replace(comple_species, 'Tridacna', 'T.')) %>% 
  pivot_longer(cols = c(2,3),
               names_to = 'alpha_metric',
               values_to = 'alpha_value') %>% 
  pivot_longer(cols = c(comple_species, region),
               names_to = 'facet_parameter',
               values_to = 'group') %>% 
  ggplot(aes(x = group,
             y = alpha_value)) +
  geom_violin(trim = F,
              color = NA,
              alpha = 0.5,
              fill = 'black') +
  geom_boxplot(fill = 'black',
               width = 0.04,
               outlier.shape = NA) + 
  stat_summary(geom = 'point',
               fun = median,
               shape = 21,
               size = 2,
               fill = 'white') +
  facet_grid(rows = vars(alpha_metric),
             cols = vars(facet_parameter),
             scales = 'free',
             space = 'free_x') +
  theme_bw() +
  theme(panel.grid = element_blank())

ggsave(its2seq_metadata_alpha_plot,
       filename = '../results/its2seq_metadata_alpha_plot.png',
       width = 300,
       height = 150, 
       units = "mm",
       type = 'cairo',
       device = 'png')
```

    Warning: Using ragg device as default. Ignoring `type` and `antialias`
    arguments

### Kruskal-Wallis (non-parametric ANOVA)

Do stats.

``` r
its2seq_metadata_alpha_site_kw <- 
  its2seq_metadata_alpha %>% 
  kruskal.test(observed_its2seqs ~ region, data = .)

write_csv(its2seq_metadata_alpha_site_kw %>% unclass() %>% as_tibble(),
          '../results/its2seq_metadata_alpha_site_kw.csv')

its2seq_metadata_alpha_gc_kw <- 
  its2seq_metadata_alpha %>% 
  kruskal.test(observed_its2seqs ~ comple_species, data = .)

write_csv(its2seq_metadata_alpha_gc_kw %>% unclass() %>% as_tibble(),
          '../results/its2seq_metadata_alpha_gc_kw.csv')
```
