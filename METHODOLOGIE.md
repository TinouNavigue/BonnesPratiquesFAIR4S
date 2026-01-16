# PHASE EXPLORATOIRE

## Requêtes de quelques articles depuis PubMed

*22/04/2025*

Equations requêtées sur PubMed (https://pmc.ncbi.nlm.nih.gov/)
| **Langage recherché** | **Requête** | **Nb articles retrieved** | **ID article** |
|:------:|:--------:|:--------:| :--------:| 
| json  | `json`   |2| PMC8075421 et PMC5796402 |
| python  | `name== python`   |2| PMC10809901 et PMC8102371



Analyse intuitive des résultats sur `Analyse_explo.xls`:
La requête n'étant pas précise, on a trouvé 3 résultats où le code se trouve soit:
- dans le corps même de l'article,
- comme lien dans le texte de l'article,
- comme image (par exemple .pdf) dans le corps de l'article


Cependant, le "code" associé n'en est pas vraiment. Il s'agit de
- renvoi à des vocabulaires. Par exemple FHIR, une norme développée par LH7 pour l'échange de données de santé entre institutions médicales, 'https://hl7.org/fhir/datatypes.html#Coding') ou 'http://xmlns.com/', domaine web créé pour nommer des concepts lors de l'utilisation de vocabulaires (RDF, XML, etc.).
- une démonstration de Jupyter Notebook sur la page des suppléments de PubMed pour montrer comment structurer des données, en html.
- de l'illustration de statistiques liées aux données de recherche, dans un format nécessitant la souscription à un logiciel propriétaire (Adobe). 

  

# STRUCTURATION DE LA BASE DE DONNEES 

*03/05/2025*

- Sur la base des résultats obtenus et de l'estimation des besoins: Élaboration d'une structure pour base de données dans un document `2025_05_03_STRUCTURE_AIRTABLE.rtf`
- Intégration dans [Airtable.com](https://airtable.com). Adaptation des champs et tables aux besoins et création de formulaires de requêtes :

  - Formulaire ‘Nouvel article’ : insère chaque article retrieved dans la table `ARTICLES`

  - Formulaire ‘Associer Code’ : Pour chaque `article`, introduction d'une ou plusieurs formes de mise à disposition (dépôt sur plateforme d’archivage, sur plateforme de développement, etc.) dans la table `MISEaDISPO` (une ligne par mise à disposition : contrairement à un tableur, la base de données permet d’assigner plusieurs mises à disposition au même code).

Rapidement, les formulaires se sont avérés désuets. L’adaptation de la base aux résultats a nécessité de travailler directement en grid view, ce qui convient du fait de la taille modeste du corpus et qui permet d'évaluer les critères subjectifs de visu, de manière empirique, afin d'affiner les critères.  

# METHODOLOGIE POUR LE RETRIEVAL DE CODE
## Choix du code
La méthode de requête est fondée sur un fichier 'suppfiles_code.csv' extrait de PubMed par M. Gobeill le *25/02/2025* et qui renvoie 13'000 articles scientifiques contenant des fichiers de code. Il compte 13'000 lignes avec un pmcid, l'extension du fichier, et son nom. Ont été exclus les fichiers dont l'extension ne correspondait pas à un fichier informatique, sur la base d’une liste fournie par M. Ruch, 'extension.json'. [PAS COMPRIS A QUOI ELLE SERVAIT] : M. Gobeill a exclu de la requête les extensions "txt","csv","html","xml","eps", et "htm", qui ne sont pas vraiment du code.


Notre intention première est de trouver du code dans les 11 langages qui ressortent le plus souvent du fichier 'suppfiles_code.csv', à savoir:

'R' 
'm'
'mat'
'py'
'pl'
'sps'
'sas'
'c'
'sh'
'cpp'
'java'

\(A noter que la liste ci-dessus sera adaptée par la suite, voir ci-dessous\). Nosu avons utilisé ChatGPT pour suggérer des requêtes.


## Requêtes formulées:

Base interrogée : https://pubmed.ncbi.nlm.nih.gov/. Un seul article (38237907) a été requêté sur https://pmc.ncbi.nlm.nih.gov/, par inadvertance.

| **Langage recherché** | **Requêtes** | **Nb articles retrieved** |
|:--------:|:------:|:--------:|
| C++ ou Cpp   | ("C++" OR "C plus plus") AND (code OR source code OR implementation OR software OR algorithm)  | 5   |
| py   | ("python" OR "py") AND code  | 3   |
| py **\***| (NCBI C++ Toolkit)  |  2  |
| py | (name== python)  |  2  |
| mat ou matlab   | (matlab OR ".mat" OR "MAT-file" OR "mat-file") AND (code OR script OR software OR "source code")  | 5   |
| java   | java code  | 5   |
| json **\*** **\***  | json  | 2 
   | 

**\***
 Requêté pour trouver du C++ mais a permis de trouver du python.

**\*** **\*** Pas du langage procédural mais ces requêtes ont été faites lors de la phase test de la base de données. Ensuite, on s'est concentré sur des langages procéduraux.


**Remarque concernant les droits d'accès:**
- les articles qui ne sont pas en accès libre (paiement requis ou accès via une institution) ont été écartés
- En revanche, les articles libre d'accès contenant du code dont l'accès est restreint ont été retenus, ceci afin de mettre en évidence les pratiques non recommandées de diffusion de code.


## Ecueils dans le choix des requêtes et langages 
### Orthographes différentes pour un même langage

Certaines des 11 extensions choisies sont difficiles à chercher séparément. 
- **Extensions ressemblantes** : Il est difficile de formuler des équations ciblées pour `mat` et `matlab`. En général, les résultats sont congruants. On a donc choisi de grouper les recherches .mat et .matlab.
- **Mêmes langages avec différentes extensions**: les fichiers C++ ont souvent comme extension cpp, pour éviter les erreurs de lecture. On a donc groupé ces deux recherches en une.

Nous avons donc décidé d'ajouter à notre liste de 11 extensions autant d'éléments que nécessaires pour obtenir 55 résulats. (voir la liste totale des requêtes plus bas dans la procédure). 

### Notion de code

Le Prof. Gobeill a exclu de son fichier les langages de code de struturation ou de représentation pour ne garder que les codes procéduraux / exécutifs. Ceci peut porter un biais sur la recherche. Toutefois, lors d'une première recherche de code en .json, on s'est rendu compte que ce type de code n'était pas mis à disposition selon les règles FAIR, cest à dire qu'il n'était pas uploadé sur des sites externes à PubMed.

Dans la table `PLATEFORME`, qui est un champ lié de la table `MaDISPO_CODE`, nous avons prévu un champ `LocalPubMed`. Il vise les cas où le code est disponible dans le corps même de l'article ou sur la page PubMed dans les suppléments. 

La plupart des codes qui présentaient cette particularité sans être publiés ailleurs étaient sous forme de fichier image (p.x. pdf), parfois en annexe, parfois dans le corps même du texte, souvent non OCR-isés et donc pas exportables dans une éditeur de code. Cet élément, si le code nest pas publié en externe, peut affecter les paramètres F, I et R.

Les quelques articles renvoyaient donc à des ontologies, des sites de gestion de concepts lors de utilisation de vocabulaires XML ou RDF, du langage html, ou des morceaux de code tels qu'ils devaient apparaître lorsque l'on éxécute un logiciel par ailleurs déjà connu des lecteurs de l'article.

Le code de structuration/représentation ne nécessite pas d'être déployé de manière détaillée à l'usage du lecteur de l'article: le code en lui-même existe déjà et ne doit pas être réinventé par les auteurs. L'exclusion des langages tels que .json non procéduraux dans la liste des codes nous a donc paru pertinente et nous avons décidé de poursuivre notre recherche sur la base de la liste de M. Gobeill.



Exemples de requête `json`:
| **Requête** | **Langage trouvé** | **Forme de Mise à dispo** |
|:------:|:--------:|:--------:|
| `json`  | json   |Lien vers .pdf sur un site d'ontologie (FHIR)|
|   | xml   |Screenshot sur Page des Suppléments|
|   | owl (langage descriptif)  |Lien dans le texte vers ontologie|
|   | html   |Jupyter notebook|
|

 

*22.10.2025*

Requêtes suivantes : 
Comme indiqué plus haut [NUMEROTER PRECISEMENT LE RENVOI...], nous avons ajouté à notre liste trois extensions:

- ipynb
- sps
- NetLogo

| **Langage recherché** | **Requête** | **Nb articles retrieved** |
|:------:|:--------:|:--------:|
| R  | r-script   |3|
| R  | r-code   |2|
| perl  | perl-code   |5|
| sas  | sas code   |5|
| shell  | "bash script"[tiab]   |5|
| ipynb  | ipynb   | 5 | 
|sps|SPSS script|4 **\***
|NetLogo|netlogo|4|


**\*** Nous n'avons à ce jour pas trouvé de 5ème article contenant du code sps. Les articles en SSPS sont anciens (articles trouvés jusqu'en 2004, soit avant GitHub) et les habitudes de publication de code n'étaient pas encore ancrées. Nous avons donc ajouté NetLogo, qui est la  suivante dans la liste des extensions les plus utilisées et qui soit du langage procédural.

ypinb n'est pas un langage procuédural mais un conteneur. Toutefois, les autres extensions de langage procédurales plus fréquentes (.c et .do) sont très difficiles à requêter dans PubMed, d'où le choix de l'extension .ipynb.



*1.11.2025*



# AMENDEMENTS DE LA BASE DE DONNEES

Au cours de l'encodage des métadonnées dans la base, des résultats sont apparus de visu et de manière empirique, ce qui a suscité un affinage de la base. Les champs suivants ont été ajoutés:

## Champs non exploités
Car jugés non pertinents lors de l'analyse exploratoire 
- Domaine
- Pays
- Langages

## Champs ajoutés
### Succès   
Ajout de 4 champs concernant le succès du code: 
- Like/stars/supporters
- Vues
- Forks
- Pull requests

Ces champs n'étant pas tous disponible sur les plateformes différentes, ajout d'un champ en fin de processus: `TotalSuccesCode`qui aditionne les 4 colonnes ci-dessus.

### Licence  
Ajout d'un champ `RegroupLicences`pour déterminer, pour chaque licence, si elle est adaptée à l'objet code: 5 entrées:

- Licence prévue pour le code (i.e. adaptée)
- CC (créative common) ou autre licence non adaptée
- Reserved (droits restreints)
- Licence pas déclarée (absence de licence)
- N/A car licence pas accessible

# CONVERSION DES ARTICLES EN HTML

Méthode
Conversion des articles de .pdf au format html à l’aide de l’outil open source pdftohtml (suite Poppler, version installée via Homebrew 23.1.0), exécuté en ligne de commande dans le Terminal macOS: 
`pdftohtml -c -s -enc UTF-8 *.pdf`

Articles non convertis : 
- 11238077.pdf protégé par copyright
- 32096823.pdf idem

Problèmes de formatage:
- 33425800.pdf (Syntax Warning: Invalid Font Weight)


# XXXXXXXXX  AVANT 24898551008, je sais pas sils sont tous lisibles.


# EXPLOITATION DES DONNEES ET EXPORTS 

## Choix de la plateforme

*24/11/2025*

En prévision de la présentation de la recherche par poster (fin décembre 2025), la statistique suivante a été effectuée: choix de la plateforme: Zenodo 10%, GitHub  55%, Autres plateformes de développement 2%, plateformes de distribution de packages 6% et pas ou peu de gestion du code 27%. Pour ce faire, un export de la table `MaDISPO_CODE` a été fait et retravaillé (voir `2025 11 24 MaDISPO_CODE-Grid view.csv`).

Les virgules dans les exports provoquent parfois des décalages lors de la conversion .csv en .xls avec tableur. Corrections à l'oeil.

A noter que la table a été modifiée après publication du poster, une entrée de la table `MaDISPO` (PMID 38032878) a été retirée (8 au lieu de 9 entrées) car le dépôt sur Zenodo ne concernait que les données. 

## Nombre d'articles

Corrections avant exploitation:

- Article 36951911 supprimé. Pas de code lié. Restent 57 articles, 81 mises à disposition. 

- NB, les articles 24268083 et 29317802 sont indisponibles bien qu'annoncés. 17332022 et 24197709 ne sont que disponibles en contactant l'auteur. Ils ont été conservés car montrent une mauvaise pratique. Nombre de mises à disposition pris en compte pour la rédaction du billet de blog:

*57 articles*

Une entrée de la table Articles s'est avérée vide. nouveau nombre pris en compte: 

*56 articles*

## Choix de la mise à disposition

Type de requêtes en langage naturel dans Omni (IA de AirTable) : `peux-tu faire une liste baseéé sur TAble articles, champ PMID et sur table MaDISPO_CODE champ Plateforme, qui dit X articles a mis à disposition sur Plateforme 1, X sur palteforme 2, etc. et compléter la liste avec les recoupements (parmi eux, X ont mis  a disposiiton à la fois sur plateforme 1 et plateforme 2).`

Vérification systématique de la cohérence des réponses avec les résultats déjà obtenus, en effectuant des checks dans la base de données et en posant des questions croisées. Omni se trompe dans ses décomptes.

## Findability

**Emplacement du lien du code:** Pour déterminer si les liens étaient dans le corps de l'article, ailleurs, ou inexistants, des exports ont été faits en csv, transformés en xls et classés. La vérification à la main s'imposait, du fait de l'encodage en langage naturel et différents champs concernés par le même paramètre.

Même méthode appliquée aux critères suivants:
- **Présence d'un ReadMe**
- **Choix de la licence**
- 

# VISUALISATIONS

Les visualisations ont été faites sur la base des exports .csv de la base de données ou des conversions en.xls, soit par l'outil de visualisation de Excel, soit par Power BI.