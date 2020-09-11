# GitHub Structure

We aim to build an open ecosystem of publisher and cosumers of DTDL models by creating an industry standard that is not directly associated to Azure or Microsoft.

## GitHub organization

By creating a GitHub organization we can leverage the next goals:

- Central directory of contributors
- Welcome to non-Microsoft collaborators
- Collection of related repositories for different platforms (clients/tools for Node, .NET, Javaand Python)

## Org/Repo Structure

We'd like to start with a top level org, eg `IoTModels` with the next repos underneath:

- `specs` Model Repository specs
- `registry` keeps the list of interfaces organized by DTMI
- `registry-tools` tools used to mantain the registry
- `dtdl-spec` DTDL specification
- `dtmi-spec` DTMI specification
- `dotnet-resolvers` .NET client to resolve models
- `node-resolvers` NodeJS client to resolver models
- .. more resolvers for other languages
