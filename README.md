# fastmorph v5

Fast corpus search engine originally made for the [Corpus of Written Tatar] (http://corpus.tatar/en) language.

You can try it [here] (http://corpus.tatar/index_en.php?openinframe=search/index_en.html).

Source code is available at https://github.com/mansayk/fastmorph.


## Features
- Advanced search options based on any combination of different search parameters:
   * word form
   * lemma
   * set of morphological tags
   * pattern matching (currently "*" and "?" masks are supported)
   * case matching
   * distance to the next word
- It receives search queries over UNIX Domain Socket file in JSON format.


## Some speed tests
### Tests performed on machine with following characteristics:
* CPU: AMD FX-4100 Quad-Core Processor
* RAM: 16 Gb
* OS: CentOS release 6.8 (Final)
* fastmorph: compiled with 4 threads support, x64
* Corpus size: 116 mln word occurences (140 mln tokens)
* Return full sentences with sources: 100


### Test results for different types of queries:
```
Query:
   Word 1: китап
Number of occurences: 32209
Query processing time: 0,4 sec.
```
```
Query:
   Word 1 (case sensitive, distance to the next word up to 3 words): Китап
   Word 2 (if in brackets, then it is lemma): (бир)
Number of occurences: 15
Query processing time: 0,4 sec.
```
```
Quite heavy query:
   Word 1 (word begins with "б" letter, distance range to the next word is from 1 to 10): б*
   Word 2 (pronoun, word ends with "ң", distance range to the next word is from 1 to 10): <prn>*ң
   Word 3 (lemma "кил", word ends with "р"): (кил)*р
Number of occurences: 135210
Query processing time: 0,8 sec.
```
```
Very heavy query:
   Word 1 (word ends with "ы", distance range to the next word is from 1 to 100): *ы
   Word 2 (word ends with "а", distance range to the next word is from 1 to 100): *а
   Word 3 (word ends with "м", distance range to the next word is from 1 to 100): *м
   Word 4 (word ends with "с", distance range to the next word is from 1 to 100): *с
   Word 5 (word ends with "ь", distance range to the next word is from 1 to 100): *ь
   Word 6 (word ends with "е"): *е
Number of occurences: 135210
Query processing time: 1,4 sec.
```


## System Requirements
- OS: tested on different Linux x86-64 distributions.
- RAM: about 800 Mb for the 100 mln word corpus.
- CPU: 64-bit multicore processors are recommended because of multithreading support.
- MySQL: program loads all data from MySQL database.
- UNIX Domain Socket support by OS.


## Dependencies for compilation
* [jsmn] (https://github.com/zserge/jsmn) is a minimalistic JSON parser in C.
* MySQL C API is a C-based API that client applications written in C can use to communicate with MySQL Server.


## Using
You can try it [here] (http://corpus.tatar/index_en.php?openinframe=search/index_en.html).
There are different search examples in our [Corpus' manual] (http://corpus.tatar/manual/tatcorpus_instruction_eng.pdf).
If you have any questions about using [fastmorph] (https://github.com/mansayk/fastmorph) in your projects, please contact us by [tatcorpus@gmail.com] (mailto:tatcorpus@gmail.com).  
Also we ask you to let us know where this search engine is used and, if you don't mind, we will publish here links to those projects.


## License
This software is distributed under GNU General Public License v3.0.


## JSON
Search query: 
```
Schematical view: {<adj>}(0) 1-5 {ке*<n>}(1) 1-1 {(кил)}(0) 1-1 {}(0) 1-1 {}(0) 1-1 {}(0)  
Detailed:  
   Word 1 (distance range to the next word is from 1 to 5, adjective): <adj>  
   Word 2 (case sensitive, begins with "ке", noun): ке*<n>  
   Word 3: (lemma "кил"):(кил)  
   Word 4:  
   Word 5:  
   Word 6:  
```


### Input format
```
{  
  "word": [  
    "",  
    "",  
    "",  
    "",
    "",
    ""  
  ],  
  "lemma": [  
    "",  
    "",  
    "кил",  
    "",
    "",
    ""  
  ],  
  "tags": [  
    "<adj>",  
    "<n>",  
    "",  
    "",  
    "",
    ""  
  ],  
  "wildmatch": [  
    "",  
    "ке*",  
    "",  
    "",
    "",
    ""  
  ],  
  "case": [  
    0,  
    1,  
    0, 
    0,
    0,
    0  
  ],  
  "dist_from": [  
    1,  
    1,  
    1, 
    1,
    1  
  ],  
  "dist_to": [  
    5,  
    1,  
    1,
    1,
    1  
  ],  
  "return": 100,  
  "last_pos": "0"  
}  
```
**"return"** - maximum amount of sentences to return.  
**"last_pos"** - "0" for the first query or just return back this string to get the next list of sentences.  

**Warning!** You should normalize and verify input data before passing it to fastmorph:  
- remove all not allowed symbols;
- check string legths, numbers correctness and so on.


### Output format
```
{  
  "example": [  
    {  
      "id": 15853,  
      "source": "\"2013 Универсиадасы блогы\" (web-сайт)",  
      "source_type": "kazan2013.ru",  
      "sentence": "Универсиада кебек зур проектның бер өлеше булу өчен, Казанга Россиянең төрле  
        төбәкләреннән һәм Дөньяның  
        <span id='found_word_0' class='found_word' title='(төрле) <adj>'>төрле</span>  
        илләреннән бик күп  
        <span id='found_word_1' class='found_word' title='(кеше) <n>,<nom>,<sg>'>кеше</span>  
        <span id='found_word_2' class='found_word' title='(кил) <ifi>,<iv>,<p3>,<sg>,<v>'>килде</span>."  
    },  
    {  
      "id": -1  
    }  
  ],  
  "last_pos": "892447x39311905x75980782x114356633",  
  "found_all": 1359  
}  
```
As you can see, each word matching the search query is returned in the following HTML tags:  
```
<span id='found_word_0' class='found_word' title='(LEMMA) <TAG1><TAG2>'>FOUND_WORD</span>
```
so, for example, you can use CSS for highlighting them.


## MySQL database format

You can find CREATE TABLE examples [here] (https://github.com/mansayk/fastmorph/blob/master/mysql_create_table.txt).

mysql> select * from morph6_main_apertium limit 10;  
  
| id | united  | sentence | source |  
| --: | ------: | -------: | -----: |  
|  0 | 1594501 |        1 |      1 |  
|  1 |  761564 |        1 |      1 |  
|  2 |  787834 |        1 |      1 |  
|  3 | 1505641 |        1 |      1 |  
|  4 |  420024 |        1 |      1 |  
|  5 |  764201 |        1 |      1 |  
|  6 | 1003674 |        1 |      1 |  
|  7 | 1003851 |        1 |      1 |  
|  8 |  764201 |        1 |      1 |  
|  9 | 1057551 |        1 |      1 |  
  
mysql> select * from morph6_united_apertium where id >= 100 limit 10;  
  
| id  | freq | word_case | word   | lemma  | tags |  
| --: | ---: | --------: | -----: | -----: | ---: |  
| 100 |    1 |   1000084 | 599888 | 429156 |    2 |  
| 101 |   60 |   1000085 | 599890 | 429158 |    2 |  
| 102 |    5 |   1000086 | 599891 | 429159 |    2 |  
| 103 |    2 |   1000087 | 599892 | 429160 |    2 |  
| 104 |    1 |   1000088 | 599893 | 429161 |    2 |  
| 105 |   10 |   1000089 | 599894 | 429162 |    2 |  
| 106 |    1 |    100008 | 164606 | 119768 |    2 |  
| 107 |    1 |   1000090 | 599895 | 429163 |    2 |  
| 108 |    5 |   1000091 | 599899 | 429167 |    2 |  
| 109 |    1 |   1000092 | 599901 | 429169 |    2 |  
  
mysql> select * from morph6_words_case_apertium where id > 200000 limit 10;  
  
| id     | freq | word_case                |  
| -----: | ---: | ------------------------ |  
| 200001 |    4 | Идарәсендәге             |  
| 200002 |    1 | Идарәсендәме             |  
| 200003 |    3 | Идарәсене                |  
| 200004 |  290 | Идарәсенең               |  
| 200005 |   14 | Идарәсеннән              |  
| 200006 |    1 | Идарәсеның               |  
| 200007 |   79 | Идарәсенә                |  
| 200008 |    1 | Идарәснең                |  
| 200009 |    1 | Идарәсәнең               |  
| 200010 |    1 | Идарәханә                |  
  
mysql> select * from morph6_words_apertium where id > 100000 limit 10;  
  
| id     | freq | word                                    |  
| -----: | ---: | --------------------------------------- |  
| 100001 |  975 | артистларны                             |  
| 100002 |    7 | артистларны гына                        |  
| 100003 |   74 | артистларны да                          |  
| 100004 |    1 | артистларныкы                           |  
| 100005 |    1 | артистларныкыдай                        |  
| 100006 |    8 | артистларныкыннан                       |  
| 100007 |    1 | артистларныкыннан да                    |  
| 100008 |    1 | артистларныкыча                         |  
| 100009 | 1408 | артистларның                            |  
| 100010 |    3 | артистларның гына                       |  
  
mysql> select * from morph6_lemmas_apertium where id > 300000 limit 10;  
  
| id     | freq | lemma                      |  
| -----: | ---: | -------------------------- |  
| 300001 |    1 | иярүчелек                  |  
| 300002 |  130 | иярүчеләр                  |  
| 300003 |    8 | иярүчеләргә                |  
| 300004 |    2 | иярүчеләрдә                |  
| 300005 |    3 | иярүчеләрдән               |  
| 300006 |    9 | иярүчеләре                 |  
| 300007 |    2 | иярүчеләрегез              |  
| 300008 |    2 | иярүчеләрен                |  
| 300009 |    1 | иярүчеләренең              |  
| 300010 |   12 | иярүчеләрне                |  
  
mysql> select * from morph6_tags_apertium where id > 11100 limit 10;  
  
| id    | freq  | combinations                       |  
| ----: | ----: | ---------------------------------- |  
| 11101 |     4 | \<ant\>,\<dat\>,\<f\>,\<frm\>,\<np\>,\<px2sg\> |  
| 11102 | 17141 | \<ant\>,\<dat\>,\<f\>,\<np\>               |  
| 11103 |   387 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<pl\>          |  
| 11104 |     1 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<pl\>,\<px1pl\>  |  
| 11105 |     1 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<pl\>,\<px1sg\>  |  
| 11106 |    12 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<pl\>,\<px3sp\>  |  
| 11107 |     1 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<pl\>,\<px\>     |  
| 11108 |    40 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<px1pl\>       |  
| 11109 |   101 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<px1sg\>       |  
| 11110 |    41 | \<ant\>,\<dat\>,\<f\>,\<np\>,\<px2sg\>       |  
  
mysql> select * from sources where col1 > 300 limit 3;  
  
| col1 |col2 | col3 |
| ---: | --- | ---- |
|  301 | "miras.belem.ru" (web-сайт)                                                                   | miras.belem.ru |
|  302 | Абдулла Алиш. Куршеләр                                                                        | book           |
|  303 | Дәүләт хакимияте һәм җирле үзидарә органнарының бердәм "Рәсми Татарстан" порталы (web-сайт)   | tatarstan.ru   |


## Apertium
If you use [Apertium's] (http://wiki.apertium.org/wiki/Publications) tagger to morphologically annotate the corpus, then you can use our Python script to generate tables from Apertium's output.  

To use this converter you should:  
1) Annotate your corpus using Apertium's tagger:  
```
cat bigfile.txt | apertium -n -d . tat-tagger | cg-proc dev/mansur.bin > bigfile_tagged.txt
```
where **mansur.bin** is just a file with some additional rules. You can find it [here] (https://svn.code.sf.net/p/apertium/svn/languages/apertium-tat/dev/).  
In result you should get file, containing annotated sentences:  
```
^Мин/Мин<prn><pers><p1><sg><nom>$ ^үземне/үз<prn><ref><px1sg><acc>$ ^белә/бел<v><tv><prc_impf>$ ^башлаганнан/башла<vaux><ger_past><abl>$ ^бирле/бирле<post>$ ^түбән/түбән<adj>$ ^очка/оч<n><sg><dat>$ – ^ерак/ерак<adj>$ ^бабакайларга/бабакай<n><pl><dat>$ ^төшәргә/төш<v><iv><inf>$ ^ярата/ярат<v><tv><pres><p3><sg>$ ^идем/и<cop><ifi><p1><sg>$^./.<sent>$
```
2) Generate "inv_so" text file in the following format:  
  
| sentence id | source id |  
| ------: | ------: |  
|    1 |    1 |  
|    2 |    1 |  
|    3 |    1 |  
|    4 |    1 |  
|    5 |    1 |  
|    6 |    1 |  
|    7 |    1 |  
|    8 |    1 |  
|    9 |    1 |  
|   10 |    1 |  
  
And place it in the same directory with script.  
3) Run the python script this way:  
```
./tat-tagger_to_ntables_v6.24.py tatcorpus2.sentences.apertium.tagged.txt
```  
It will take quite much time according to the size of your corpus.  
4) If everything went well, you should get a list of new files that you need to import to MySQL database:  
```
tatcorpus2.sentences.apertium.tagged.txt.lemmas.output.txt
tatcorpus2.sentences.apertium.tagged.txt.main.output.txt
tatcorpus2.sentences.apertium.tagged.txt.tags-uniq.output.txt.sorted.txt
tatcorpus2.sentences.apertium.tagged.txt.tags.output.txt
tatcorpus2.sentences.apertium.tagged.txt.united.output.txt
tatcorpus2.sentences.apertium.tagged.txt.words.output.txt
tatcorpus2.sentences.apertium.tagged.txt.words_case.output.txt
```  


## ChangeLog:

27.02.2017 - The 5th version of fastmorph corpus search engine is released. Now it consumes about 2,5 times less memory (RAM).

18.11.2016 - The 4th version of fastmorph corpus search engine is released. List of changes:
   - case sensitive search option was added;
   - the memory (RAM) usage by the search system is reduced twice;
   - because of essential changes in the application architecture, search query performs now 3 - 5 times faster.
Technical info: version 4 consumes about 2 Gb RAM for the same corpus.

19.07.2016 - Some improvements in the Complex morphological search engine "fastmorph":
   - in addition to the existing mask "\*", that matches any number of any symbols, the mask "?", that represents any single character, were added. More information about it you can find in the updated Guides;
   - in the technical plan memory usage by the search system is reduced up to 25%.
Technical info: version 3 consumes about 4 Gb RAM for the same corpus.

13.06.2016 - Search by the middle part of a word functionality was added in the fastmorph module. For example, if you type \*әме\*, words like ярдәмендә, бәйрәмен, үткәрәмен, өйдәме will be found...

21.04.2016 - Because of implementation in "fastmorph" module some processor optimizations and multithreading support we achieved that complex morphological search now performs up to five times faster.

03.04.2016 - Complex morphological search system's features were significantly extended. You can get more info about them in [The Guides] (http://corpus.tatar/manual_en.htm) updated up to 3.0 and higher version.

22.02.2016 - Complex morphological search function appeared in The Corpus of Written Tatar, where you can use different combinations of such parameters as wordform, lemma, grammatical tags, beginning and end of words, distances between them.
Technical info: version 1 consumes about 6 Gb RAM for the corpus, consisting of 116 mln word occurences. Its speed is quite high.

