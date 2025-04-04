# Taxonomic data on tape

See issue: https://github.com/SpeciesFileGroup/2025_workshop_on_taxon_names/issues/6
Repository on Codeberg: https://codeberg.org/dhobern/tapeworm

## Concept
Exchange of taxonomic data between different databases and representations is challenging because of differences in data structures and terminology, the need to match records that may include small differences, etc.

Although standards and representations vary, most representations rest on common or intercompatible concepts such as publication/reference/source, taxon name, taxon/otu, synonym, taxon name relation, distribution record, type material, etc.

It seems reasonable to consider an interchange format that is optimised for easy generation and flexible consumption (rather than optimised for user interactions). The focus should be on granular units that represent simple assertions or sets of related assertions that a data consumer may choose to adopt. Of course, ...

![image](https://github.com/user-attachments/assets/e879441c-b429-40e9-ab31-37e74dfdbd14) (XKCD 927)

This has been discussed in terms of generating a "tape" of actionable data objects representing taxonomic data, either as a streaming serialisation of a dataset or as a smaller object that can be tested against an existing dataset or to represent a diff between two datasets or as a set of patch instructions for modifying a dataset. These cases are all strongly interrelated.

In many/most cases, the items on such a tape can be ordered so that each item depends only on items that have already been processed (i.e, the tape avoids forward references to objects). There are some areas where this goal may be more challenging. For example, genera should normally be ordered to appear before species included in each genus, but this could conflict with normal practice in declaring the type species for the genus (as a property of the genus). To avoid the need for forward references, the relationship could be asserted as a property on the species (isTypeSpeciesFor), or it could be broken out as a TaxonNameRelationship object that follows both items, or the genus could be included twice (same identifier, properties split between the two occurrences). Using finer-grained object types (TaxonNameRelationship, etc.) may be the easiest way to accommodate such cases and maximise flexibility for the data systems generating and consuming data.

## Options

Two families of serialisation have been considered.

1. JSONL (or similar rows, each representing a data object that a system can choose to process).
2. A Markdown or TextTree representation that uses nesting (by paragraph level or indentation) to express the structure of the data.

### JSONL tape
The following is an example JSONL representation for the root components of the classification in the [COLDP Alucitoidea](https://www.checklistbank.org/dataset/2207/about) dataset. The JSON objects effectively provide a graph serialisation for the data. This example was generated using a recursive method to walk the classification from the root taxon. Since the taxon depended on a name record, it triggered the insertion of the name first, and the name similarly triggered the addition of the reference. Once any item has been added, any subsequent references to the same item simply points back to the earlier entry. Each row is expressed here as an item of a defined class that can be added (Add) to any new dataset. The reason for this becomes clear below. ID values are internally consistent between all references to any object and also unique within the dataset (so they can serve as identifiers for nodes in a graph). The original identifier in the source dataset is included for information. All included properties are supplied as the Body of the record.
```
{"Action": "Add", "ItemClass": "Reference", "ID": "00000001", "SourceID": "42", "Body": {"ID": "00000001", "author": "Linnaeus, C.", "title": "Systema naturae per regna tria naturae: secundum classes, ordines, genera, species, cum characteribus, differentiis, synonymis, locis", "issued": "1758", "containerTitle": "", "volume": "", "issue": "", "page": "1-824", "link": "https://www.biodiversitylibrary.org/page/726886", "citation": ""}}
{"Action": "Add", "ItemClass": "Name", "ID": "00000002", "SourceID": "1", "Body": {"ID": "00000002", "basionymID": "00000002", "scientificName": "Animalia", "authorship": "Linnaeus, 1758", "rank": "kingdom", "uninomial": "Animalia", "genus": "", "infragenericEpithet": "", "specificEpithet": "", "infraspecificEpithet": "", "referenceID": "00000001", "publishedInPage": "9", "publishedInYear": "1758", "code": "ICZN", "status": "established", "remarks": "", "link": "https://www.biodiversitylibrary.org/page/726898"}}
{"Action": "Add", "ItemClass": "Taxon", "ID": "00000003", "SourceID": "1", "Body": {"ID": "00000003", "parentID": "", "nameID": "00000002", "scrutinizer": "Gielis, C. & Hobern, D.", "scrutinizerDate": "2020-07-16", "provisional": "false", "referenceID": "", "extinct": "false", "temporalRangeEnd": "Holocene", "lifezone": "", "kingdom": "", "phylum": "", "class": "", "order": "", "superfamily": "", "family": "", "subfamily": "", "tribe": "", "genus": "", "uninomial": "Animalia", "species": "", "remarks": ""}}
{"Action": "Add", "ItemClass": "Reference", "ID": "00000004", "SourceID": "107", "Body": {"ID": "00000004", "author": "von Siebold, C.T. & Stannius, H.", "title": "Lehrbuch der vergleichenden Anatomie der Wirbellosen Thiere, Erster Theil", "issued": "1848", "containerTitle": "", "volume": "", "issue": "", "page": "1-679", "link": "https://www.biodiversitylibrary.org/page/11149410", "citation": ""}}
{"Action": "Add", "ItemClass": "Name", "ID": "00000005", "SourceID": "2", "Body": {"ID": "00000005", "basionymID": "00000005", "scientificName": "Arthropoda", "authorship": "von Siebold, 1848", "rank": "phylum", "uninomial": "Arthropoda", "genus": "", "infragenericEpithet": "", "specificEpithet": "", "infraspecificEpithet": "", "referenceID": "00000004", "publishedInPage": "4", "publishedInYear": "1848", "code": "ICZN", "status": "established", "remarks": "", "link": "https://www.biodiversitylibrary.org/page/11149394"}}
{"Action": "Add", "ItemClass": "Taxon", "ID": "00000006", "SourceID": "2", "Body": {"ID": "00000006", "parentID": "00000003", "nameID": "00000005", "scrutinizer": "Gielis, C. & Hobern, D.", "scrutinizerDate": "2020-07-16", "provisional": "false", "referenceID": "", "extinct": "false", "temporalRangeEnd": "Holocene", "lifezone": "", "kingdom": "Animalia", "phylum": "", "class": "", "order": "", "superfamily": "", "family": "", "subfamily": "", "tribe": "", "genus": "", "uninomial": "Arthropoda", "species": "", "remarks": ""}}
```

Generating a JSONL tape of this kind for two versions of a dataset (or two sources for the same taxon) makes it possible to compare tapes and identify differences that may need to be processed. The following is a derived tape that documents the differences between two recent versions of the Alucitoidea dataset. Some items now have an Action of None. These are included because they are referenced from within some of the new (Action is Add) items. Including these will allow the target data system to locate the desired anchorpoints for the new data.
```
{'Action': 'None', 'ItemClass': 'Name', 'ID': '00000031', 'SourceID': '11', 'Body': {'ID': '00000031', 'basionymID': '00000031', 'scientificName': 'Alucita', 'authorship': 'Linnaeus, 1758', 'rank': 'genus', 'uninomial': 'Alucita', 'genus': '', 'infragenericEpithet': '', 'specificEpithet': '', 'infraspecificEpithet': '', 'referenceID': '00000001', 'publishedInPage': '542', 'publishedInYear': '1758', 'code': 'ICZN', 'status': 'established', 'remarks': '', 'link': ''}}
{'Action': 'None', 'ItemClass': 'Taxon', 'ID': '00000032', 'SourceID': '11', 'Body': {'ID': '00000032', 'parentID': '00000016', 'nameID': '00000031', 'scrutinizer': 'Gielis, C. & Hobern, D.', 'scrutinizerDate': '2020-07-17', 'provisional': 'false', 'referenceID': '', 'extinct': 'false', 'temporalRangeEnd': 'Holocene', 'lifezone': 'terrestrial', 'kingdom': 'Animalia', 'phylum': 'Arthropoda', 'class': 'Insecta', 'order': 'Lepidoptera', 'superfamily': 'Alucitoidea', 'family': 'Alucitidae', 'subfamily': '', 'tribe': '', 'genus': '', 'uninomial': 'Alucita', 'species': '', 'remarks': ''}}
{'Action': 'Add', 'ItemClass': 'Reference', 'ID': '00000905', 'SourceID': '167', 'Body': {'ID': '00000905', 'author': 'Ustjuzhanin, P., Kovtunovich, V., Saldaitis, A. & Prozorov, A.', 'title': 'New data on many-plumed moths (Lepidoptera: Alucitidae) of the Arabian Peninsula', 'issued': '2025', 'containerTitle': 'Zoology in the Middle East', 'volume': '71', 'issue': '1', 'page': '58-62', 'link': 'https://doi.org/10.1080/09397140.2025.2459507', 'citation': ''}}
{'Action': 'Add', 'ItemClass': 'Name', 'ID': '00000906', 'SourceID': '507', 'Body': {'ID': '00000906', 'basionymID': '00000906', 'scientificName': 'Alucita mayri', 'authorship': 'Ustjuzhanin, Kovtunovich & Saldaitis, 2025', 'rank': 'species', 'uninomial': '', 'genus': 'Alucita', 'infragenericEpithet': '', 'specificEpithet': 'mayri', 'infraspecificEpithet': '', 'referenceID': '00000905', 'publishedInPage': '60', 'publishedInYear': '2025', 'code': 'ICZN', 'status': 'established', 'remarks': '', 'link': ''}}
{'Action': 'Add', 'ItemClass': 'Taxon', 'ID': '00000907', 'SourceID': '503', 'Body': {'ID': '00000907', 'parentID': '00000032', 'nameID': '00000906', 'scrutinizer': 'D. Hobern', 'scrutinizerDate': '2025-02-25', 'provisional': 'false', 'referenceID': '00000905', 'extinct': 'false', 'temporalRangeEnd': 'Holocene', 'lifezone': 'terrestrial', 'kingdom': 'Animalia', 'phylum': 'Arthropoda', 'class': 'Insecta', 'order': 'Lepidoptera', 'superfamily': 'Alucitoidea', 'family': 'Alucitidae', 'subfamily': '', 'tribe': '', 'genus': 'Alucita', 'uninomial': '', 'species': 'Alucita mayri', 'remarks': ''}}
{'Action': 'Add', 'ItemClass': 'Reference', 'ID': '00000908', 'SourceID': '168', 'Body': {'ID': '00000908', 'author': 'Gieliz R. & Buszko, J.', 'title': 'Order Lepidoptera, family Alucitidae. – In: van Harten, A. (ed.) Arthropod fauna of the UAE', 'issued': '2009', 'containerTitle': '', 'volume': '2', 'issue': '', 'page': '458–462, figs 1–2, 3–6, 7–8, 9', 'link': '', 'citation': ''}}
{'Action': 'Add', 'ItemClass': 'Name', 'ID': '00000909', 'SourceID': '508', 'Body': {'ID': '00000909', 'basionymID': '00000909', 'scientificName': 'Alucita inflativora', 'authorship': 'Gielis & Buszko, 2009', 'rank': 'species', 'uninomial': '', 'genus': 'Alucita', 'infragenericEpithet': '', 'specificEpithet': 'inflativora', 'infraspecificEpithet': '', 'referenceID': '00000908', 'publishedInPage': '458', 'publishedInYear': '2009', 'code': 'ICZN', 'status': 'established', 'remarks': '', 'link': ''}}
{'Action': 'Add', 'ItemClass': 'Taxon', 'ID': '00000910', 'SourceID': '504', 'Body': {'ID': '00000910', 'parentID': '00000032', 'nameID': '00000909', 'scrutinizer': 'D. Hobern', 'scrutinizerDate': '2025-02-25', 'provisional': 'false', 'referenceID': '00000908', 'extinct': 'false', 'temporalRangeEnd': 'Holocene', 'lifezone': 'terrestrial', 'kingdom': 'Animalia', 'phylum': 'Arthropoda', 'class': 'Insecta', 'order': 'Lepidoptera', 'superfamily': 'Alucitoidea', 'family': 'Alucitidae', 'subfamily': '', 'tribe': '', 'genus': 'Alucita', 'uninomial': '', 'species': 'Alucita inflativora', 'remarks': ''}}
{'Action': 'None', 'ItemClass': 'Name', 'ID': '00000911', 'SourceID': '332', 'Body': {'ID': '00000911', 'basionymID': '00000911', 'scientificName': 'Alucita tanzanica', 'authorship': 'Ustjuzhanin & Kovtunovich, 2018', 'rank': 'species', 'uninomial': '', 'genus': 'Alucita', 'infragenericEpithet': '', 'specificEpithet': 'tanzanica', 'infraspecificEpithet': '', 'referenceID': '00000079', 'publishedInPage': '169', 'publishedInYear': '2018', 'code': 'ICZN', 'status': 'established', 'remarks': '', 'link': ''}}
{'Action': 'Add', 'ItemClass': 'Synonym', 'ID': '00000912', 'SourceID': '', 'Body': {'taxonID': '00000910', 'nameID': '00000911', 'accordingToID': '', 'status': 'synonym', 'referenceID': '00000905', 'remarks': '', 'ID': '00000912'}}
```
### TextTree tape
The following is an example of a text tree representation including more detail than is present in the standard ChecklistBank text tree exports. The content corresponds to the information in the JSONL example above. This representation is more esoteric than the JSONL representation, but it has no need to express any identifiers - all content represents comparable information. Depth of indentation may vary between sources, but the content of each row will be identical if the same detail is represented. This makes this format much more amenable to processing using text-based diff tools or, e.g. git merge tools.
```
+ T kingdom | Animalia | Linnaeus, 1758
  + N kingdom | Animalia | Linnaeus, 1758 | ACCEPTED
  + R Linnaeus, C. | 1758 | Systema naturae per regna tria naturae: secundum classes, ordines, genera, species, cum characteribus, differentiis, synonymis, locis |  |  |  | 1-824 | https://www.biodiversitylibrary.org/page/726886 | REFERENCEID
  + T phylum | Arthropoda | von Siebold, 1848
    + N phylum | Arthropoda | von Siebold, 1848 | ACCEPTED
    + R von Siebold, C.T. & Stannius, H. | 1848 | Lehrbuch der vergleichenden Anatomie der Wirbellosen Thiere, Erster Theil |  |  |  | 1-679 | https://www.biodiversitylibrary.org/page/11149410 | REFERENCEID
```

The following (with indentation reduced) is an example including a basionym, synonym and more references for a species.
```
+ T species | Alucita beinongdai | (Yang, 1977)
  + N species | Alucita beinongdai | (Yang, 1977) | ACCEPTED
    + N species | Orneodes beinongdai | Yang, 1977 | BASIONYM
      + R Yang, C.Z. | 1977 | Moths of North China 1 |  |  |  |  |  | REFERENCEID
  + N species | Alucita ussurica | Ustjuzhanin, 1999 | SYNONYM
    + R Ustjuzhanin, P. | 1999 | New and little-known Palaearctic Species of Alucitidae (Lepidoptera) | Far Eastern Entomologist | 68 |  | 1–7 | http://www.biosoil.ru/Files/FEE/00000102.pdf | REFERENCEID
    + R Hao, Sh., Ustjuzhanin, P.Y. & Kovtunovich, V.N | 2025 | New data on poorly-known species of many-plumes moth (Lepidioptera: Alucitidae) from China and Russia | Far Eastern Entomologist | 517 |  | 9-13 | https://doi.org/10.25221/fee.517.2 | REFERENCEID
```

A more markdown-based representation could easily supply similar data and simultaneously be meaningful to users. The hardest part is determining a way to express the hierarchy while both avoiding ambiguity and facilitating efficient comparison between alternative sources.

## Use cases
The following use cases may help to evaluate options. These mostly focus on the challenges in improving the [Global Lepidoptera Index](https://www.checklistbank.org/dataset/55434/about) (GLI).

### Summarise differences between datasets
A tape like the second JSONL example above illustrates a possible use case:
1. Generate a tape summarising a section of an external checklist (a regional species list, summary of a new taxonomic paper, etc.) - Plazi/Pensoft digests are close to this already.
2. Generate a tape for the corresponding taxon (genus, etc.) from GLI.
3. Compare the pair of tapes and derive an actionable diff tape.
4. Human review/evaluation of proposed changes.
5. Process the actions one at a time in the tape using the TW API for GLI. (The None actions allow the processor to find the necessary existing items to link the new data.)

Having a process like this would make it much easier to expand the types of data added to GLI. At present, the workload makes it infeasible also to add type records, distribution data, etc.
