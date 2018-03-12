---
title: Skapa en zoned Linux VM med Azure CLI | Microsoft Docs
description: "Skapa en Linux-VM i en zon för tillgänglighet med Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: e31eb02fda7ade027225c428c5b15804ebc6f182
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Skapa en virtuell Linux-dator i en zon för tillgänglighet med Azure CLI

Den här artikeln steg genom att använda Azure CLI för att skapa en Linux-VM i en zon för Azure tillgänglighet. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Kontrollera att du har installerat senast [Azure CLI 2.0](/cli/azure/install-az-cli2) och inloggade på en Azure-konto med [az inloggningen](/cli/azure/reference-index#az_login).


## <a name="check-vm-sku-availability"></a>Kontrollera tillgänglighet för SKU för virtuell dator
Tillgängligheten för VM-storlek eller SKU: er kan variera beroende på region och zon. När du planerar för användningen av tillgänglighetszoner kan du visa tillgängliga VM SKU:er via Azure-region och zon. Den möjligheten säkerställer att du väljer en lämplig VM-storlek och hämtar önskad elasticitet i flera zoner. Mer information om olika VM-typer och -storlekar finns i [Översikt över VM-storlekar](sizes.md).

Du kan visa de tillgängliga VM SKU: er med den [az vm lista-SKU](/cli/azure/vm#az_vm_list_skus) kommando. Följande exempel visar tillgängliga VM SKU:er i regionen *usaöstra2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Utdata liknar följande komprimerade exempel som visar tillgänglighetszoner som varje VM-storlek är tillgänglig i:

```azurecli
ResourceType      Locations  Name               Tier       Size     Zones
----------------  ---------  -----------------  ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2    Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2    Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s       Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s       Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3    Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3    Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3     Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3     Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create).  

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I det här exemplet en resursgrupp med namnet *myResourceGroupVM* skapas i den *eastus2* region. USA, östra 2 är en av de Azure-regioner som stöder tillgänglighetszoner i förhandsversioner.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

Resursgruppen har angetts när du skapar eller ändrar en VM som kan ses i den här artikeln.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az_vm_create). 

När du skapar en virtuell dator finns flera tillgängliga alternativ, som t.ex. avbildning av operativsystemet, bestämning av diskstorlek och administrativa autentiseringsuppgifter. I det här exemplet skapas en virtuell dator med namnet *myVM* som kör Ubuntu Server. Den virtuella datorn skapas i tillgänglighet zon *1*. Som standard skapas den virtuella datorn i den *Standard_DS1_v2* storlek. Den här storleken stöds i förhandsgranskningen tillgänglighet zoner.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

Det kan ta några minuter att skapa den virtuella datorn. När den virtuella datorn skapats visar Azure CLI information om den virtuella datorn. Anteckna den `zones` -värde som anger den tillgänglighet zon där den virtuella datorn körs. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zon för IP-adress och hanterade diskar

När den virtuella datorn distribueras i en zon för tillgänglighet, distribueras IP-adress och hanterade diskresurser i samma zon tillgänglighet. I följande exempel hämta information om dessa resurser.

Först använda den [az vm lista-ip-adresser](/cli/azure/vm#az_vm_list_ip_addresses) kommando för att returnera namn på offentlig IP-adressresurs i *myVM*. I det här exemplet har namnet lagras i en variabel som används i ett senare steg.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nu kan du få information om IP-adressen:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Utdata visar att IP-adressen är i samma tillgänglighet zon som den virtuella datorn:

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

Kontrollera dessutom att hanterade diskar för den virtuella datorn är i zonen tillgänglighet. Använd kommandot [az vm show](/cli/azure/vm#az_vm_show) för att hämta diskens ID. I det här exemplet lagras disk-id i en variabel som används i ett senare steg. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Nu kan du få information om hanterade disken:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Utdata visar att de hanterade diskarna är i samma tillgänglighetszon som den virtuella datorn:

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Läs mer om [regioner och tillgänglighet](regions-and-availability.md) för virtuella Azure-datorer.




