---
title: Montera en Azure-filer i Azure Container instanser
description: "Lär dig hur du monterar en volym för Azure-filer för att bevara tillstånd med Azure Container instanser"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 98be7e65c2280aa58cf904cbca265f87610eff55
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresursen i Azure Container instanser

Som standard är Azure Behållarinstanser tillståndslösa. Om behållaren kraschar eller stoppar, förloras alla dess tillstånd. För att bevara tillståndet utöver livslängd för behållaren måste du ansluta en volym från en extern butik. Den här artikeln visar hur du monterar en Azure-filresursen för användning med Azure Container instanser.

> [!NOTE]
> Montera en resurs i Azure Files är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresursen med Azure Container instanser, måste du skapa den. Kör följande skript för att skapa ett lagringskonto som värd för filresursen och resursen sig själv. Lagringskontonamnet måste vara globalt unika, så skriptet lägger till ett slumpmässigt värde grundläggande sträng.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>Hämta autentiseringsuppgifter för lagring

Om du vill montera en Azure-filresurs som en volym i Azure Container instanser, du behöver tre värden: lagringskontonamnet, resursnamnet och lagringsåtkomstnyckel.

Om du använder skriptet ovan, har namnet på lagringskontot skapats med ett slumpmässigt värde i slutet. Om du vill fråga sista strängen (inklusive den slumpmässiga delen), använder du följande kommandon:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

Resursnamnet är redan känd (definieras som *acishare* i skriptet ovan), så att all som återstår är lagringskontonyckel som finns med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Distribuera behållare och montera volymen

Ange om du vill montera en Azure-filresurs som en volym i en behållare, resurs och volym monteringspunkt när du skapar behållaren med [az behållaren skapa][az-container-create]. Om du har följt de föregående stegen kan du montera den resurs som du skapade tidigare med hjälp av följande kommando för att skapa en behållare:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image microsoft/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Den `--dns-name-label` värdet måste vara unikt i Azure-regionen du skapar behållaren-instans. Uppdatera värdet i kommandot ovan om du får en **DNS-namnetikett** felmeddelande när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer i monterade volymer

När behållaren startar, kan du använda en enkel webbapp som distribueras den [aci/microsoft-hellofiles] [ aci-hellofiles] bilden för att hantera filerna i Azure-filresursen på monteringssökväg som du angav. Hämta webbappens fullständigt kvalificerade domännamnet (FQDN) med den [az behållaren visa] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Du kan använda den [Azure-portalen] [ portal] eller ett verktyg som den [Microsoft Azure Lagringsutforskaren] [ storage-explorer] att hämta och granska filen skrivs till filresursen.

## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållare instans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först innehåller dela information och definiera volymer genom att fylla i `volumes` matris i den `properties` avsnitt i mallen. Till exempel om du har skapat två Azure filresurser med namnet *share1* och *share2* i lagringskonto *Mittlagringskonto*, `volumes` matris visas liknar följande:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Därefter för varje behållare i behållargruppen där du vill montera volymerna fylla den `volumeMounts` matris i den `properties` avsnitt i definition av behållare. Till exempel detta monterar två volymer *myvolume1* och *myvolume2*, tidigare definierad:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Ett exempel på distribution av behållare-instans med en Azure Resource Manager-mall finns i [distribuera flera behållare grupper i Azure Behållarinstanser](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper av i Azure Container instanser:

* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container instanser](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show