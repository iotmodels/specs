# Model Index Spec

A model is composed by a set of interfaces identified by a unique id in the form of a DTMI. 
The model repo stores one interface version per file following the structure defined below.

## Folder and File structure

DTMIs are case sensitive, the index keeps the case sensitive IDs but the folder names will be lowercase, the file name will be just the version with the `.json` extension.

A DTMI like `dtmi:com:example:Thermostat;1` MUST be stored in the following path:

```text
models/com-example-thermostat/1.json
```

Folders are associated to `CODEOWNERS` (Spec TBD) to ensure that any future updates are reviewed by the user who initially created the file.

>Note: Once a DTMI is registered with specific casing all other case variants will be dissalowed.

## Model Index

The `model-index.json` file contains a JSON Map with all the DTMI that can be resolved by using the repo where the file is located.

```json
{
    "dtmi:com:example:Thermostat;1": {
        "path" : "models/com-example-thermostat/1.json"
    }
}
```

### File Location

Each IoT Model Repo will be accessible at a URL, the `model-index.json` file must be accessible at the root, and MUST ensure that all paths are relative to the location of the file.

For a model repo url of `https://iotmodels.github.io/registry` the index and the referenced interfaces will be available from:

- `https://iotmodels.github.io/registry/model-index.json`
- `https://iotmodels.github.io/registry/models/com-example-thermostat/1.json`

## Dependencies

If the interface has any dependencies, via `extends` or `component interfaces`, these IDs will be added to the `model-index.json` file.

```json
{
    "dtmi:azure:DeviceManagement:DeviceInformation;1": {
        "path": "models/azure-devicemanagement-deviceinformation/1.json"
      },
    "dtmi:com:example:Thermostat;1": {
        "path" : "models/com-example-thermostat/1.json"
    },
    "dtmi:com:example:TemperatureController;1": {
        "path": "models/com-example-temperaturecontroller/1.json",
        "depends": [
          "dtmi:com:example:Thermostat;1",
          "dtmi:azure:DeviceManagement:DeviceInformation;1"
    ]
  }
}
```

## Other resolvable @id

DTDL allows to specify `@id` at any level in the document, for schemas, properties and fields. To guarantee that all speficied `@id`s are public, the index will contain a first level entry for each dtmi pointing to file with the definition.

Given the next interface:

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

will produce the next entries in the `model-index.json` file:

```json
    "dtmi:com:demo;1": {
        "path" : "models/com-demo/1.json"
    },
    "dtmi:com:demo:name;1": {
        "path" : "models/com-demo/1.json"
    }
```
