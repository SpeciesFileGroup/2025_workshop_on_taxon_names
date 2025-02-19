# SFG 2025 Workshop on Taxonomic Names

## Logistics

Week of March 3rd. In Person. University of Illinois, Champaign.

## Participation

Participation is closed.

## Outreach

_An opportunity to hear from the hackathon participants as they "report out"._

Thursday 10:00-11:30 CST.  Forbes 1005.

Indexing a biological experiment a quintillion(?) years in the making: A round-table on the practicality of indexing and application of taxon names around the world 

Please join the Species File Group and their national and international hackathon guests for short presentations and a round table discussion on the informatics challenges surrounding how we name the Earth's species. Gain insights from developers and contributors to some of the world's largest aggregators of species indices including the World Registry of Marine Organisms, Catalogue of Life, and the Global Biodiversity Informatics Facility as to the challenges behind managing and curating comprehensive global species indices. Learn how INHS Collections (and collections around the world) both benefit and contribute to these efforts, and how they drive innovation via sharing their practical needs within software-development processes.

The event will held be in-person at Forbes 1005 and on Zoom at https://illinois.zoom.us/my/sfgcommons?pwd=eVI4UkdIUzdIYXRIcUhreVdES2ZQQT09. 

While participation is currently closed you can read more on the hackathon here: https://github.com/SpeciesFileGroup/2025_workshop_on_taxon_names.

Contact Deborah Paul if you have questions regarding the open session.

## Background

Driven by a long-running set of use-cases and requirements largely pertaining to TaxonWorks related requests from Donald Hobern, and a need to firmly set-aside time to address these, we blocked out time to focus on the issues. Given ongoing developments at the SFG, and a need to align with other global efforts both technical, and cross domain (e.g. plants, animals) we've expanded the scope, slightly.

While we have some specific agendas we will come to the hackathon with an open mindset and evolve the work according to some unconference-style discussion to happen on the first day.

There are some well known contexts behind the efforts, the primary focus is their technical aspects:

* "Sharing" taxonomies - People need to effectively draw in data into their work-spaces. 
* "Under-utilized" APIs - All the major players now share data in straightforwardly accessible APIs, and all players can quickly adopt those APIs to emerging needs. The utility and potential behind this fact remains, at least in Matt's opinion, greatly underutilized at all levels of use (from individual research to global initiatives).
* "Fractal" interactions - There is a spectrum of work, from reconciling a single taxon name, to creating a globally unique nomenclator. Describing this spectrum and annotating the paths across it with real life use-cases is necessary for us to collectively architect workflows, software, APIs, UIs that facilitate this work. See `Exchanging Names` below.
* "Merge and sync" - Given a set of names A, and a set of names B, we want a multi-step, human-in-the-loop process that a) minimizes losseyness; b) minimizes dangerous human decisions; c) maximizes a human's decisions impact; d) integrates or clearly ties into a DSL (domain specific language) that facilitates the work
* "Checklists as macros" - When we compare two rows of data in A and B, we should come up with a deterministic set of steps that results in their "syncing" or "merging".  These need not need to result in a perfect merge or sync, they must, however, result in an *understood outcome* at some stage/step.
* "Latency exists, and that is OK" - We are not banks and trading platforms requiring sub-millisecond latency, for example when a taxonomist updates a name in TaxonWorks it does not need to be globally shared *immediately*. Accepting latency scales with the size of the dataset (we've spent 250 years or more building the global dataset, and we're not done), and documenting and explaining this removes a lot of pressure from engineering requirements. For another example, is it really necessary to do "Monthly" updates to the CoL? Carefully defining and communicating how often resources are updated lets tools/users comfortably work around these. Coming up with real-world examples of latency impacting biodiversity informatics would almost certainly become a very useful reference. Existing related examples (time from collection to species description) are published on and increasingly well understood. Can we predict upper and lower bounds per process/product?
* "Using the right names" - Curators, scientists etc. claim they want to use the correct names, and the statement "automatically update" is typically used shortly after their scenarios are layed out. Matt strongly believes this simplification is not at all what they want, and that this becomes evident once scenarios are played out in detail. What then _do_ they really want, and how can we better communicate and point this out?
* "Using a backbone" -> See "Using the right names".

## Exchanging Names

In general the challenge is now not API access, it's moving from "native" or "standard" to "native" or "standard".  For example:

* ChecklistBank to TW native
* TW Native to WoRMS
* TW Native to Checklist Bank
* ChecklistBank to GBIF Backbone
* Catalogue of Life (=ChecklistBank?) to TW
* Checklist Bank to Specify/Arctos/Symbiota/ALA/custom 
* Rhakis to ChecklistBank
* ChecklistBank to DwC Checklist

This can also be abstracted on the axis of DSLs, e.g. a name in native Go to R to Ruby to Python to Java to PHP to Javascript etc.

### A single name

For example "Aus bus Smith 1920" attached to "root" of the native target.

### A name and its classification

E.g. a species name, and its genus and higher taxonomy.

Here we assume the name is missing, but the classification may be:
* Completely missing
* Partially missing
* Completely present

### A name and circumscribed names 

E.g. a Genus and its species.

While the simplest use case is everything is missing, we can perhaps assume almost nothing:
* The name may be present/absent
* The children maybe be present/absent.

If there is some synchronization, then we have the "Many names" case.

### Many names
A matrix of possible use-cases exist based on the following axes:

* The set of name are all valid, valid and invalid, all invalid
* The attachment point is empty, or not (i.e. create or update, aka add or sync)

### All names
Global checklists, nomenclators

## APIs

Here we are thinking both of web-accessible `/api/` entry points, REST, JSON etc. and Code-based.  E.g. ORMs used in things like TaxonWorks.

### APIs of interest

_TODO: brief links to documentation._

#### GBIF 
#### ChecklistBank 
#### TaxonWorks /api/v1
#### WoRMS
#### TaxonWorks ORM (code)
#### SFG "Borg" (code)
#### Rhakhis

## Use cases

_Should likely move these to Issues for discussion and clear this out. These are concrete examples with known/requests real-world examples that if fixed would remove bottlenecks to *existing* workflows. "Imagined" use cases are welcome, but should be flagged as such._

* Given ~4k Lepidopteran names, that are up-to-date according to the curator's assertion, merge these into and existing, richly annotated set of data that variously overlaps.
* When typing a name in TaxonWorks, and it is not found (e.g. async-autocomplete), smoothly prompt the user to draw it down from CheckList Bank (or another API). From TW -> CheckListBank -> TW.  Note that we can share data across TW projects by proxy of ChecklistBank given snapshots are there.
* Request that all children of a name be inputed into TaxonWorks, in context, seemlessly
* Resolve Otu names to both existing TaxonNames in TaxonWorks or if no matches new names
* Consistent, routine builds of ChecklistBank formatted data for CoL exports, from TaxonWorks.
* When initiating a new project in TaxonWorks I want to pull down a set of completely new names into a project.  From time-to-time I want to check to see if there are divergences.
* TODO: Borg examples

