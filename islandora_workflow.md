# Islandora AIP workflow

These are workflow details for SFU's Islandora repositories. 
For Summit, an AIP should include the thesis paper along with any supplemental materials associated with it (e.g. 
datasets, READMEs, codebooks).

Details about Islandora-specific metadata can be found in [Islandora Notes](/islandora_notes.md)

## Steps

The basic steps to create an AIP from an Islandora digital object are:

1. Create the configuration file for automated AIP creation. The basic configuration can be found in
   the [sample_config.yml](https://github.com/kpoloney/create_aip/blob/main/sample_config.yml) file.
2. Configure [Islandora Bagger](https://github.com/mjordan/islandora_bagger) in the YAML file. The basic YAML
   configuration for SFU AIPs can be found in the [sample_bagger_config.yml](https://github.com/kpoloney/create_aip/blob/main/sample_bagger_config.yml) file.
   1. Set `bag_name` to `uuid`.
   2. `bag-info` must contain all fields marked as required in the [SFU Bag Profile](https://github.com/kpoloney/create_aip/blob/main/sfu-bagit-profile.json).
   3. Ensure the following plugins are listed: `['AddBasicTags', 'AddMedia', 'AddNodeJson', 'AddMediaJson',
      'AddFileFromTemplate', 'AddFile']`.
3. The AIP creation steps are automated through the scripts in the [Create AIP](https://github.com/kpoloney/create_aip/) repository.
   1. Either manually get a list of node IDs to create AIPs from, or set the `get_nodes` argument to "True" in the 
      Mint Islandora ARK script. This also requires that the `date` argument be filled with the search date in 
      YYYYMMDD format.
   2. Mint ARKs for each object. Islandora ARKs are built using the SFU NAAN, public-facing shoulder, and the object's
      Drupal uuid. The ARK's ERC metadata is created from the object's node.json metadata. This step is automated in
      the [Mint Islandora ARK](https://github.com/kpoloney/create_aip/blob/main/mint_islandora_ark.py) script.
   3. Create a METS file for each object showing its hierarchical relationships and related objects. This is 
      automated in the [Islandora to METS](https://github.com/kpoloney/create_aip/blob/main/islandora_METS.py) 
      script.
   4. Create a bag using Islandora Bagger. This step is automated using the [make_bag_islandora.py](https://github.com/kpoloney/create_aip/blob/main/make_bag_islandora.py) script.
   5. Validate the bag's structure and contents.
