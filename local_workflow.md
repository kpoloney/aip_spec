# Create AIP for objects stored locally

This workflow is specific to objects that will not be hosted online and are intended to remain on local storage.

For objects hosted on an Islandora repository, see [Islandora Workflow](/islandora_workflow.md)

## Local storage structure
Each digital object that will remain on local storage should be saved within its own folder. The folder should contain the digital object, a text file containing the ERC elements, and any other metadata to be included in the bag. Use the following naming conventions to ensure that metadata are identified correctly by the scripts:

* Top-level folder name: ObjectFileName
* Descriptive metadata: ObjectFileName_dmd.xml
* ERC elements: ObjectFileName_erc.txt

When starting the automation steps for local objects, your file structure should look something like this:

```
ItemsToBag
    └── Object1
        ├── Object1.tiff
        ├── Object1_erc.txt
        └── Object1_dmd.xml
    ├── Object2
    └── Object3    
```

The [local objects script](https://github.com/kpoloney/create_aip/blob/main/local_objects.py) will make a bag for each object directly under the `ItemsToBag` directory level. I.e., in the example above, three total bags will be made: one per object. A completed bag for a local object should look like this:

```
BagOutputDir
    └── aip-Object1UUID
        └── data
            ├── Object1.tiff
            └── metadata
                ├── Object1_erc.txt
                ├── Object1_fits.xml
                └── Object1_dmd.xml
        ├── bag-info.txt
        ├── bagit.txt
        ├── manifest-sha1.txt
        └── tagmanifest-sha1.txt
        
```

## Steps
1. Create the ERC metadata for each object. This must be done before minting an ARK, and follow the naming convention: 'ObjectFileName_erc.yml.' 
    1. This can be saved as either a YAML file or a TXT file with each element on a new line.
2. Mint an ARK for each object. Ensure that the ARK's ERC "where" field is the object's location on the local drive.
   1. This step is automated in the mint_local_ark script in the aip tools.
3. Create a bag for each object, using its ARK identifier as the bag folder name.
   1. This step is automated in the [local objects script](https://github.com/kpoloney/create_aip/blob/main/local_objects.py) in the aip creation tools.
4. Validate the contents and structure of the bag.
