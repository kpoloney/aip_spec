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
## ERC Metadata

The minimum ERC metadata elements must be included as either a .txt file or a YAML file. If using the automated ARK minting script, the metadata must be formatted correctly in order to be parsed. The required elements (who, what, when) must be included. For a text 
file, elements must be listed one per line:
```
erc:
who: Lastname, Person
what: A dataset for a thesis paper
when: 2005-05-06
where: C:/Drive/Location
```

YAML files should contain the required elements as key/value pairs, using quotes around the values if special 
characters are used. If there are multiple creators, format the value as a list.
```yaml
erc:
who: ['Lastname, Firstname', 'SecondAuthor, Name']
what: 'A Research Paper: with a Subtitle'
when: '2020-04-05'
where: C:/Drive/Location
```


## Steps

Automation scripts for Local objects can be found in the [Local](https://github.com/kpoloney/create_aip/tree/main/Local) subfolder of the create_aip repository.

### 1. Create ERC metadata

This must be done before minting an ARK, and follow the naming convention:'ObjectFileName_erc'. The file can be saved as either a YAML file or a TXT file with each element on a new line.

If the object has metadata in AtoM, metadata generation can be automated using the [get_atom_metadata.py](https://github.com/kpoloney/create_aip/blob/main/Local/get_atom_metadata.py) script. The script uses AtoM's REST API to generate the ERC metadata and downloads the complete descriptive metadata from AtoM. 

Required input for the script includes: 
* The base URL for AtoM (e.g. https://atom.archives.sfu.ca/)
* AtoM slug of the object (e.g. msc-146 to create direct path to the object at https://atom.archives.sfu.ca/msc-146)
* Login information for AtoM 
* Path to the directory on the local drive which contains the digital object

The script's output is two metadata files in the format `AtoMSlug_erc.txt` and `AtoMSlug_atom_dmd.json`. By default, they are saved in the same location as the object.


### 2. Mint ARK

Mint an ARK for each object. Ensure that the ARK's ERC "where" field is the object's location on the local drive. This step is automated in the [mint_local_ark.py](https://github.com/kpoloney/create_aip/blob/main/Local/mint_local_ARK.py) script. In order for the ARK to be minted, an ERC metadata file must be saved in the same location as the object with `_erc` appended to the filename. 

Required input for the script includes:
* The base URL of larkm
* Path to the directory on the local drive which contains the digital object(s)
    - Multiple ARKs can be minted at once if the specified directory contains multiple digital objects with appropriate metadata.
    - For example, `path_to_directory` would be used for this file structure:
    ```
    path_to_directory
        └── Object1
            └── Object1.pdf
            └── Object1_erc.txt
        └── Object2
        └── Object3
    ```
* If processing a single file not in a directory (e.g. a TIFF alone), use the optional argument `single_obj=True`
    - The ERC file must be saved in the same location and have the same filename as the object.
    - This means that the file structure looks something like this:
    ```
    path_to_directory
        └── Object.TIFF
        └── Object_erc.txt
    ```
    
The script does not output a file, but will add the object's information to larkm and assign an ARK.

### 3. Create AIP bag

Create a bag conforming to the Library of Congress BagIt specification. The bag will contain the digital object and any metadata associated with it. The metadata will be stored in a subdirectory within the bag's `data` directory. The bag's name should be in the format `aip-uuid` where the uuid is the ARK identifier of the object. 

This step is automated in the [local objects script](https://github.com/kpoloney/create_aip/blob/main/Local/local_objects.py) in the aip creation tools.

The automation script has the following required input:
* Path to the directory containing digital objects to be bagged. This may be the same as the directory indicated in the ARK creation script.
    - Multiple objects can be bagged if the specified directory contains multiple digital objects with appropriate metadata and ARKs indexed in larkm.
* Path to an output directory where completed bags will be saved
* The base URL for larkm (for ARK lookup)

The script will output a bag for each object in the directory as long as the object has an ARK indexed in larkm.

### 4. Validate AIP

Confirm that a Bag is valid according to this specification. This includes validating that:
* The bag conforms to the BagIt specification
* Bag-info tags conform to the SFU BagIt Profile
* Bag content includes an ERC file
* The object has a valid ARK indexed in larkm

This step is automated in the [validate_aip.py](https://github.com/kpoloney/create_aip/blob/main/validate_aip.py) script. Multiple bags can be validated at once with the script and any errors will be noted in the log.
