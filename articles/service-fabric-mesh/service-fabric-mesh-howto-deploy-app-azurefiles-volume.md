---
title: Använda en Azure-filer baserat volym i ett Service Fabric-nät program | Microsoft Docs
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric nät program genom att montera en Azure-filer baserat volym inuti en tjänst med Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9bce2d0e6d01813fd376b2505838defc9c772d70
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891103"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montera en Azure-filer baserat volym i ett Service Fabric-nät program 

Den här artikeln beskriver hur du monterar en Azure-filer baserat volym i en tjänst i ett Service Fabric-nät program.  Azure Files volym drivrutinen är en drivrutin för Docker-volym som används för att montera en Azure Files-resurs till en behållare som du använder för att bevara tillståndet för tjänsten. Volymer ger dig allmänna fillagring och gör att du kan läsa/skriva filer med hjälp av normal disk-i/o-fil som API: er.  Läs mer om volymer och alternativ för att lagra programdata [lagra tillstånd](service-fabric-mesh-storing-state.md).

Om du vill montera en volym i en tjänst, skapa en volym-resurs i ditt Service Fabric-nät program och sedan referera till den volymen i din tjänst.  Deklarera volymresursen och refererar till den i tjänstresursen kan göras antingen i den [YAML-baserade resursfiler](#declare-a-volume-resource-and-update-the-service-resource-yaml) eller [JSON-baserade Distributionsmall](#declare-a-volume-resource-and-update-the-service-resource-json). Innan du monterar volymen först skapa ett Azure storage-konto och en [filresurs i Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. 

Om du vill använda Azure CLI lokalt med den här artikeln, kontrollerar du att `az --version` returnerar minst `azure-cli (2.0.43)`.  Installera (eller uppdatera) Azure Service Fabric nät CLI-tillägg-modulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

Logga in på Azure och ange din prenumeration:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Skapa en resurs för kontot och (valfritt)
Montera en Azure Files-volym kräver en storage-konto och filresursen.  Du kan använda ett befintligt Azure storage-konto och filresursen eller skapa resurser:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Hämta lagringskontonamn och nyckel och namn på filresurs
Namnet på lagringskontot och lagringskontonyckeln filresursens namn är refereras till som `<storageAccountName>`, `<storageAccountKey>`, och `<fileShareName>` i följande avsnitt. 

Lista dina lagringskonton och hämta namnet på lagringskontot med filresursen som du vill använda:
```azurecli-interactive
az storage account list
```

Hämta namnet på filresursen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Hämta lagringskontonyckeln (”key1”):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Du kan också hitta de här värdena i den [Azure-portalen](https://portal.azure.com):
* `<storageAccountName>` -Under **Lagringskonton**, namnet på lagringskontot som används för att skapa filresursen.
* `<storageAccountKey>` -Välj ditt lagringskonto under **Lagringskonton** och välj sedan **åtkomstnycklar** och använda värde under **key1**.
* `<fileShareName>` -Välj ditt lagringskonto under **Lagringskonton** och välj sedan **filer**. Namn är namnet på den filresurs som du skapade.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarera en volymresursen och uppdatera tjänstresurs (JSON)

Lägga till parametrar för den `<fileShareName>`, `<storageAccountName>`, och `<storageAccountKey>` värden som du hittade i föregående steg. 

Skapa en volym-resurs som en peer-datorn om programresursen. Ange ett namn och provider (”SFAzureFile” att använda Azure Files baserat volym). I `azureFileParameters`, anger du parametrarna för den `<fileShareName>`, `<storageAccountName>`, och `<storageAccountKey>` värden som du hittade i föregående steg.

Om du vill montera volymen i din tjänst, lägger du till en `volumeRefs` till den `codePackages` element i tjänsten.  `name` är resurs-ID för volymen (eller en mallparameter i distributionen för volymresursen) och namnet på volymen som har deklarerats i resursfilen volume.yaml.  `destinationPath` är den lokala katalogen som volymen ska monteras till.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide a empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarera en volymresursen och uppdatera tjänstresurs (YAML)

Lägga till en ny *volume.yaml* fil i den *appresurser* katalogen för ditt program.  Ange ett namn och provider (”SFAzureFile” att använda Azure Files baserat volym). `<fileShareName>`, `<storageAccountName>`, och `<storageAccountKey>` är de värden som du hittade i föregående steg.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Uppdatera den *service.yaml* fil i den *tjänstresurser* directory för att montera volymen i din tjänst.  Lägg till den `volumeRefs` elementet så att den `codePackages` element.  `name` är resurs-ID för volymen (eller en mallparameter i distributionen för volymresursen) och namnet på volymen som har deklarerats i resursfilen volume.yaml.  `destinationPath` är den lokala katalogen som volymen ska monteras till.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Nästa steg

- Visa Azure Files volym exempelprogrammet på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
