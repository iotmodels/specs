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

>Note: Once a DTMI is registered with specific casing all other case variants will be stored under the same folder, owned by the same user.

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

If the interface has any dependencies, via `extends` or `component interfaces`, these IDs will be added to the `model-index.json` as dependencies for each id.

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

DTDL v2 allows to reuse elements by adding and `@id` that can be referenced in any other interface, this features implies that any element (schema, property, command, interface) with an `@id` will be publicily available, hence _resolvable_.

The first version of the model repo will block the submission of models that use `@id` for any element that is not the top level interface of the file being submitted.

>Note: Inline schemas will be accepted as long they dont use an `@id`

### Sample interfaces that will be blocked in the first model repo release

Id not allowed in properties

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

Id not allowed in schemas (that means schemas are not allowed since Id is mandatory)

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:InterfaceA;1",
  "@type": "Interface",
  "schemas": [
    {
      "@type": "Object",
      "@id": "dtmi:com:example:InterfaceA:person;1",
      "fields": [
        { "name": "firstName", "schema": "string"},
        { "name": "lastName", "schema": "string"},
        { "name": "age","schema": "integer"}
      ]
    }
  ]
}
```

Id not allowed in fields

```json
{
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:InterfaceB;1",
    "@type": "Interface",
    "contents": [
        {
            "@type": "Property",
            "name": "aPerson",
            "schema": {
                "@type": "Object",
                "fields": [
                  {
                      "@id": "dtmi:com:example:InterfaceB:person:name;1",
                      "name": "firstName", 
                      "schema": "string"
                    },
                  { "name": "lastName", "schema": "string"},
                  { "name": "age", "schema": "integer"}
                ]
            }
        }
    ]
}
```
