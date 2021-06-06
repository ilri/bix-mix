# How to query the ENA and GenBank database for nucleotide sequences


## Introduction : nucleotide sequence databases

Here, we explore the various keywords and the syntax to use in order to build advanced queries and find sets of **nucleotide sequences** present in the database and abiding by specific criteria.

The public nucleotide sequence databases [**ENA (European Nucleotide Archive)**](https://www.ebi.ac.uk/ena/browser/home) and [**GenBank**](https://www.ncbi.nlm.nih.gov/genbank/) (the Americna database maintained by NCBI) constitute, together with the Japan-based [**DDBJ (DNA DataBank of Japan)**](https://www.ddbj.nig.ac.jp/), the [INSDC international consortium](http://www.insdc.org/) (literally: the *International Nucleotide Sequence Database Collaboration*). These three databases exchange data on a daily basis, so that their contents are synchronized and therefore identical. Thus, whichever nucleotide sequence database one interrogates, one must be able to get the same results. This being said, the way to write database queries differs from one database to another, and that is what we will examine below. As many scientists in Europe and in Africa use either ENA or GenBank, we will focus exclusively on these two databases.


## Where to browse to in order to launch queries on ENA or on GenBank?


**Regarding ENA**, the [database homepage](https://www.ebi.ac.uk/ena/browser/home) enables simple searches through the use of the two entry fields on the top right of the homepage, within the green header. Le field on top, with "Enter text search terms" greyed out in it, enables the user to launch simple queries based on keywods separated by spaces, without any other form of syntactic formatting. The field right below this one, showing "Enter accession" in grey, enables to look for a specific record in the database by inputting its record ID. Such a record can correspond to a sequence record, to a study or to a sample. Using this requires to know the exact **accession number** beforehand.


The structured queries (called "advanced searches") through ENA are performed through the [advanced search module](https://www.ebi.ac.uk/ena/browser/advanced-search) that one can reach via the menus: Search > Advanced Search from the ENA homepage. After entering the "Data type" as being "Nucleotide sequences" (that is the most common data type, encompassing all nucleotide sequences) and after clicking the "Next" button in the bottom right corner of the page, one lands on another page with a "Query:" field in the middle. That is the field we will use to enter the advanced queries we will see below.

**With regards to GenBank**, things are simpler: the [database homepage](https://www.ncbi.nlm.nih.gov/genbank/) contains only one search field at the top of the page, in which the user can either enter space-separated keywords, or more structured queries as the ones we will see below. Be careful, though, to make sure that the drop-down field immediately on the left of the search field indicates "Nucleotide".


## How to build complex queries from simpler, atomic ones: general remarks

In order to perform advanced searches, we will build **logical combinations** of search terms and simple queries : for instance, one could want to find all the sequences corresponding to a given gene within a given group of species and having a minimal length. Writing this means that we want to find the sequences **simultaneously** satisfying the three criteria. Therefore, we musy use a logical "AND" connector. Similarly, one can be looking for sequences of dog **or** of cat DNA, and in that case, one will use a logical "OR" to connect the two sub-queries.

We will write **AND** between two atomic queries to ask for the satisfaction of all criteria simultaneously, and **OR** for an alternative in which results can satisfy only one (or more) of the stated criteria.

To negate a criteria (i.e. to get the sequences **not** satisfying a given criterion), we will prefix the atomic query with **NOT** (e.g "Canis lupus"[ORGN] NOT 1997:2000[PDAT]). Pay attention to the fact that for GenBank queries, there is no "AND" before the "NOT", while in ENA searches, a "NOT" necessarily comes with either an "OR" or and "AND" before it.


In ENA searches, one can also use the infix operator **!=** to mean “not equal to”.


## Syntax of ENA and GenBank queries

In the following, we describe the syntax to abide by when writing ENA and GenBank queries. This is divided in several sections according to the type of criteria according which to filter the nucleotide sequence dababase. In the following tables, each row contains ENA and GenBank queries that are (as much as possible) semantically equivalent.

### Filtering on the date of first publication of the sequences

Please note ENA only accepts exact dates, formatted YYYY-MM-DD. GenBank, though, accepts dates only partially specified, in which only the year is mentioned. To use exact dates in GenBank searches, please note the proper syntax is then YYYY/MM/DD (with forward slashes instead of the dashes used in ENA queries).


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `first_public <= 2000-12-31 AND first_public >= 1997-01-01` | `1997:2000[PDAT]`             | sequences first published between 1997 and 2000 |
| `first_public <= 2021-04-01 AND first_public >= 2021-05-07` | `2021/04/01:2021/05/07[PDAT]` | sequences first published between April 1<sup>st</sup> and May 7<sup>th</sup>, 2021 | 

### Filtering on the date of last modification of the sequences

Sometimes, sequences are modified after their initial publication, for instance when the team who published them figured out they made a mistake. In such a case, the core accession number itself doesn't change, but the version number is incremented. For instance, the database is updated and a sequence moves from accession FR682468.1 to accession FR682468.2. One can be looking for sequences based on their date of last modification: 

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `last_updated >= 2021-04-01 AND last_updated <= 2021-04-27` | `2021/04/01:2021/04/27[MDAT]` | sequences whose record was last modified between April 1<sup>st</sup> and April 27<sup>th</sup>, 2021 |


### Sequence length

Sometimes, one wants to get sequences whose length is above a certain value, or below a given threshold. It is possible to specify such a request through a search. For nucleotide sequences, sequence lengths are always expressed as a number of base pairs or nucleotides. For proteic sequences, the length would be in number of amino acids, of course. We can specify a ceiling or a threshold value, or even a range of acceptable lengths.

On this note, queries to GenBank must always be written with a range of sequence lengths: if one gives only one value, then only sequences whose length is exactly equal to that value will be returned. Therefore, in order to simulate with GenBank a search with only a lower bound, one must give a range whose upper bound is arbitrarily high, as in the example below.



| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `base_count > 500` | `501:10000000[SLEN]` | sequences of length at least equal to 501 nucleotides |
| `base_count >= 5000 AND base_count <= 10000` | `5000:10000[SLEN]` | sequences whose length falls between 5kb and 10kb |
| `base_count <= 5000` | `0:5000[SLEN]` | sequences shorter than or equal to 5kb |
| `base_count=537` | `537[SLEN]` | sequences of length exactly equal to 537 nucleotides |


### Taxonomic divisions

Sequence data within the INSCDC databases are organised into a number of large taxonomic divisions. To each of these divisions corresponds a three-letter code, and each published sequence appears in a single division. This information features on the first line ("ID" tag) of an EMBL file, or on the first line ("LOCUS" tag) of a file in the GenBank format. For instance, the "VRL" code in the [EMBL file](https://www.ebi.ac.uk/ena/browser/api/embl/MK940252.1?lineLimit=1000) or in the [GenBank file](https://www.ncbi.nlm.nih.gov/nuccore/MK940252) for the sequence with accession MK940252 indicates that sequence is from a non-bacteriophage virus.


The taxonomic divisions in the INSDC databases owe a lot to the historical conditions of the development of the GenBank database, and are not meant to host equal number of the extant taxa. For instant, human (within the "PRI" division) and mouse species (within the "ROD" division) used to account for a large number of published sequences, and therefore they almost have dedicated divisions. The 13 INSDC taxonomic divisions are as follows:


| three-letter code | taxonomic division |
| -------- | ------- |
| PRI | all primate species, including *Homo sapiens* |
| ROD | rodents: mice, rats, etc |
| MAM | all other mammals |
| VRT | all other vertebrates (non-mammals!) |
| INV | invertebrates |
| PLN | plants and fungi |
| BCT | bacteria |
| PHG | bacteriophages |
| VRL | all other viruses ("virological") |
| ENV | environmental sequences (metagenomics) |
| SYN | synthetic sequences (incl. chimeric ones) |
| PAT | patented sequences |
| UNA | unannotates sequences (rare) |


The syntax to perform a search based on the taxonomic division of the sequences is a bit more cumbersome with GenBanks than with ENA:


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_division="VRL"` | `gbdiv_vrl[PROP]` | viral sequences except bacteriophages |
| `tax_division="PLN"` | `gbdiv_pln[PROP]` | plant and fungi sequences |


### Looking for one taxon or several taxa

The most intuitive way to search sequences based on the taxon they should come from, consists in indicating the binomial scientific name of that taxon. For instance, the domestic dog and the wolf pertain to one species called *Canis lupus*, and the maize or corn is called *Zea mays*.

Note that in the GenBank queries, the keyword `ORGN` stands for "organism".

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Canis lupus")` | `"Canis lupus"[ORGN]` | sequences for which a source taxon name contains the words “Canis lupus” |

Warning! The ENA query `tax_name("Canis lupus")` is strictly identical to `tax_eq(9612)` (see below): the name indicated between quotes must correspond exactly to a taxon name, while GenBank does some *partial pattern matching*. This means that the sequences belonging to *Canis lupus lycaon* will be included in the results of the above search with GenBank, but not in the results of the search through ENA as written above.


Besides, in the case where there are several source organisms (for instance in the case of some synthetic DNA), the GenBank keyword `PORGN` designates the "source organism" first mentioned in the GenBank file (that can be for instance "synthetic construct"), while `ORGN` designates any of the taxa that are "DNA contributors" to a sequence. In all other cases, `PORGN`is strictly equivalent to `ORGN`. An example of such a synthetic sequence, obtained through the GenBank search `"Homo sapiens"[ORGN] NOT "Homo sapiens"[PORGN]`, is the [sequence with accession MG272208](https://www.ncbi.nlm.nih.gov/nuccore/MG272208.1).



| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `"Canis lupus"[PORGN]` | sequences for which the primary taxon name contains "Canis lupus" |


Every single taxon that is present in the Tree of Life, whatever its position may be, possesses a unique numeric code (called "taxid") identifying it unambiguously. Such a numeric code can be found via a search through the [NCBI Taxonomy](https://www.ncbi.nlm.nih.gov/taxonomy). Once the code is known, one can ask for sequences originating in that specific taxon.


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_eq(1773)` | *?* | sequences from *Mycobacterium tuberculosis*, a species whose [taxid is 1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773) |

Warning! The searches formed using the `tax_eq()` predicate do not yield the sequences placed in the taxonomy *below* the stated taxon (above: *Mycobacterium tuberculosis*, with taxonomic ID [1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773)). For instance, the above ENA query **will not** output the sequences from “*Mycobacterium tuberculosis* variant bovis”. Please see below for another query outputting all the sequences corresponding to a given phylogenetic clade.


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_tree(1773)` | `zzz[orgn:__txid1773]` | sequences from *Mycobacterium tuberculosis* and all the clade whose root taxon is *M. tb* |

Please note that in the GenBank query above, the token that appears just on the left of the left square bracket is meaningless. But there needs to be something there, and writing only `[orgn:__txid1773]` will not work. Please also note there are two consecutive underscore characters (`_`) in this query.

### Searching through sequence descriptions using keyowrds

All INSDC sequence records contain a free text descriptive field. Appearing under the "DE" tag in a file in EMBL format, or on the line with the "DESCRIPTION" tag in a GenBank file, this is sometimes referred to as the "title" of the record. Searches based on words found within that field are performed as below.

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `description="*RPOB*"` | `RPOB[TITL]` | records containing “RPOB” (case-insensitive) anywhere in the description (“title” of the record) |


### Searching based on the cellular compartment where the DNA was isolated

In all eukaryotes, most of the genomic material is found in the kernel of the cells, but in some instances there is also DNA in the mitochondria, in the chloroplasts, in plasmids, etc. It is possible to filter the database records and get only the sequences belonging to a specific cellular compartment:


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `organelle="mitochondrion"` | `gene_in_mitochondrion[PROP]` | mitochondrial DNA sequences |
| `organelle != "mitochondrion" AND mol_type="genomic DNA"` | `gene_in_genomic[PROP]` | nuclear DNA sequences |


### Filtering by type of database or on the origin database

When searching through GenBank, one can wish to exclude the [RefSeq sequences](https://www.ncbi.nlm.nih.gov/refseq/about/), which are already present as "base" records elsewhere in GenBank. The RefSeq set is a very selective subset of reference sequences that is maintained by NCBI/GenBank.

It is also possible to ask for sequences that were initially uploaded to GenBank, or to ENA, or to DDBJ, before being synchronized with all these INSDC databases.

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `srcdb_genbank[PROP]` | only the sequences whose initial upload was to GenBank |
| *?* | `NOT refseq[FILTER]` | do not include the NCBI RefSeq set (which is already the default behaviour with ENA queries, since RefSeq is an NCBI thing) |
| *?* | `srcdb_ddbj/embl/genbank[PROP]` | similar to the filtering by `NOT refseq[FILTER]`: original sequences |

### Filtering by study

In ENA, sequences are necessarily published as part of a research **study**. Therefore, it is possible to ask for all the sequences published within the framework of such a study:


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `study_accession="PRJEB402"` | *?* | all the sequences published as part of the study with identifier PRJEB402 |


## Complex queries

Above was a brief tour of the various types of atomic queries one can issue to ENA or to GenBank. As we said at the beginning of the present document, it is possible to build more complex queries by combining several atomic ones through the use of the `AND`, `OR` and `NOT` logical connectors:


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Sus scrofa") AND organelle="mitochondrion" AND base_count > 10000 AND description="*complete*"` | `"Sus scrofa"[ORGN] AND gene_in_mitochondrion[PROP] AND 10000:5000000000[SLEN] AND "complete"[TITL] NOT refseq[FILTER]` | pig mitochondrial sequences long of at least 10kb and whose description contains the word "complete" |


