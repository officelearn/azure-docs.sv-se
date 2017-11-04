---
title: "Montera en volym för Azure-filer i Azure Container instanser"
description: "Lär dig hur du monterar en volym för Azure-filer för att bevara tillstånd med Azure Container instanser"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41c3a449b39d6ef77e1dd0cf10699f8debcad475
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Montera en filresurs som Azure med Azure Container instanser

Som standard är Azure Behållarinstanser tillståndslösa. Om behållaren kraschar eller stoppar, förloras alla dess tillstånd. För att bevara tillståndet utöver livslängd för behållaren måste du ansluta en volym från en extern butik. Den här artikeln visar hur du monterar en Azure-filresursen för användning med Azure Container instanser.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresursen med Azure Container instanser, måste du skapa den. Kör följande skript för att skapa ett lagringskonto som värd för filresursen och resursen sig själv. Lagringskontonamnet måste vara globalt unika, så skriptet lägger till ett slumpmässigt värde grundläggande sträng.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Hämta information om lagringskonto åtkomst

Om du vill montera en Azure-filresurs som en volym i Azure Container instanser, du behöver tre värden: lagringskontonamnet, resursnamnet och lagringsåtkomstnyckel.

Om du använder skriptet ovan, har namnet på lagringskontot skapats med ett slumpmässigt värde i slutet. Om du vill fråga sista strängen (inklusive den slumpmässiga delen), använder du följande kommandon:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Resursnamnet är redan känd (det är *acishare* i skriptet ovan), så att all som återstår är lagringskontonyckel som finns med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Lagra information om lagringskonto åtkomst med Azure key vault

Lagringskontonycklar skydda åtkomst till dina data, så vi rekommenderar att du lagrar dem i en Azure key vault.

Skapa ett nyckelvalv med Azure CLI:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

Den `enabled-for-template-deployment` växel kan Azure Resource Manager pull hemligheter från nyckelvalvet vid tidpunkten för distribution.

Lagra lagringskontots åtkomstnyckel som en ny hemlighet i nyckelvalvet:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Montera volymen

Montera en Azure-filresurs som en volym i en behållare är en tvåstegsprocess. Först du ange information om resursen som en del av definiera behållargruppen sedan hur du vill volymen monterade i en eller flera behållare i gruppen.

Om du vill definiera de volymer som du vill göra tillgängliga för montering, lägger du till en `volumes` matris till behållaren Gruppdefinition i Azure Resource Manager-mall och sedan refererar till dem i definitionen av enskilda behållare.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

Mallen innehåller lagringskontonamn och nyckel som parametrar som kan finnas i en separat parameterfil. Om du vill fylla i parameterfilen du behöver tre värden: lagringskontonamnet resurs-ID för din Azure key vault och det hemliga namnet för nyckelvalv som du använde för att lagra nyckel för säkerhetslagring. Om du har följt de föregående stegen kan hämta du dessa värden med följande skript:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Infoga värden i parameterfilen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Distribuera behållaren och hantera filer

Du kan skapa behållaren och montera dess volymen med hjälp av Azure CLI med hjälp av mallen som definierats. Förutsatt att mallfilen heter *azuredeploy.json* och som heter parameterfilen *azuredeploy.parameters.json*, och sedan kommandoraden är:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

När behållaren startar, kan du använda en enkel webbapp som distribueras den **aci/seanmckenna-hellofiles** svår att hantera filerna i Azure-filresursen på monteringssökväg som du angav. Hämta IP-adressen för webbappen via följande:

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

Du kan använda ett verktyg som den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) att hämta och granska filen skrivs till filresursen.

>[!NOTE]
> Mer information om hur du använder Azure Resource Manager-mallar parametern filer och distribuera med Azure CLI, se [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Nästa steg

- Distribuera för din första behållare med Azure Container instanser [Snabbstart](container-instances-quickstart.md)
- Lär dig mer om den [förhållandet mellan Azure Behållarinstanser och behållare orchestrators](container-instances-orchestrator-relationship.md)
