---
title: Montera en Azure Files-volym i Azure Container Instances
description: Lär dig hur du monterar en Azure Files-volym för att bevara tillstånd med Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657628"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillståndslösa. Om behållaren kraschar eller stoppas, förloras alla dess tillstånd. För att bevara tillstånd bortom livslängden för behållaren, måste du ansluta en volym från en extern lagring. Den här artikeln visar hur du monterar en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md) för användning med Azure Container Instances. Azure Files erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Med hjälp av en Azure-filresurs med Azure Container Instances innehåller fildelning funktioner ungefär som att använda en Azure-filresurs med Azure virtual machines.

> [!NOTE]
> Montera en Azure Files-resurs är för närvarande begränsade till Linux-behållare. När vi arbetar för att göra alla funktioner till Windows-behållare kan du hitta nuvarande skillnaderna i den [översikt](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresurs med Azure Container Instances, måste du skapa den. Kör följande skript för att skapa ett lagringskonto för att vara värd för filresursen och dela själva. Lagringskontonamnet måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde till strängen som bas.

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

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Hämta autentiseringsuppgifter för lagring

Om du vill montera en Azure-filresurs som en volym i Azure Container Instances, du behöver tre värden: namnet på lagringskontot, resursnamnet och åtkomstnyckel för lagring.

Om du använder skriptet ovan lagrades lagringskontonamnet i variabeln $ACI_PERS_STORAGE_ACCOUNT_NAME. Om du vill se namnet på kontot, skriver du:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Resursnamnet redan är känd (definieras som *acishare* i skriptet ovan), så att alla som återstår är nyckeln till lagringskontot, som finns med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuera behållare och montera volymen – CLI

Ange om du vill montera en Azure-filresurs som en volym i en behållare med hjälp av Azure CLI, resurs och volym monteringspunkt när du skapar behållaren med [az container skapa][az-container-create]. Om du har följt de föregående stegen, kan du montera filresursen du skapade tidigare med hjälp av följande kommando för att skapa en behållare:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Den `--dns-name-label` värdet måste vara unikt inom Azure-region där du skapar behållarinstansen. Uppdatera värdet i föregående kommando om du får en **DNS-Namnetiketten** felmeddelande när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer i monterad volym

När behållaren startas, kan du använda enkla webbappen distribueras via Microsoft [aci hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] kommando:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

När du har sparat text med appen, kan du använda den [Azure-portalen][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] att hämta och granska filen skrivs till filresursen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuera behållare och montera volymen - YAML

Du kan också distribuera en behållargrupp och montera en volym i en behållare med Azure CLI och en [YAML mallen](container-instances-multi-container-yaml.md). Distribution av YAML-mallen är den bästa metoden när du distribuerar behållargrupper som består av flera behållare.

Följande YAML-mall definierar en behållargrupp med en behållare som skapats med den `aci-hellofiles` bild. Behållaren monterar Azure-filresursen *acishare* skapade tidigare som en volym. Där det anges, ange och nyckeln för lagringskontot som är värd för filresursen. 

Som i CLI-exempel i `dnsNameLabel` värdet måste vara unikt inom Azure-region där du skapar behållarinstansen. Uppdatera värdet i YAML-fil om det behövs.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

För att distribuera med mallen YAML, spara den föregående YAML till en fil med namnet `deploy-aci.yaml`, kör du den [az container skapa][az-container-create] med den `--file` parameter:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuera behållare och montera volym – Resource Manager

Förutom CLI och YAML-distributionen som du kan distribuera en behållargrupp och montera en volym i en behållare med en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. 

Sedan för varje behållare där du vill montera volymen, fylla det `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen.

Följande Resource Manager-mall definierar en behållargrupp med en behållare som skapats med den `aci-hellofiles` bild. Behållaren monterar Azure-filresursen *acishare* skapade tidigare som en volym. Där det anges, ange och nyckeln för lagringskontot som är värd för filresursen. 

Som i föregående exempel kan den `dnsNameLabel` värdet måste vara unikt inom Azure-region där du skapar behållarinstansen. Uppdatera värdet i mallen om det behövs.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

För att distribuera med Resource Manager-mallen, spara ovanstående JSON till en fil med namnet `deploy-aci.json`, kör du den [az group deployment skapa][az-group-deployment-create] med den `--template-file` parameter:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållarinstans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups) eller en YAML-fil. Om du vill använda en mall eller YAML-fil, ange resursinformationen och definiera volymer genom att fylla i `volumes` matrisen i den `properties` avsnitt i mallen. 

Exempel: Om du har skapat två Azure-filresurser med namnet *share1* och *share2* i storage-konto *myStorageAccount*, `volumes` matris i Resource Manager mallen skulle se ut ungefär så här:

```JSON
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

Därefter för varje behållare i behållargruppen där du vill montera volymerna fylla det `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen. Till exempel detta monterar två volymer *myvolume1* och *myvolume2*, tidigare definierad:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create