# Islandora AIP workflow

These are workflow details for SFU's Islandora repositories. 
For Summit, an AIP should include the thesis paper along with any supplemental materials associated with it (e.g. 
datasets, READMEs, codebooks).

Details about Islandora-specific metadata can be found in [Islandora Notes](/islandora_notes.md)

# Steps

The AIP creation steps are automated through the scripts in the [Create AIP](https://github.com/kpoloney/create_aip/)
repository. These steps can also be completed manually, but automation allows for AIPs to be created in bulk from a 
list of node IDs.

## 1. AIP Configuration

Create the configuration file for automated AIP creation. The basic configuration can be found in the README of the 
[Create AIP](https://github.com/kpoloney/create_aip/) repository. The contents of the configuration file are used as 
arguments in the automation scripts.

## 2. Islandora Bagger setup

Configure [Islandora Bagger](https://github.com/mjordan/islandora_bagger) in the YAML file.
   1. Set `bag_name` to `uuid`.
   2. `bag-info` must contain all fields marked as required in the [SFU Bag Profile](https://github.com/kpoloney/create_aip/blob/main/sfu-bagit-profile.json).
   3. Ensure the following plugins are listed: `['AddBasicTags', 'AddMedia', 'AddNodeJson', 'AddMediaJson',
   'AddFileFromTemplate']`.

## 3. Get Node IDs

Either manually get a list of node IDs to create AIPs from, or set the `get_nodes` argument to "True" in the 
[mint_islandora_ark.py](https://github.com/kpoloney/create_aip/blob/main/Islandora/mint_islandora_ARK.py) script to 
generate 
the list automatically. This requires the `date` argument to be filled with the search date in YYYYMMDD format.

## 4. Mint ARKS

Mint ARKs for each object. Islandora ARKs are built using the SFU NAAN, public-facing shoulder, and the object's 
Drupal uuid. The ARK's ERC metadata is created from the object's node.json metadata. This step is automated in the 
[mint_islandora_ark.py](https://github.com/kpoloney/create_aip/blob/main/Islandora/mint_islandora_ARK.py) script.

## 5. Create METS metadata

Create a METS file for each object showing its hierarchical relationships and related objects. This is automated in 
the [Islandora to METS](https://github.com/kpoloney/create_aip/blob/main/Islandora/islandora_METS.py) script. 

If a METS file is created, the Islandora Bagger plugin `AddFiles` should be added to include it in the bag. It is 
automatically added in the 
[make_bag_islandora.py](https://github.com/kpoloney/create_aip/blob/main/Islandora/make_bag_islandora.py) script if the 
configuration file has a valid directory for the `mets_dir` value.

## 6. Create Bag

Create a bag using Islandora Bagger. This step is automated using the [make_bag_islandora.py](https://github.com/kpoloney/create_aip/blob/main/Islandora/make_bag_islandora.py) script.

## 7. Validate AIP

Validate the bag's structure and contents. This step is automated in the [validate_aip.py](https://github.com/kpoloney/create_aip/blob/main/validate_aip.py)
script. 

To automatically scan for infected files, use the optional `--clamav` argument at this step to specify the directory 
location of ClamAV software. 
