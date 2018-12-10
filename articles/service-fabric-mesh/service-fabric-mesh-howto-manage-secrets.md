---
title: Hantera Azure Service Fabric Mesh-programhemligheter | Microsoft Docs
description: Hantera programhemligheter så att du på ett säkert sätt kan skapa och distribuera ett Service Fabric Mesh-program.
services: service-fabric-mesh
keywords: secrets
author: aljo
ms.author: aljo
ms.date: 11/28/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: d92726ebc2cd4c6c44afdb2d2a9f53ab5441ac32
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891969"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Hantera Service Fabric Mesh-programhemligheter
Service Fabric Mesh stöder hemligheter som Azure-resurser. En Service Fabric Mesh-hemlighet kan vara valfri känslig textinformation, till exempel lagringsanslutningssträngar, lösenord eller andra värden som bör lagras och överföras på ett säkert sätt. Den här artikeln visar hur du använder Säker lagringstjänst i Service Fabric för att distribuera och hantera hemligheter.

En Mesh-programhemlighet består av:
* En **hemlighetsresurs**, vilket är en container som lagrar texthemligheter. Hemligheter som ingår i **hemlighetsresursen** lagras och överförs på ett säkert sätt.
* En eller flera **hemlighets-/värderesurser** som lagras i **hemlighetsresurscontainern**. Varje **hemlighets-/värderesurs** identifieras med ett versionsnummer. Du kan inte ändra en version av en **hemlighets-/värderesurs**, bara lägga till en ny version.

Hantering av hemligheter består av följande steg:
1. Deklarera en Mesh-**hemlighetsresurs** i en YAML- eller JSON-fil för Azure-resursmodell med definitioner av inlinedValue som kind och SecretsStoreRef som contentType.
2. Deklarera Mesh-**hemlighets-/värderesurser** i en YAML- eller JSON-fil för Azure-resursmodell som lagras i **hemlighetsresursen** (från steg 1).
3. Ändra Mesh-programmet så att det refererar till Mesh-hemlighetsvärden.
4. Distribuera eller uppgradera löpande Mesh-programmet så att det använder hemlighetsvärden.
5. Använd Azure ”az” CLI-kommandon för livscykelhantering i Säker lagringstjänst.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarera en Mesh-hemlighetsresurs
En Mesh-hemlighetsresurs deklareras i en YAML- eller JSON-fil för Azure-resursmodell med definitioner av inlinedValue som kind och SecretsStoreRef som contentType. Mesh-hemlighetsresursen stöder ursprungshemligheter i Säker lagringstjänst. 
>
Följande är ett exempel på hur du deklarerar Mesh-hemlighetsresurser i en JSON-fil:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
Följande är ett exempel på hur du deklarerar Mesh-hemlighetsresurser i en YAML-fil:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarera Mesh-hemlighets-/värderesurser
Mesh-hemlighets-/värderesurser är beroende av de Mesh-hemlighetsresurser som definierats i föregående steg.

Om relationen mellan fälten ”value:” och ”name:” i avsnittet ”resources”: den andra delen av strängen ”name:” som avgränsas med ett kolon är versionsnumret som används för en hemlighet, och namnet före kolonet måste matcha Mesh-hemlighetsvärdet som det är beroende av. För elementet ```name: mysecret:1.0``` är till exempel versionsnumret 1.0 och namnet ```mysecret``` måste matcha ```"value": "mysecret"``` som definierats tidigare.

>
Följande är ett exempel på hur du deklarerar Mesh-hemlighets-/värderesurser i en JSON-fil:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
Följande är ett exempel på hur du deklarerar Mesh-hemlighets-/värderesurser i en YAML-fil:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Ändra Mesh-programmet så att det refererar till Mesh-hemlighetsvärden
Service Fabric Mesh-program måste känna till följande två strängar för att kunna använda hemlighetsvärden i Säker lagringstjänst:
1. Micrsoft.ServiceFabricMesh/Secrets.name innehåller namnet på filen och kommer att innehålla hemlighetsvärdet i klartext.
2. Windows- eller Linux-miljövariabeln ”Fabric_SettingPath” innehåller katalogsökvägen där filer som innehåller hemlighetsvärden i Säker lagringstjänst kommer att vara tillgängliga. Den är ”C:\Settings” för Mesh-program i Windows och ”/var/settings” i Linux.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Distribuera eller använda en löpande uppgradering för Mesh-programmet så att det använder hemlighetsvärden
Skapande av hemligheter och/eller versionshanterade hemligheter/värden är begränsat till resursmodelldeklarerade distributioner. Det enda sättet att skapa de här resurserna är genom att skicka en JSON- eller YAML-fil för resursmodell med kommandot **az mesh deployment** på följande sätt:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Azure CLI-kommandon för livscykelhantering i Säker lagringstjänst

### <a name="create-a-new-secrets-resource"></a>Skapa en ny hemlighetsresurs
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Skicka antingen **template-file** eller **template-uri** (men inte båda).

Exempel:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create -- https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Visa en hemlighet
Returnerar beskrivningen av hemligheten (men inte värdet).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Ta bort en hemlighet

- En hemlighet kan inte tas bort medan den refereras av ett Mesh-program.
- Om du tar bort en hemlighetsresurs tas alla versioner av hemligheter/resurser bort.
```azurecli-interactive
az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="list-secrets-in-subscription"></a>Visa lista över hemligheter i prenumeration
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Visa lista över hemligheter i resursgrupp
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Visa lista över alla versioner av en hemlighet
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Visa värde för hemlighetsversion
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Ta bort värde för hemlighetsversion
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)
