---
title: Använda en Azure-filbaserad volym i en Service Fabric Mesh-app
description: Lär dig hur du lagrar tillstånd i ett Azure Service Fabric Mesh-program genom att montera en Azure Files-baserad volym i en tjänst med Hjälp av Azure CLI.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718828"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montera en Azure-filbaserad volym i ett Service Fabric Mesh-program 

I den här artikeln beskrivs hur du monterar en Azure-filbaserad volym i en tjänst för ett Service Fabric Mesh-program.  Azure Files-volymdrivrutinen är en Docker-volymdrivrutin som används för att montera en Azure-filresurs till en behållare som du använder för att bevara tjänsttillståndet. Volymer ger dig allmänt ändamål fillagring och kan du läsa / skriva filer med hjälp av vanliga disk I / O-fil API: er.  Om du vill veta mer om volymer och alternativ för att lagra programdata läser [du lagringstillstånd](service-fabric-mesh-storing-state.md).

Om du vill montera en volym i en tjänst skapar du en volymresurs i programmet Service Fabric Mesh och refererar sedan till den volymen i tjänsten.  Deklarera volymresursen och referera till den i tjänstresursen kan göras antingen i [YAML-baserade resursfiler](#declare-a-volume-resource-and-update-the-service-resource-yaml) eller i den [JSON-baserade distributionsmallen](#declare-a-volume-resource-and-update-the-service-resource-json). Innan du monterar volymen skapar du först ett Azure-lagringskonto och en [filresurs i Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Krav
> [!NOTE]
> **Känt problem med distribution på Windows RS5-utvecklingsdator:** Det finns ett öppet fel med Powershell cmdlet New-SmbGlobalMapping på RS5 Windows-datorer som förhindrar montering av Azurefile-volymer. Nedan visas exempelfel som påträffas när AzureFile-baserad volym monteras på den lokala utvecklingsdatorn.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Lösningen för problemet är att 1) Kör nedan kommando som Powershell administratör och 2) Starta om maskinen.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här artikeln. 

Om du vill använda Azure CLI lokalt `az --version` med `azure-cli (2.0.43)`den här artikeln kontrollerar du att returnerar minst .  Installera (eller uppdatera) azure service fabric mesh CLI-tilläggsmodulen genom att följa dessa [instruktioner](service-fabric-mesh-howto-setup-cli.md).

Så här loggar du in på Azure och anger din prenumeration:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Skapa ett lagringskonto och en filresurs (valfritt)
För att montera en Azure Files-volym krävs ett lagringskonto och en filresurs.  Du kan använda ett befintligt Azure-lagringskonto och filresurs eller skapa resurser:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Hämta namn och nyckel för lagringskonto och filresursnamnet
Lagringskontonamnet, lagringskontonyckeln och filresursnamnet `<storageAccountName>`refereras till som , `<storageAccountKey>`och `<fileShareName>` i följande avsnitt. 

Lista dina lagringskonton och hämta namnet på lagringskontot med den filresurs du vill använda:
```azurecli-interactive
az storage account list
```

Hämta namnet på filresursen:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Hämta lagringskontonyckeln ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Du kan också hitta dessa värden i [Azure-portalen:](https://portal.azure.com)
* `<storageAccountName>`- Under **Lagringskonton**, namnet på lagringskontot som används för att skapa filresursen.
* `<storageAccountKey>`- Välj ditt lagringskonto under **Lagringskonton** och välj sedan **Åtkomstnycklar** och använd värdet under **key1**.
* `<fileShareName>`- Välj ditt lagringskonto under **Lagringskonton** och välj sedan **Filer**. Namnet som ska användas är namnet på den filresurs du skapade.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarera en volymresurs och uppdatera tjänstresursen (JSON)

Lägg till `<fileShareName>`parametrar `<storageAccountName>`för `<storageAccountKey>` de värden som hittades i ett tidigare steg. 

Skapa en volymresurs som peer för programresursen. Ange ett namn och en provider ("SFAzureFile" för att använda den Azure Files-baserade volymen). I `azureFileParameters`anger du `<fileShareName>`parametrarna `<storageAccountName>`för `<storageAccountKey>` , och värden som hittades i ett tidigare steg.

Om du vill montera volymen `volumeRefs` i `codePackages` tjänsten lägger du till en i delslaget av tjänsten.  `name`är resurs-ID för volymen (eller en distributionsmallparameter för volymresursen) och namnet på volymen som deklarerats i resursfilen volume.yaml.  `destinationPath`är den lokala katalog som volymen ska monteras på.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
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
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarera en volymresurs och uppdatera tjänstresursen (YAML)

Lägg till en ny *volume.yaml-fil* i *appresurskatalogen* för ditt program.  Ange ett namn och en provider ("SFAzureFile" för att använda den Azure Files-baserade volymen). `<fileShareName>`och `<storageAccountName>` `<storageAccountKey>` är de värden som du hittade i ett tidigare steg.

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

Uppdatera *filen service.yaml* i katalogen *Service Resources* för att montera volymen i tjänsten.  Lägg `volumeRefs` till elementet i elementet. `codePackages`  `name`är resurs-ID för volymen (eller en distributionsmallparameter för volymresursen) och namnet på volymen som deklarerats i resursfilen volume.yaml.  `destinationPath`är den lokala katalog som volymen ska monteras på.

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

- Visa exempelprogrammet för Azure Files-volymen på [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Mer information om Service Fabric-resursmodellen finns i avsnittet om [Service Fabric Mesh-resursmodellen](service-fabric-mesh-service-fabric-resources.md).
- Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
