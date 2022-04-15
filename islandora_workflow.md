# Islandora AIP workflow

These are workflow details for SFU's Islandora repositories (Summit, Digital Collections). 

For Summit, an AIP should include the thesis paper along with any supplemental materials associated with it.

## Steps

The basic steps to create an AIP from an Islandora digital object are:

1. Get a list of node IDs to create AIPs from.
2. Confirm ARKs for each object. If an object or its supplemental file(s) do not have an ARK, mint one
   using [larkm](https://github.com/mjordan/larkm). Islandora ARKs are built using the SFU NAAN, public-facing shoulder,
   and the object's Drupal uuid.
3. Run the [Islandora to METS](https://github.com/kpoloney/islandora_to_mets) script to generate METS XML files for each
   object.
4. Configure [Islandora Bagger](https://github.com/mjordan/islandora_bagger) in the YAML file.
   1. Set `bag_name` to `uuid`.
   2. bag-info <!--- conform to bagit profile tbd. Maybe use Kernal md here --->
   3. Ensure the following plugins are included: `AddNodeJson`, `AddMediaJson`, and `AddFile`.
   4. Under the `files_to_add` configuration option, list the object's associated METS file.

#### Note on SFU Summit

Object relationships are represented in the `node.json` and `members.json` files in Summit. 

1. Use Islandora Bagger with the AddMedia plugin to include `media.json` and `media_use_summary.tsv` when creating a
   Bag.
2. In `node.json`, the `"field_member_of" : [{"target_uuid":}]` item contains the Islandora identifier for the parent
   object.
3. To get information about the object's child items, query the `members.json` metadata from the node (e.g. using the
   url format `node/0000/members?_format=json`, where the 000s are the Islandora node id number).
4. In `members.json`, the Islandora identifier and uuids are listed for the original node's child objects. The
   information can be found in the `"uuid" : [{"value":}]` item.

### Islandora identifiers

Islandora metadata contains platform-specific identifiers which point to digital objects, their parent objects, and 
object relationships. The following are relevant identifiers in SFU's Islandora repositories and can be extracted using 
Islandora Bagger.

In `node.json`:
* `uuid`: This field is the Islandora identifier for the object. It should be translated to the object's ARK, or an ARK 
should be created if one doesn't already exist. 
* `field_member_of`: The `target_uuid` is the Islandora identifier for the parent object.

In `members.json`:

* `uuid`: The value is the Islandora identifier for the object. There will be a UUID for each child object listed in the
  file.

`media.json` contains information about media files connected to the original object (e.g. thumbnails, extracted text):

* `field_media_of`: The `target_uuid` here is the identifier of the main object. It should be the same as the `uuid`
  in `node.json`.
* `field_media_use`: This field contains the taxonomy identifiers for the term describing the file's relationship to the
  main object. The `url` item here is used to look up the taxonomy metadata.

Secondary lookup `taxonomy.json` (using the url `/taxonomy/term/xx?_format=json` where "xx" is the `target_id` in
`field_media_use` or `field_model`):

* `field_external_uri`: Contains `url` which directs to the description of the taxonomy term.
* `name` and `description`: These values are the taxonomy term and its description in human-readable text.
