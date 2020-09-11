# Model Index Spec

A model is composed by a set of interfaces identified by a unique id in the form of a DTMI.

The model repo stores one interface version per file following the structure defined below.

## Folder and File structure

A DTMI can be translated to a relative path with the following rules:

- Folder name is based on the DTMI
  - Remove the `dtmi` prefix
  - Replace `:` with `-`
  - Convert the lower case invariant
- File name is the last DTMI segment plust the version number with the `.json` extension

```text
dtmi:com:example:Thermostat;1 -> models/com-example/thermostat-1.json
```

Folders are associated to `CODEOWNERS` (Spec TBD) to ensure that any future updates are reviewed by the user who initially created the folder.

>Note: Once a DTMI is registered with a specific casing all other case variants will be stored under the same folder owned by the same user.

## Model Index

The `model-index.json` file contains a JSON Map with all the DTMI and relative paths.

```json
{
    "dtmi:com:example:Thermostat;1": {
        "path" : "models/com-example/thermostat-1.json"
    }
}
```

### Casing

The DTMI stored in the `model-index.json` is case sensitive, however based on the file/folder rules, only one DTMI can be stored with specific casing.

Allowed

```text
dtmi:com:example:Thermostat;1 -> models/com-example/thermostat-1.json
dtmi:com:example:thermostat;2 -> models/com-example/thermostat-2.json
```

Dissalowed

```text
dtmi:com:example:Thermostat;1 -> models/com-example/thermostat-1.json
dtmi:com:example:thermostat;1 -> models/com-example/thermostat-1.json
```

### Relative Paths

Each IoT Model Repo will be accessible at a URL, the `model-index.json` file must be accessible at the root, and MUST ensure that all paths are relative to the location of the file.

For a model repo at `https://iotmodels.github.io/registry` the index and the referenced interfaces will be available from:

```rest
https://iotmodels.github.io/registry/model-index.json
https://iotmodels.github.io/registry/models/com-example/thermostat-1.json
```

The same folder structure can be used locally with local file paths

```rest
C:\localModelRepo\model-index.json
C:\localModelRepo\models\com-example\thermostat-1.json
```

## Dependencies

If the interface has any dependencies, via `extends` or `component interfaces`, these IDs will be added to the `model-index.json` as dependencies for each id.

```json
{
    "dtmi:azure:DeviceManagement:DeviceInformation;1": {
        "path": "models/azure-devicemanagement/deviceinformation-1.json"
      },
    "dtmi:com:example:Thermostat;1": {
        "path" : "models/com-example/thermostat-1.json"
    },
    "dtmi:com:example:TemperatureController;1": {
        "path": "models/com-example/temperaturecontroller-1.json",
        "depends": [
          "dtmi:com:example:Thermostat;1",
          "dtmi:azure:DeviceManagement:DeviceInformation;1"
    ]
  }
}
```

## Unique `@id` elements

The model repo must guarantee that all `@id` included in the interfaces are unique accross the repo to avoid collisions when loading different models.

- `@id` elements from interfaces are guaranteed to be unique by the `model-index.json` file.
- All other `@id` elements from schemas, fields, and properties must be _under_ the root DTMI established by the interface.

Valid

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

Invalid

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
