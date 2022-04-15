# AIP specification planning

This document is a draft specification for SFU Library's Archival Information Packages (AIPs). 

Ongoing planning, issues, and other considerations are in [Planning notes](/planning_notes.md).

# 1. Introduction

The purpose of the AIP specification is to define what components and metadata are necessary and practical for long-term
preservation of digital objects at the SFU Library. The specification will provide guidelines and suggest workflows for
producing and validating AIPs in a platform-independent way.

# 2. AIP contents

AIPs should be structurally consistent to a degree. The `data` directory may include additional metadata other than those specified here, but should not include derivatives such as OCR, thumbnails, or access copies.

## 2.1 BagIt files

The AIP will be contained within a Bag conforming to the [BagIt specification](https://www.ietf.org/rfc/rfc8493.txt). The top-level file directory will be as
follows:

```
├── bag-info.txt
├── bagit.txt
├── data
├── manifest-sha1.txt
└── tagmanifest-sha1.txt
```

These files will be generated automatically when using bag generator tools.

The `bag-info.txt` file must include the `BagIt-Profile-Identifier` tag in order for it to be validated.

The data folder will contain the digital object and its metadata. At the minimum, the data directory
should contain the following:

```
└── data
    ├── DigitalObject.tiff
    ├── DigitalObject_dmd.xml
    ├── DigitalObject_fits.xml
```

Where `DigitalObject.tiff` is the object itself saved in its original format.

## 2.2 Metadata

Descriptive and technical metadata must be included in the AIP. Other metadata files not specified here (such as
platform-specific metadata exported directly from a repository) may be included if they would provide additional
contextual information.

### Descriptive

Descriptive metadata should be represented following the Dublin Core schema. In Islandora repositories, Dublin Core
metadata can be exported using [Islandora Bagger](https://github.com/mjordan/islandora_bagger).

If full Dublin Core metadata is not included with the object at bag
creation, [DCMI Kernal Metadata](https://www.dublincore.org/groups/kernel/spec/) should be included for minimum
descriptive information.

The descriptive metadata file is identified by the "_dmd" appended to the filename.

### Technical

Technical information will be represented by [FITS metadata](https://projects.iq.harvard.edu/fits/home). 

FITS metadata files should follow the naming convention "ObjectName_fits.xml"


### Structural
Information about parent/child relationships between objects as well as arrangement for ordered content must be 
captured when relevant. When referencing an external object, use its persistent identifier (i.e., ARK) rather than a system-
specific identifier.

## 2.3 Taxonomies
Whenever possible, include either a URI pointing to taxonomy terms used (e.g. from [PCDM](https://pcdm.org/)), or define them within the metadata. 

Do not leave platform-specific taxonomy identifiers in the metadata without either translating them into terms or 
providing a URI containing a definition. 

# 3. Workflow

The AIP will be created as soon as possible after an object has been deposited. 

1. The AIP creator will create a Bag from the digital object. This can either be created directly from the repository 
 (i.e., with [Islandora bagger](https://github.com/mjordan/islandora_bagger)) or 
created from the object itself using a tool such as [Bagger](https://github.com/LibraryOfCongress/bagger) or [SFU Moveit](https://github.com/axfelix/moveit-electron).
   * Islandora Bagger will create the FITS and Dublin Core metadata files if specified in the configuration. 
2. Confirm the Bag contains the expected contents and structure detailed in [2. AIP Contents](#2-aip-contents). 
3. Enter the `BagIt-Profile-Identifier` in the `bag-info.txt` file. The `BagIt-Profile-Identifier` is the URI of the 
BagIt profile JSON file.
4. Run validation steps detailed in the [Validation](#4-validation) 
section. 

# 4. Validation
