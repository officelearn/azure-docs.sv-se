---
title: Montera en Azure-filer i Azure Container instanser
description: "Lär dig hur du monterar en volym för Azure-filer för att bevara tillstånd med Azure Container instanser"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Montera en filresurs som Azure med Azure Container instanser

Som standard är Azure Behållarinstanser tillståndslösa. Om behållaren kraschar eller stoppar, förloras alla dess tillstånd. För att bevara tillståndet utöver livslängd för behållaren måste du ansluta en volym från en extern butik. Den här artikeln visar hur du monterar en Azure-filresursen för användning med Azure Container instanser.

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresursen med Azure Container instanser, måste du skapa den. Kör följande skript för att skapa ett lagringskonto som värd för filresursen och resursen sig själv. Lagringskontonamnet måste vara globalt unika, så skriptet lägger till ett slumpmässigt värde grundläggande sträng.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Hämta information om lagringskonto åtkomst

Om du vill montera en Azure-filresurs som en volym i Azure Container instanser, du behöver tre värden: lagringskontonamnet, resursnamnet och lagringsåtkomstnyckel.

Om du använder skriptet ovan, har namnet på lagringskontot skapats med ett slumpmässigt värde i slutet. Om du vill fråga sista strängen (inklusive den slumpmässiga delen), använder du följande kommandon:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Resursnamnet är redan känd (definieras som *acishare* i skriptet ovan), så att all som återstår är lagringskontonyckel som finns med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>Distribuera behållaren och montera volymen

Ange om du vill montera en Azure-filresurs som en volym i en behållare, resurs och volym monteringspunkt när du skapar behållaren med [az behållaren skapa](/cli/azure/container#az_container_create). Om du har följt de föregående stegen kan du montera den resurs som du skapade tidigare med hjälp av följande kommando för att skapa en behållare:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Hantera filer i monterade volymer

När behållaren startar, kan du använda en enkel webbapp som distribueras den [aci/seanmckenna-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/) bilden för att hantera filerna i Azure-filresursen på monteringssökväg som du angav. Skaffa IP-adressen för webbprogrammet med den [az behållaren visa](/cli/azure/container#az_container_show) kommando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

Du kan använda den [Azure-portalen](https://portal.azure.com) eller ett verktyg som den [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com) att hämta och granska filen skrivs till filresursen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om förhållandet mellan [Azure Behållarinstanser och behållare orchestrators](container-instances-orchestrator-relationship.md).
