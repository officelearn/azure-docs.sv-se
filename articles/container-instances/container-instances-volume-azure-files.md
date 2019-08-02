---
title: Montera en Azure Files volym i Azure Container Instances
description: Lär dig hur du monterar en Azure Files volym för att spara tillstånd med Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325493"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillstånds lösa. Om behållaren kraschar eller stoppas, försvinner hela sitt tillstånd. Om du vill bevara tillstånd bortom behållarens livs längd måste du montera en volym från en extern lagrings plats. Den här artikeln visar hur du monterar en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md) för användning med Azure Container instances. Azure Files erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB-protokollet som är branschstandard. Med hjälp av en Azure-filresurs med Azure Container Instances får du fildelnings funktioner som liknar att använda en Azure-filresurs med Azure Virtual Machines.

> [!NOTE]
> Montering av en Azure Files-resurs är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du använder en Azure-filresurs med Azure Container Instances måste du skapa den. Kör följande skript för att skapa ett lagrings konto som är värd för fil resursen och själva resursen. Lagrings kontots namn måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde i bas strängen.

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

Om du vill montera en Azure-filresurs som en volym i Azure Container Instances behöver du tre värden: lagrings kontots namn, resurs namnet och lagrings åtkomst nyckeln.

Om du använde skriptet ovan lagras lagrings konto namnet i variabeln $ACI _PERS_STORAGE_ACCOUNT_NAME. Om du vill se konto namnet skriver du:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Resurs namnet är redan känt (definieras som *acishare* i skriptet ovan), så allt det kvarstår är lagrings konto nyckeln, som kan hittas med följande kommando:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuera behållare och montera volym – CLI

Om du vill montera en Azure-filresurs som en volym i en behållare med hjälp av Azure CLI anger du resurs-och volym monterings punkt när du skapar behållaren med [AZ container Create][az-container-create]. Om du har följt de föregående stegen kan du montera resursen som du skapade tidigare genom att använda följande kommando för att skapa en behållare:

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

`--dns-name-label` Värdet måste vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i föregående kommando om du får ett fel meddelande om **DNS-namns etikett** när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer på monterad volym

När behållaren har startats kan du använda den enkla webbappen som distribueras via Microsoft [ACI-hellofiles-][aci-hellofiles] avbildningen för att skapa små textfiler i Azure-filresursen på den monterings Sök väg du angav. Hämta webbappens fullständigt kvalificerade domän namn (FQDN) med kommandot [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

När du har sparat text med appen kan du använda [Azure Portal][portal] eller ett verktyg som [Microsoft Azure Storage Explorer][storage-explorer] för att hämta och granska filen som skrivits till fil resursen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuera behållare och montera volym – YAML

Du kan också distribuera en behållar grupp och montera en volym i en behållare med Azure CLI och en [yaml-mall](container-instances-multi-container-yaml.md). Att distribuera med YAML-mall är den bästa metoden när du distribuerar behållar grupper som består av flera behållare.

Följande yaml-mall definierar en behållar grupp med en behållare som `aci-hellofiles` skapats med avbildningen. Behållaren monterar *acishare* för Azure-filresursen som skapades tidigare som en volym. Ange namnet och lagrings nyckeln för det lagrings konto som är värd för fil resursen. 

Som i CLI-exemplet måste `dnsNameLabel` värdet vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i YAML-filen om det behövs.

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

Om du vill distribuera med yaml-mallen sparar du föregående yaml till en fil `deploy-aci.yaml`med namnet och kör sedan kommandot [AZ container Create][az-container-create] med `--file` parametern:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuera behållare och montera volym resurs hanterare

Förutom CLI-och YAML-distribution kan du distribuera en behållar grupp och montera en volym i en behållare med hjälp av en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll `volumes` först i matrisen i avsnittet behållar grupp `properties` i mallen. 

För varje behållare där du vill montera volymen fyller `volumeMounts` du sedan `properties` i matrisen i avsnittet i behållar definitionen.

Följande Resource Manager-mall definierar en behållar grupp med en behållare som `aci-hellofiles` skapats med avbildningen. Behållaren monterar *acishare* för Azure-filresursen som skapades tidigare som en volym. Ange namnet och lagrings nyckeln för det lagrings konto som är värd för fil resursen. 

Som i föregående exempel `dnsNameLabel` måste värdet vara unikt i den Azure-region där du skapar behållar instansen. Uppdatera värdet i mallen om det behövs.

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

Om du vill distribuera med Resource Manager-mallen sparar du föregående JSON till en fil `deploy-aci.json`med namnet och kör sedan kommandot [AZ Group Deployment Create][az-group-deployment-create] med `--template-file` parametern:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållar instans måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups) eller en yaml-fil. Om du vill använda en mall eller en yaml-fil anger du resurs informationen och definierar volymerna `volumes` genom att fylla `properties` i matrisen i avsnittet i mallen. 

Om du till exempel har skapat två Azure Files-resurser med namnet *Share1* och *share2* i lagrings kontot `volumes` *myStorageAccount*, skulle matrisen i en Resource Manager-mall se ut ungefär så här:

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

Därefter, för varje behållare i den behållar grupp där du vill montera volymerna, fyller `volumeMounts` du `properties` i matrisen i avsnittet i behållar definitionen. Detta monterar till exempel de två volymerna, *myvolume1* och *myvolume2*, som tidigare definierats:

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

Lär dig hur du monterar andra volym typer i Azure Container Instances:

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