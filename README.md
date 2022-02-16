# AIP specification planning

This document is a draft specification for SFU Library's Archival Information Packages (AIPs). 

Ongoing planning, issues, and other considerations are in [Section 5](#5-other-considerations).

# 1. Introduction

The purpose of the AIP specification is to define what components and metadata are necessary and practical for long-term
preservation of digital objects at the SFU Library. The specification will provide guidelines and suggest workflows for
producing and validating AIPs in a platform-independent way.

# 2. AIP contents

AIPs should be structurally consistent to a degree. Additional metadata for digital objects may be included in the 
`data` directory, but should not include derivatives such as OCR, thumbnails, or access copies.

## 2.1 BagIt files

The AIP will be contained within a Bag conforming to the BagIt specification. The top-level file directory will be as
follows:

```
├── bag-info.txt
├── bagit.txt
├── data
├── manifest-sha1.txt
└── tagmanifest-sha1.txt
```

These files will be generated automatically when using tools such as Bagger.

The `bag-info.txt` file must include the `BagIt-Profile-Identifier` tag in order for it to be validated.

The data folder will contain the digital object and its metadata and derivatives. At the minimum, the data directory
should contain the following:

```
└── data
    ├── DigitalObject.tiff
    ├── objectname_dc.xml
    ├── objectname_fits.xml
```

Where `DigitalObject.tiff` is the object itself saved in its original format.

## 2.2 Metadata

Descriptive and technical metadata must be included in the AIP. Other metadata files not specified here (such as
platform-specific metadata exported directly from a repository) may be included if they would provide additional
contextual information.

### Descriptive

Descriptive metadata must be represented following the Dublin Core schema. In Islandora repositories, Dublin Core 
metadata can be exported using [Islandora bagger](https://github.com/mjordan/islandora_bagger).

The Dublin Core metadata file is identified by the "_dc" appended to the filename.

### Technical

Technical information will be represented in FITS metadata. In Islandora repositories, FITS metadata can be exported 
using [Islandora bagger](https://github.com/mjordan/islandora_bagger).


FITS metadata files should follow the naming convention "identifier-FITS.xml"


### Structural
Information about parent/child relationships between objects as well as arrangement for ordered content must be 
captured when relevant. When referencing an external object, use its persistent identifier rather than a system-
specific identifier.

Details specific to SFU repositories are included below.

#### SFU Summit
Relationships are represented in the media use field in SFU Summit. To extract this information from Summit:
1. Use Islandora Bagger with the AddMedia plugin to include `media.json` and `media_use_summary.tsv` when creating a Bag.
2. In `media.json` the `field_media_use` object identifies the relationships by their taxonomy id. Using the URL in 
`field_media_use`, lookup the JSON metadata for the taxonomy id and confirm that there is an external URI for the term.
This should match the URI in the `media_use_summary.tsv` file.
3. 

#### SFU Special Collections

## 2.3 Taxonomies
Whenever possible, include either a URI pointing to taxonomy terms, or define them within the metadata. 

Do not leave platform-specific taxonomy identifiers in the metadata without either translating them into terms or providing a 
URI containing a definition. 

### Islandora identifiers
Media/use
Memberof
taxonomy


# 3. Workflow

The AIP will be created as soon as possible after an object has been deposited. 

1. The AIP creator will create a Bag from the digital object. This can either be created directly from the repository 
 (i.e., with [Islandora bagger](https://github.com/mjordan/islandora_bagger)) or 
created from the object itself using a tool such as [Bagger](https://github.com/LibraryOfCongress/bagger) or [SFU Moveit](https://github.com/axfelix/moveit-electron).
2. Confirm the Bag contains the expected contents and structure detailed in [2. AIP Contents](#2-aip-contents). 
3. Enter the `BagIt-Profile-Identifier` in the `bag-info.txt` file. The `BagIt-Profile-Identifier` is the URI of the 
BagIt profile JSON file.
4. Run validation steps detailed in the [Validation](#4-validation) 
section. 

# 4. Validation

## 4.1 BagIt validator

## 4.2 Bag profile validator

## 4.3 Metadata validation (?)

# 5. Other considerations
The following section includes outstanding issues or topics for further consideration in this specification's 
development.

## 5.1 Persistent identifiers

Persistent identifiers may be desirable to include in the specification. Requiring AIPs to have objects identified using
a DOI or ARK would solve the problem of objects being labeled with only system-specific identifiers (such as an
Islandora PIDs).

When making a decision about persistent identifiers, we should consider whether they will be intended for internal 
Libary use only or if they will be used for public access as well.

Regardless of which standard is chosen, if persistent identifiers are to be used in the AIPs, the Library must be able 
to ensure that the redirect links will be up-to-date in the case of repository migration or other changes to the URL. 

### DOI

DOIs have the advantage of being widely recognized and widely used for academic information like published works and
research data. The disadvantage of using DOIs is that they are proprietary and will require a contract with a service 
provider and funding allocated depending on the number of DOIs to be created. Being managed externally does also mean 
that the Library will not have to allocate resources towards creating and managing a database of identifiers.  

### ARK

ARKs are commonly used for archival records and museum items. The advantage of ARKs is that they are free, open, and 
flexible compared to DOIs. The disadvantage is that their management would require more responsibility from the Library 
to manage and update the identifiers. 

ARKs can be maintained locally by an institution and still remain publicly resolvable at a standardized URL. For example, 
if SFU Library maintained an ARK resolver at `https://ids.lib.sfu.ca`,
 ARKs such `https://ids.lib.sfu.ca/ark:/19837/e0ab731b-2767-4983-8fb9-2975a3f0b419` can be published, if desired, in the 
form `https://n2t.net/ark:/19837/e0ab731b-2767-4983-8fb9-2975a3f0b419` as long as the host `https://ids.lib.sfu.ca` 
remains associated in the ARKAlliance's NAAN registry with SFU's NAAN, 19837. N2T commits to redirecting resolution 
requests from `https://n2t.net` to local hosts.

For ARKs that are not intended primarily for public consumption, such as those used in internal structural metadata, 
using ARKs in their URN form, without an `http` transport layer (e.g. `ark:/19837/e0ab731b-2767-4983-8fb9-2975a3f0b419`) 
will be sufficient.

The unique string that identifies an object (in the above example, the UUID `e0ab731b-2767-4983-8fb9-2975a3f0b419`) can be 
generated by the ARK service, or by any other trustworthy application. Systems such as Drupal create UUIDs for all entities 
they manage, and UUIDs can be easily generated elsewhere with a very high degree of assurance that they will be unique. 
Because UUIDs can be safely assumed to be unique, it is posible to mint an ARK incorporating them at any point in the object's 
life cycle, and update the ARK resolution service at any time. For these reasons, SFU should consider using UUIDs as the 
identifier portion of ARKs it mananges.

Maintaining a local ARK resolution service at SFU will require documented processes for the creation, maintenance (e.g., 
updating), and use of ARKs, as well as commitment to maintain the active resolution service and to preserve the data set 
that links ARKs to the current location of the objects they identify.

## 5.2 Structural metadata

Including metadata like METS could provide important information about object relationships. This could be especially 
useful for paged objects like newspapers. In Islandora, relationships are represented with platform-specific terms which 
require the inclusion of a taxonomy document to interpret those relationships. METS either instead of or in addition to 
a taxonomy description could make object relationships and structure more apparent to future users.

### Islandora relationships


## 5.3 Taxonomy management