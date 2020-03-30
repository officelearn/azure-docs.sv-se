---
title: Montera Azure Files-volym till behållargrupp
description: Lär dig hur du monterar en Azure Files-volym för att bevara tillståndet med Azure Container Instances
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561345"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Montera en Azure-filresurs i Azure Container Instances

Som standard är Azure Container Instances tillståndslösa. Om containern kraschar eller stoppas förloras hela tillståndet. Om du vill bevara tillståndet längre än containerns livslängd måste du montera en volym från en extern lagring. Som visas i den här artikeln kan Azure Container Instances montera en Azure-filresurs som skapats med [Azure Files](../storage/files/storage-files-introduction.md). Azure Files erbjuder fullständigt hanterade filresurser som finns i Azure Storage och som är tillgängliga via SMB-protokollet (Industry Standard Message Block). Med hjälp av en Azure-filresurs med Azure Container Instances kan fildelningsfunktioner som liknar att använda en Azure-filresurs med virtuella Azure-datorer.

> [!NOTE]
> Montering av en Azure Files-resurs är för närvarande begränsad till Linux-behållare. Hitta aktuella plattformsskillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).
>
> Att montera en Azure Files-resurs till en behållarinstans påminner om ett [Docker-bindningsfäste](https://docs.docker.com/storage/bind-mounts/). Tänk på att om du monterar en resurs i en behållarkatalog där filer eller kataloger finns, skyms dessa filer eller kataloger av fästet och är inte tillgängliga medan behållaren körs.
>

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Innan du kan använda en Azure-filresurs med Azure Container Instances måste du skapa den. Kör följande skript för att skapa ett lagringskonto som är värd för filresursen och själva resursen. Namnet på lagringskontot måste vara globalt unikt, så skriptet lägger till ett slumpmässigt värde i bassträngen.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Hämta autentiseringsuppgifter för lagringen

När du ska montera en Azure-filresurs som en volym i Azure Container Instances behöver du tre värden: namnet på lagringskontot, resursnamnet och åtkomstnyckeln för lagringen.

* **Namn på lagringskonto** - Om du använde det föregående `$ACI_PERS_STORAGE_ACCOUNT_NAME` skriptet lagrades lagringskontonamnet i variabeln. Om du vill visa kontonamnet skriver du:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Resursnamn** - Det här värdet `acishare` är redan känt (definierat som i föregående skript)

* **Lagringskontonyckel** - Det här värdet kan hittas med följande kommando:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Distribuera behållare och montera volym - CLI

Om du vill montera en Azure-filresurs som en volym i en behållare med hjälp av Azure CLI anger du resurs- och volymmonteringspunkten när du skapar behållaren med [az-behållarskap][az-container-create]. Om du har följt föregående steg kan du montera resursen som du skapade tidigare med hjälp av följande kommando för att skapa en behållare:

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

Värdet `--dns-name-label` måste vara unikt inom Azure-regionen där du skapar behållarinstansen. Uppdatera värdet i föregående kommando om du får ett **felmeddelande om DNS-namnetikett** när du kör kommandot.

## <a name="manage-files-in-mounted-volume"></a>Hantera filer i monterad volym

När behållaren startar kan du använda den enkla webbapp som distribueras via Microsoft [aci-hellofiles-avbildningen][aci-hellofiles] för att skapa små textfiler i Azure-filresursen på den monteringssökväg du angav. Hämta webbappens fullständigt kvalificerade domännamn (FQDN) med kommandot [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

När du har sparat text med hjälp av appen kan du använda [Azure-portalen][portal] eller ett verktyg som [Microsoft Azure Storage Explorer][storage-explorer] för att hämta och inspektera filen eller filerna som skrivits till filresursen.

## <a name="deploy-container-and-mount-volume---yaml"></a>Distribuera behållare och montera volym - YAML

Du kan också distribuera en behållargrupp och montera en volym i en behållare med Azure CLI och en [YAML-mall](container-instances-multi-container-yaml.md). Distribuera med YAML-mall är en föredragen metod när du distribuerar behållargrupper som består av flera behållare.

Följande YAML-mall definierar en behållargrupp `aci-hellofiles` med en behållare som skapats med avbildningen. Behållaren monterar Azure-filresursen *acishare* som skapats tidigare som en volym. Ange namn och lagringsnyckel för lagringskontot som är värd för filresursen i det angivna anges. 

Precis som i CLI-exemplet måste `dnsNameLabel` värdet vara unikt inom Azure-regionen där du skapar behållarinstansen. Uppdatera värdet i YAML-filen om det behövs.

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

Om du vill distribuera med YAML-mallen sparar `deploy-aci.yaml`du föregående YAML i en `--file` fil med namnet och kör sedan kommandot az container [create][az-container-create] med parametern:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Distribuera behållare och montera volym - Resource Manager

Förutom CLI- och YAML-distribution kan du distribuera en behållargrupp och montera en volym i en behållare med hjälp av en Azure [Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Fyll först i `volumes` matrisen i `properties` behållargruppen i mallen. 

Fyll sedan i matrisen i avsnittet i behållardefinitionen `volumeMounts` `properties` för varje behållare där du vill montera volymen.

Följande Resource Manager-mall definierar en behållargrupp `aci-hellofiles` med en behållare som skapats med avbildningen. Behållaren monterar Azure-filresursen *acishare* som skapats tidigare som en volym. Ange namn och lagringsnyckel för lagringskontot som är värd för filresursen i det angivna anges. 

Precis som i föregående `dnsNameLabel` exempel måste värdet vara unikt inom Azure-regionen där du skapar behållarinstansen. Uppdatera värdet i mallen om det behövs.

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

Om du vill distribuera med Resource Manager-mallen sparar `deploy-aci.json`du föregående JSON i en `--template-file` fil med namnet och kör sedan kommandot [az-gruppdistribution skapa][az-group-deployment-create] med parametern:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montera flera volymer

Om du vill montera flera volymer i en behållarinstans måste du distribuera med en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups), en YAML-fil eller en annan programmatisk metod. Om du vill använda en mall eller YAML-fil anger `volumes` du delningsinformationen och definierar volymerna genom att fylla i matrisen `properties` i avsnittet i filen. 

Om du till exempel har skapat två Azure-filresurser med namnet *share1* `volumes` och *share2* i lagringskontot *myStorageAccount,* verkar matrisen i en Resource Manager-mall ungefär så här:

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

Fyll sedan i matrisen `volumeMounts` i avsnittet i behållardefinitionen `properties` för varje behållare i den behållargrupp där du vill montera volymerna. Till exempel monterar detta de två volymerna, *myvolume1* och *myvolume2*, tidigare definierade:

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

* [Montera en emptyDir-volym i Azure Container-instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo-volym i Azure Container-instanser](container-instances-volume-gitrepo.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create