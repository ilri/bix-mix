# Chercher efficacement dans les bases ENA et GenBank


## Introduction : les bases de données de séquences

Dans ce document, nous explorons les différents mots-clefs et la syntaxe à utiliser pour construire des recherches afin de trouver un ensemble de **séquences nucléotidiques** présentes dans la base de données et correspondant à des critères précis.

Les bases de données de séquences [**ENA (European Nucleotide Archive)**](https://www.ebi.ac.uk/ena/browser/home) et [**GenBank**](https://www.ncbi.nlm.nih.gov/genbank/) (base de données du NCBI aux États-Unis) sont, avec la japonaise [**DDBJ (DNA DataBank of Japan)**](https://www.ddbj.nig.ac.jp/), regroupées dans le [consortium international INSDC](http://www.insdc.org/) (très littéralement *International Nucleotide Sequence Database Collaboration*). Les trois bases de données échangent des données de façon quotidienne, de manière à ce que leur contenu soit synchronisé et donc identique. Ainsi, quelle que soit la base de données de séquences nucléotidiques qu'on interroge, on doit obtenir les mêmes résultats. Ceci dit, la façon d'écrire des requêtes interrogeant ces bases de données diffère d'une base à l'autre, et c'est ce que nous allons voir ici. Comme la plupart des chercheurs en Europe et en Afrique utilisent soit ENA, soit GenBank, c'est sur ces deux bases que nous allons nous focaliser.


## Où se rendre pour effectuer des requêtes sur ENA ou sur GenBank?

**En ce qui concerne ENA**, la [page d'accueil de la base](https://www.ebi.ac.uk/ena/browser/home) permet de faire des recherches simples en utilisant l'un ou l'autre des deux champs en blanc en haut à droite de la page d'accueil, dans le bandeau vert. Le champ du haut, avec "Enter text search terms" en grisé, permet de lancer des recherches simples à base de mots-clefs séparés par des espaces, sans mise en forme particulière. Le champ situé immédiatement en dessous, montrant "Enter accession" en grisé, permet de rechercher un enregistrement précis dans la base de données, correspondant à une séquence, à un projet, ou à un échantillon. Il faut pour cela connaître à l'avance le **numéro d'accession** exact.

Les recherches structurées (dites "avancées") à travers ENA se font à partir du [module de recherche avancée](https://www.ebi.ac.uk/ena/browser/advanced-search) accessible à travers les menus Search > Advanced Search depuis la page d'accueil d'ENA. Après avoir renseigné le "Data type" comme étant "Nucleotide sequences" (le type de données le plus générique englobant toutes les séquences nucléotidiques) et après avoir cliqué sur le bouton "Next" en bas à droite de la page, on atterrit sur la page avec le champ "Query:" au milieu, dans lequel on pourra entrer les requêtes complexes que nous allons voir ci-après.

**En ce qui concerne GenBank**, les choses sont plus simples: la [page d'accueil de la base](https://www.ncbi.nlm.nih.gov/genbank/) contient un seul champ de recherche en haut de page, dans lequel on peut rentrer soit des mots-clefs "désarticulés" séparés par des espaces, soit des requêtes plus structurées comme celles que nous allons voir plus loin.


## Remarques générales sur la construction de requêtes complexes à partir de sous-requêtes atomiques

Pour faire des recherches avancées, nous allons construire des combinaisons logiques associant des termes de recherche ou sous-requêtes plus simples : par exemple, on pourra vouloir chercher les séquences correspondant à un gène donné au sein d'un groupe d'espèces données et ayant une longueur minimale donnée. Lorsque j'écris ça, je sous-entend que je souhaite trouver les séquences satisfaisant **simultanément** ces trois critères. C'est donc un "ET" logique que je devrai employer. De la même manière, on peut souhaiter trouver des séquences de chien **ou bien** de chat, et alors c'est un "OU" logique qu'on emploiera pour lier nos deux sous-requêtes.

Il faudra utiliser l’opérateur **AND** pour construire des ET logiques (satisfaction de toutes les conditions simultanément) ou l’opérateur **OR** pour des OU logiques.

La négation s’écrit seulement **NOT** en préfixe chez GenBank (e.g "Canis lupus"[ORGN] NOT 1997:2000[PDAT]), alors que chez ENA il faut obligatoirement faire précéder le mot-clef “NOT” d’un “AND” ou d’un “OR”.


Avec ENA, on peut aussi utiliser l’opérateur “!=” pour dire “différent de”.


## Syntaxe des requêtes dans ENA et dans GenBank

On décrit dans ce qui suit la syntaxe des requêtes GenBank et ENA, dans plusieurs sections organisées selon la nature du filtrage effectué dans la base de données. Dans les tableaux qui suivent, chaque ligne contient des requêtes ENA et GenBank sémantiquement identiques.

### Filtrage sur la date de première publication des séquences

Il est à noter qu'ENA n'accepte que des dates exactes, au format AAAA-MM-JJ. GenBank, en revanche, accepte des dates partiellement spécifiées, en n'indiquant que l'année. Si vous utilisez des dates exactes avec GenBank, notez que la syntaxe est AAAA/MM/JJ (avec des barres obliques et non des tirets comme avec ENA.


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `first_public <= 2000-12-31 AND first_public >= 1997-01-01` | `1997:2000[PDAT]`             | séquences publiées entre 1997 et 2000 |
| `first_public <= 2021-04-01 AND first_public >= 2021-05-07` | `2021/04/01:2021/05/07[PDAT]` | séquences publiées entre le 1<sup>er</sup> avril et le 7 mai 2021 | 

### Filtrage sur la date de dernière modification des séquences

Il arrive que des séquences publiées soient modifiées par la suite, lorsque par exemple l'équipe qui les a publiées s'est rendu compte d'une erreur. Dans ce cas, le numéro d'accession en principal ne change pas, mais le numéro de version se trouve incrémenté. Par exemple, on passe de l'accession FR682468.1 à l'accession FR682468.2. On peut vouloir chercher des séquences en fonction de leur date de dernière modification :


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `last_updated >= 2021-04-01 AND last_updated <= 2021-04-27` | `2021/04/01:2021/04/27[MDAT]` | séquences dont l’enregistrement a été modifié pour la dernière fois entre le 1<sup>er</sup> et le 27 avril 2021 |


### Longueur des séquences

On peut vouloir ne retenir que des séquences plus longues (ou plus courtes) qu'une certaine longueur. Les longueurs de séquences sont toujours exprimées en nombre de nucléotides (en ce qui concerne les séquences nucléotidiques, bien sûr, qui sont les seules dont nous parlons ici. Pour les séquences protéiques, la longueur serait exprimée en nombre d'acides aminés). On peut aussi donner une plage de tailles de séquence.

D'ailleurs, les requêtes GenBank doivent toujours être écrites avec une plage de tailles de séquence : si l'on ne donne qu'une valeur, alors les résultats de la requête ne produiront que des séquences de longueur exactement égale à ladite valeur. Ainsi, pour simuler avec GenBank une recherche avec seulement une borne inférieure, on droit donner une plage de valeurs avec une borne supérieure arbitrairement élevée, comme ci-dessous.



| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `base_count > 500` | `501:10000000[SLEN]` | séquences de longueur au moins 501 nucléotides |
| `base_count >= 5000 AND base_count <= 10000` | `5000:10000[SLEN]` | séquences de longueur comprise entre 5kb et 10kb |
| `base_count <= 5000` | `0:5000[SLEN]` | séquences de longueur inférieure ou égale à 5kb |


### Division taxonomique

Les bases de données INSDC contiennent un certain nombre de grandes divisions taxonomiques. À chacune de ces divisions correspond un code sur trois caractères, et chaque séquence publiée apparaît dans une et une seule division. Cette dernière apparaît sur la première ligne (ID) d'un fichier au format EMBL, ou sur la premirère ligne (LOCUS) d'une fichier au format GenBank. Par exemple, le code "VRL" dans le [fichier EMBL](https://www.ebi.ac.uk/ena/browser/api/embl/MK940252.1?lineLimit=1000) ou dans le [fichier GenBank](https://www.ncbi.nlm.nih.gov/nuccore/MK940252) de la séquence d'accession MK940252 nous indique qu'il s'agit d'une séquence de virus. 

Les divisions taxonomiques des bases INSDC doivent beaucoup à l'historique de la formation de GenBank, et ne sont pas destinées à inclure un nombre égal de taxa contemporains. Elles sont comme suit :

| code à trois lettres | division taxonomique |
| -------- | ------- |
| PRI | tous les primates, y compris *Homo sapiens* |
| ROD | rongeurs ("rodents") : souris, rats, etc |
| MAM | tous les autres mammifères |
| VRT | tous les autres vertébrés (non mammifères) |
| INV | invertébrés |
| PLN | plantes et champignons (fungi) |
| BCT | bactéries |
| PHG | bactériophages |
| VRL | tous les autres virus ("virological") |
| ENV | séquences environnementales (métagénomique) |
| SYN | séquences synthétiques et chimériques |
| PAT | séquences brevetées (brevet = "patent" en anglais) |
| UNA | séquences non annotées (rare) |


La syntaxe pour effectuer une recherche en fonction de la division taxonomique des séquences est un peu plus scabreuse avec GenBank qu'avec ENA :


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_division="VRL"` | `gbdiv_vrl[PROP]` | séquences virales hors bactériophages |
| `tax_division="PLN"` | `gbdiv_pln[PROP]` | séquences de plantes et de champignons |


### Recherches par taxon/taxa

La manière la plus intuitive de faire des recherches par taxon consiste à indiquer le nom scientifique latin (dit "binomial") du taxon. Par exemple, le chien domestique et le loup sont regroupés au sein de l'espèce *Canis lupus*. Dans les requêtes GenBank, le mot-clef `ORGN` vient du mot "organism" ("organisme" en français) :

| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Canis lupus")` | `"Canis lupus"[ORGN]` | séquences pour lesquelles un nom de taxon source contient “Canis lupus” |

Attention ! La requête ENA `tax_name("Canis lupus")` est strictement équivalente à `tax_eq(9612)` (voir ci-dessous) : le nom fourni doit correspondre exactement à celui d’un taxon, alors que Genbank fait du *pattern matching*. C'est-à-dire que “Canis lupus lycaon” est inclus dans les résultats de la requête NCBI, pas dans ceux de la requête ENA telle qu’écrite ici.

D'autre part, dans le cas où il y a plusieurs organismes sources (par exemple dans le cas d'une séquence ADN de synthèse), le mot-clef GenBank `PORGN` désigne l'"organisme" source cité en premier lieu dans le fichier GenBank (par exemple "synthetic construct"), alors que `ORGN` désigne le ou l'un des "donneur(s)" d'ADN. Dans tous les autres cas, `PORGN` est strictement identique à `ORGN`. Un exemple d'une telle séquence synthétique, obtenue de GenBank par la recherche `"Homo sapiens"[ORGN] NOT "Homo sapiens"[PORGN]`, est la [séquence d'accession MG272208](https://www.ncbi.nlm.nih.gov/nuccore/MG272208.1).


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `"Canis lupus"[PORGN]` | séquences pour lesquelles le nom de taxon source primaire contient “Canis lupus” |


Tout taxon, quelle que soit sa position dans l'arbre du Vivant, possède un code numérique l'identifiant de manière non équivoque. On peut obtenir un tel code via une recherche dans [NCBI Taxonomy](https://www.ncbi.nlm.nih.gov/taxonomy). Une fois le code connu, on peut demander les séquences correspondant exactement à notre taxon.


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_eq(1773)` | *?* | séquences issues de *Mycobacterium tuberculosis*, espèce dont [le taxid est 1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773) |


Attention ! Les requêtes formées à l'aide du prédicat `tax_eq()` ne renvoient pas les séquences taxonomiquement “en dessous” du taxon donné (ci-dessus *Mycobacterium tuberculosis*, dont l'ID taxonomique est [1773](https://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=1773)). Par exemple la requête ci-dessus ne produira pas les séquences de “*Mycobacterium tuberculosis* variant bovis”. Voir ci-dessous pour une autre requête renvoyant toutes les séquences correspondant à tout un clade phylogénétique.


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_tree(1773)` | `zzz[orgn:__txid1773]` | séquences issues de *Mycobacterium tuberculosis* et de tout le clade dont *M. tb* est la racine |

Notez bien que dans la requête Genbank, ce qu’on écrit à gauche du crochet n’a pas d’importance, mais il faut qu’il y ait quelque chose.

### Recherche avec mots-clefs dans la description de la séquence

Tous les enregistrements INSDC contiennnent un champ descriptif en texte libre. C'est le champ apparaissant dans un fichier EMBL à la balise "DE", ou sur la ligne "DEFINITION" d'un fichier au format GenBank, aussi appelé parfois le champ de "titre" d'un enregistrement. On peut rechercher un mot-dlef dans ce champ à l'aide des requêtes ci-dessous.


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `description="*RPOB*"` | `RPOB[TITL]` | séquences avec “RPOB” (recherche insensible à la casse des caractères) apparaissant n’importe où dans le champ de description (“titre”) |


### Recherche en fonction du compartiment cellulaire où se trouve l'ADN

Chez les eukaryotes, la plupart du matériel génétique se trouve dans le noyau, mais il y a aussi selon le cas de l'ADN dans les mitochondries, dans les plasmides, etc. On peut demander à voir uniquement les séquences correspondant à un compartiment cellulaire bien précis :


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `organelle="mitochondrion"` | `gene_in_mitochondrion[PROP]` | séquences d'ADN mitochondrial et non nucléaire |
| `organelle != "mitochondrion" AND mol_type="genomic DNA"` | `gene_in_genomic[PROP]` | séquences d'ADN du noyau |


### Filtrage par type de base de données

On peut vouloir exclure les données RefSeq, déjà présentes ailleurs dans GenBank mais constituant un sous-ensemble très sélectif de séquences de référence, ou bien demander les séquences qui ont été initialement publiées ou bien dans GenBank, ou bien dans ENA, ou bien dans DDBJ :


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| *?* | `srcdb_genbank[PROP]` | ne prendre que les données initialement soumises via Genbank |
| *?* | `NOT refseq[FILTER]` | ne pas inclure les données du jeu RefSeq (ce qui est déjà le comportement par défaut pour les requêtes ENA) |
| *?* | `srcdb_ddbj/embl/genbank[PROP]` | un peu la même chose que de filtrer par `NOT refseq[FILTER]` : séquences originales |

### Filtrage par projet/étude

Dans ENA, les séquences publiées le sont nécessairement au sein d'un **projet ou étude ("study")**. Ainsi, on peut demander à voir toutes les séquences publiées dans le cadre d'une telle étude :


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `study_accession="PRJEB402"` | *?* | trouver toutes les séquences issues du projet d’identifiant PRJEB402 |


## Requêtes complexes

Voilà, nous avons fait un bref tour d'horizon des requêtes dans ENA et dans GenBank. Comme nous l'avions annoncé en ouverture de ce document, on peut construire des requêtes de base en combinant les opérateurs avec des "ET" ou des "OU" logiques :

| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `tax_name("Sus scrofa") AND organelle="mitochondrion" AND base_count > 10000 AND description="*complete*"` | séquences mitochondriales de porc d'au moins 10kb et contenant le mot "complete" dans leur description |


