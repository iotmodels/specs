# Resolution Convention Spec

A model is composed by a set of interfaces identified by a unique id in the form of a DTMI.

The model repo stores one interface version per file following the structure defined below.

## Folder and File structure

A DTMI can be translated to a relative path with the following rules:

- Folder name is based on the DTMI
  - Convert to lower case invariant
  - Create a folder structure with a subfolder for each DTMI segment (separated by `:`)
- File name is the last DTMI segment plus the version number with the `.json` extension

```text
dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
```

Folders or files are associated to `CODEOWNERS` (Spec TBD) to ensure that any future updates are reviewed by the user who initially created the folder.

>Note: Once a DTMI is registered with a specific casing all other case variants will be stored under the same folder owned by the same user.

### Casing

Based on the file/folder rules, only one DTMI can be stored with specific casing.

:heavy_check_mark: Valid

```text
dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
dtmi:com:example:thermostat;2 -> dtmi/com/example/thermostat-2.json
```

:x: Invalid

```text
dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
dtmi:com:example:thermostat;1 -> dtmi/com/example/thermostat-1.json
```

### Relative Paths

Each IoT Model Repo will be accessible at a URL, where all paths are relative to the location of base repo URL.

For a model repo at `https://iotmodels.github.io/registry` the referenced interfaces will be available from:

```rest
https://iotmodels.github.io/registry/dtmi/com/example/thermostat-1.json
```

The same folder structure can be used locally with local file paths

```rest
C:\localModelRepo\dtmi\com\example\thermostat-1.json
```

## Unique `@id` elements

The model repo must guarantee that all `@id` included in the interfaces are unique accross the repo to avoid collisions when loading different models.

- `@id` elements from interfaces are guaranteed to be unique by the `model-index.json` file.
- All other `@id` elements from schemas, fields, and properties must be _under_ the root DTMI established by the interface.

:heavy_check_mark: Valid

```json
{
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:demo;1",
    "@type": "Interface",
    "contents": [
        {
            "@id": "dtmi:com:demo:name;1",
            "@type" : "Property",
            "name" : "name",
            "schema" : "string"
        }
    ]
  }
```

:x: Invalid

```json
{
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:demo;1",
    "@type": "Interface",
    "contents": [
        {
            "@id": "dtmi:com:notindemo:name;1",
            "@type" : "Property",
            "name" : "name",
            "schema" : "string"
        }
    ]
  }
```

>Note: During the validation process we will validate all the interfaces under the same folder do not contain duplicated `@id` elements.
