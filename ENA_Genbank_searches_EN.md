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

On this note, queries to GenBank must always be written with a range of sequence lengths: if one gives only one value, then only sequences whose length is exactly equal to that value will be returned. Therefore, in order to simulate with GenBank a search with only a lower bound, one must give a range whose upper bound is arbitrarily high, as is the case below.



| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `base_count > 500` | `501:10000000[SLEN]` | sequences of length at least equal to 501 nucleotides |
| `base_count >= 5000 AND base_count <= 10000` | `5000:10000[SLEN]` | sequences whose length falls between 5kb and 10kb |
| `base_count <= 5000` | `0:5000[SLEN]` | sequences shorter than or equal to 5kb |
| `base_count=537` | `537[SLEN]` | sequences of length exactly equal to 537 nucleotides |


### Division taxonomique

Les bases de données INSDC contiennent un certain nombre de grandes divisions taxonomiques. À chacune de ces divisions correspond un code sur trois caractères, et chaque séquence publiée apparaît dans une et une seule division. Cette dernière apparaît sur la première ligne (ID) d'un fichier au format EMBL, ou sur la premirère ligne (LOCUS) d'une fichier au format GenBank. Par exemple, le code "VRL" dans le [fichier EMBL](https://www.ebi.ac.uk/ena/browser/api/embl/MK940252.1?lineLimit=1000) ou dans le [fichier GenBank](https://www.ncbi.nlm.nih.gov/nuccore/MK940252) de la séquence d'accession MK940252 nous indique qu'il s'agit d'une séquence de virus. 

Les divisions taxonomiques des bases INSDC doivent beaucoup à l'historique de la formation de GenBank, et ne sont pas destinées à inclure un nombre égal de taxa contemporains. Elles sont comme suit :

| code à trois lettres | division taxonomique |
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
| `tax_division="VRL"` | `gbdiv_vrl[PROP]` | viral sequences expect bacteriophages |
| `tax_division="PLN"` | `gbdiv_pln[PROP]` | plant and fungi sequences |


### Recherches par taxon/taxa

La manière la plus intuitive de faire des recherches par taxon consiste à indiquer le nom scientifique latin (dit "binomial") du taxon. Par exemple, le chien domestique et le loup sont regroupés au sein de l'espèce *Canis lupus*. Dans les requêtes GenBank, le mot-clef `ORGN` vient du mot "organism" ("organisme" en français) :

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Canis lupus")` | `"Canis lupus"[ORGN]` | sequences for which a source taxon name contains “Canis lupus” |

Attention ! La requête ENA `tax_name("Canis lupus")` est strictement équivalente à `tax_eq(9612)` (voir ci-dessous) : le nom fourni doit correspondre exactement à celui d’un taxon, alors que Genbank fait du *pattern matching*. C'est-à-dire que “Canis lupus lycaon” est inclus dans les résultats de la requête NCBI, pas dans ceux de la requête ENA telle qu’écrite ici.

D'autre part, dans le cas où il y a plusieurs organismes sources (par exemple dans le cas d'une séquence ADN de synthèse), le mot-clef GenBank `PORGN` désigne l'"organisme" source cité en premier lieu dans le fichier GenBank (par exemple "synthetic construct"), alors que `ORGN` désigne le ou l'un des "donneur(s)" d'ADN. Dans tous les autres cas, `PORGN` est strictement identique à `ORGN`. Un exemple d'une telle séquence synthétique, obtenue de GenBank par la recherche `"Homo sapiens"[ORGN] NOT "Homo sapiens"[PORGN]`, est la [séquence d'accession MG272208](https://www.ncbi.nlm.nih.gov/nuccore/MG272208.1).


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `"Canis lupus"[PORGN]` | sequences for which the primary taxon name contains "Canis lupus" |


Tout taxon, quelle que soit sa position dans l'arbre du Vivant, possède un code numérique l'identifiant de manière non équivoque. On peut obtenir un tel code via une recherche dans [NCBI Taxonomy](https://www.ncbi.nlm.nih.gov/taxonomy). Une fois le code connu, on peut demander les séquences correspondant exactement à notre taxon.


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_eq(1773)` | *?* | séquences issues de *Mycobacterium tuberculosis*, espèce dont [le taxid est 1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773) |


Attention ! Les requêtes formées à l'aide du prédicat `tax_eq()` ne renvoient pas les séquences taxonomiquement “en dessous” du taxon donné (ci-dessus *Mycobacterium tuberculosis*, dont l'ID taxonomique est [1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773)). Par exemple la requête ci-dessus ne produira pas les séquences de “*Mycobacterium tuberculosis* variant bovis”. Voir ci-dessous pour une autre requête renvoyant toutes les séquences correspondant à tout un clade phylogénétique.


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_tree(1773)` | `zzz[orgn:__txid1773]` | séquences issues de *Mycobacterium tuberculosis* et de tout le clade dont *M. tb* est la racine |

Notez bien que dans la requête Genbank, ce qu’on écrit à gauche du crochet n’a pas d’importance, mais il faut qu’il y ait quelque chose.

### Recherche avec mots-clefs dans la description de la séquence

Tous les enregistrements INSDC contiennnent un champ descriptif en texte libre. C'est le champ apparaissant dans un fichier EMBL à la balise "DE", ou sur la ligne "DEFINITION" d'un fichier au format GenBank, aussi appelé parfois le champ de "titre" d'un enregistrement. On peut rechercher un mot-dlef dans ce champ à l'aide des requêtes ci-dessous.


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `description="*RPOB*"` | `RPOB[TITL]` | séquences avec “RPOB” (recherche insensible à la casse des caractères) apparaissant n’importe où dans le champ de description (“titre”) |


### Recherche en fonction du compartiment cellulaire où se trouve l'ADN

Chez les eukaryotes, la plupart du matériel génétique se trouve dans le noyau, mais il y a aussi selon le cas de l'ADN dans les mitochondries, dans les plasmides, etc. On peut demander à voir uniquement les séquences correspondant à un compartiment cellulaire bien précis :


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `organelle="mitochondrion"` | `gene_in_mitochondrion[PROP]` | séquences d'ADN mitochondrial et non nucléaire |
| `organelle != "mitochondrion" AND mol_type="genomic DNA"` | `gene_in_genomic[PROP]` | séquences d'ADN du noyau |


### Filtrage par type de base de données

On peut vouloir exclure les données RefSeq, déjà présentes ailleurs dans GenBank mais constituant un sous-ensemble très sélectif de séquences de référence, ou bien demander les séquences qui ont été initialement publiées ou bien dans GenBank, ou bien dans ENA, ou bien dans DDBJ :


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `srcdb_genbank[PROP]` | ne prendre que les données initialement soumises via Genbank |
| *?* | `NOT refseq[FILTER]` | ne pas inclure les données du jeu RefSeq (ce qui est déjà le comportement par défaut pour les requêtes ENA) |
| *?* | `srcdb_ddbj/embl/genbank[PROP]` | un peu la même chose que de filtrer par `NOT refseq[FILTER]` : séquences originales |

### Filtrage par projet/étude

Dans ENA, les séquences publiées le sont nécessairement au sein d'un **projet ou étude ("study")**. Ainsi, on peut demander à voir toutes les séquences publiées dans le cadre d'une telle étude :


| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `study_accession="PRJEB402"` | *?* | trouver toutes les séquences issues du projet d’identifiant PRJEB402 |


## Requêtes complexes

Voilà, nous avons fait un bref tour d'horizon des requêtes dans ENA et dans GenBank. Comme nous l'avions annoncé en ouverture de ce document, on peut construire des requêtes de base en combinant les opérateurs avec des "ET" ou des "OU" logiques :

| ENA syntax (Advanced Search)                               | GenBank syntax               | meaning |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Sus scrofa") AND organelle="mitochondrion" AND base_count > 10000 AND description="*complete*"` |  | séquences mitochondriales de porc d'au moins 10kb et contenant le mot "complete" dans leur description |


