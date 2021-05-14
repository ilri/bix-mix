# Construire des requêtes avancées pour ENA et GenBank


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

Il arrive que des séquences publiées soient modifiées par la suite, lorsque par exemple l'équipe qui les a publiées s'est rendu compte d'une erreur. Dans ce cas, le numéro d'accession en principal ne change pas, mais le numéro de version se trouve incrémenté. Par exemple, on passe de l'accession FR682468.1 à l'accession FR682468.2. On peut vouloir chercher des séquences en fonction de leur date de dernière modification.


| syntaxe ENA (Advanced Search)                               | syntaxe GenBank               | signification |
| ----------------------------------------------------------- | ----------------------------- | ------------- |
| `last_updated >= 2021-04-01 AND last_updated <= 2021-04-27` | `2021/04/01:2021/04/27[MDAT]` | séquences dont l’enregistrement a été modifié pour la dernière fois entre le 1<sup>er</sup> et le 27 avril 2021 |

