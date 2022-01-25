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

Descriptive metadata must be represented following the Dublin Core schema. In Islandora, Dublin Core metadata can be exported using 
[Islandora bagger](https://github.com/mjordan/islandora_bagger).

The Dublin Core metadata file is identified by the "_dc" appended to the filename.

### Technical

Technical information will be represented in FITS metadata. 

## 2.3 Taxonomies

# 3. Workflow

# 4. Validation

## 4.1 Checksums

## 4.2 Bag profile validator

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

## 5.2 Structural metadata

## 5.3 Taxonomy management