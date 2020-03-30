---
title: Distribuera virtuella Linux-datorer till dedikerade värdar med CLI
description: Distribuera virtuella datorer till dedikerade värdar med Hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127689"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Distribuera virtuella datorer till dedikerade värdar med Azure CLI
 

Den här artikeln guidar dig genom hur du skapar en [Azure-dedikerad värd](dedicated-hosts.md) som värd för dina virtuella datorer. 

Kontrollera att du har installerat Azure CLI version 2.0.70 eller senare `az login`och loggat in på ett Azure-konto med . 


## <a name="limitations"></a>Begränsningar

- Skalningsuppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- Storleken och maskinvarutyperna som är tillgängliga för dedikerade värdar varierar mellan olika regioner. Läs [värdprissidan](https://aka.ms/ADHPricing) om du vill veta mer.
 

## <a name="create-resource-group"></a>Skapa resursgrupp 
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa resursgruppen med az-gruppskapa. I följande exempel skapas en resursgrupp med namnet *myDHResourceGroup* på platsen för *östra USA.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Skapa en värdgrupp 

En **värdgrupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värdgrupp i en region och en tillgänglighetszon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträcker sig över flera tillgänglighetszoner. I det här fallet måste du ha en värdgrupp i var och en av de zoner som du vill använda.
- Span över flera feldomäner som mappas till fysiska rack. 
 
I båda fallen måste du ange antalet feldomäner för värdgruppen. Om du inte vill spänna över feldomäner i gruppen använder du ett antal feldomäner på 1. 

Du kan också välja att använda både tillgänglighetszoner och feldomäner. 

I det här exemplet använder vi [az vm-värdgrupp skapa](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värdgrupp med både tillgänglighetszoner och feldomäner. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Övriga exempel

Du kan också använda [az vm-värdgrupp skapa](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värdgrupp i tillgänglighet zon 1 (och inga feldomäner).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Följande använder [az vm-värdgrupp skapa](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värdgrupp med hjälp av feldomäner (som ska användas i regioner där tillgänglighetszoner inte stöds). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Skapa en värd 

Nu ska vi skapa en dedikerad värd i värdgruppen. Förutom ett namn för värden måste du ange SKU för värden. Host SKU fångar den VM-serien som stöds samt maskinvarugenereringen för din dedikerade värd.  

Mer information om värdsskrona och priser finns i [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

Använd [az vm-värdskapet](/cli/azure/vm/host#az-vm-host-create) för att skapa en värd. Om du anger ett antal feldomäner för värdgruppen blir du ombedd att ange feldomänen för värden.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator 
Skapa en virtuell dator i en dedikerad värd med [az vm create](/cli/azure/vm#az-vm-create). Om du angav en tillgänglighetszon när du skapar värdgruppen måste du använda samma zon när du skapar den virtuella datorn.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Om du skapar en virtuell dator på en värd som inte har tillräckligt med resurser skapas den virtuella datorn i ett misslyckat tillstånd. 


## <a name="check-the-status-of-the-host"></a>Kontrollera värdens status

Du kan kontrollera värdens hälsostatus och hur många virtuella datorer du fortfarande kan distribuera till värden med [az vm-värd get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Utdata kommer att se ut ungefär så här:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportera som mall 
Du kan exportera en mall om du nu vill skapa en ytterligare utvecklingsmiljö med samma parametrar eller en produktionsmiljö som matchar den. Resource Manager använder JSON-mallar som definierar alla parametrar för din miljö. Du skapar hela miljöer genom att referera till den här JSON-mallen. Du kan skapa JSON-mallar manuellt eller exportera en befintlig miljö för att skapa JSON-mallen åt dig. Använd [az-gruppexport](/cli/azure/group#az-group-export) för att exportera resursgruppen.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Med det här `myDHResourceGroup.json` kommandot skapas filen i den aktuella arbetskatalogen. När du skapar en miljö från den här mallen uppmanas du att ange alla resursnamn. Du kan fylla i dessa namn i `--include-parameter-default-value` mallfilen `az group export` genom att lägga till parametern i kommandot. Redigera JSON-mallen för att ange resursnamnen eller skapa en parameters.json-fil som anger resursnamnen.
 
Om du vill skapa en miljö från mallen använder du [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Rensa 

Du debiteras för dina dedikerade värdar även när inga virtuella datorer distribueras. Du bör ta bort alla värdar som du för närvarande inte använder för att spara kostnader.  

Du kan bara ta bort en värd när det inte längre finns virtuella datorer som använder den. Ta bort de virtuella datorerna med [az vm-borttagning](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

När du har tagit bort de virtuella datorerna kan du ta bort värden med [az vm-värdborttagning](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
När du har tagit bort alla dina värdar kan du ta bort värdgruppen med [az vm-värdgruppborttagning](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Du kan också ta bort hela resursgruppen i ett enda kommando. Detta tar bort alla resurser som skapats i gruppen, inklusive alla virtuella datorer, värdar och värdgrupper.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

- Mer information finns i översikten [Dedikerade värdar.](dedicated-hosts.md)

- Du kan också skapa dedikerade värdar med [Azure-portalen](dedicated-hosts-portal.md).

- Det finns exempelmall, som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och feldomäner för maximal återhämtning i en region.