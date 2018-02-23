---
title: "Skapa en Azure skala som använder tillgänglighet zoner (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du skapar skalningsuppsättningar i virtuella Azure-datorn som använder tillgänglighet zoner för ökad redundans mot avbrott"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 2de214f604469025a8a4accde44359fea0ded7e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Skapa en skaluppsättning för virtuell dator som använder tillgänglighet zoner (förhandsgranskning)
Du kan skapa en skala in över tillgänglighet zoner för att skydda din skalningsuppsättningar i virtuella datorer från fel datacenter-nivå. Azure-regioner som har stöd för tillgänglighet zoner har minst tre separata zoner med sina egna oberoende power käll-, nätverks- och kylning. Mer information finns i [översikt av tillgänglighet zoner](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Skaluppsättningar för en zon och zonredundant
När du distribuerar en skaluppsättning för virtuell dator kan välja du att använda en zon i en region tillgänglighet eller flera zoner.

När du skapar en skala som anges i en zon, styr vilken zon dessa VM-instanser körs i och skaluppsättning hanteras och autoscales endast inom zonen. Zonredundant skaluppsättning kan du skapa en enda skaluppsättning som sträcker sig över flera zoner. När VM-instanser som skapas som standard balanseras de jämnt mellan zoner. Bör avbrott uppstår i någon av zonerna kan en skaluppsättning inte automatiskt kan skaländras ut till öka kapaciteten. Ett bra tips är att konfigurera automatiska regler baserat på användning av CPU eller minne. Autoskala reglerna att skaluppsättningen att svara på en förlust av VM-instanser i en zonen genom att skala ut nya instanser i de återstående operativa zonerna.

Om du vill använda tillgänglighet zoner din skaluppsättning måste skapas i en [stöd för Azure-region](../availability-zones/az-overview.md#regions-that-support-availability-zones). Du måste också [registrera för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). Du kan skapa en skalningsuppsättning som använder tillgänglighet zoner med någon av följande metoder:

- [Azure-portalen](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-portal.md). Se till att du har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). När du väljer en Azure-regionen stöds kan skapa du en skala som angetts i någon av de tillgängliga zonerna som visas i följande exempel:

![Skapa en skala som anges i en enda tillgänglighet zon](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Skaluppsättning och ge support för resurser, till exempel Azure belastningsutjämnare och offentliga IP-adressen skapas i samma zon som du anger.


## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-cli.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll).

Lägg till den `--zones` parametern till den [az vmss skapa](/cli/azure/vmss#az_vmss_create) kommando och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). I följande exempel skapas en enda zon skaluppsättningen namngivna *myScaleSet* i zonen *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
För en komplett exempel på en enda zon skala och nätverksresurser finns i avsnittet [exempelskriptet CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zonredundant skaluppsättning
Så här skapar du en zonredundant skala kan du använda en *Standard* SKU offentliga IP-adress och läsa in belastningsutjämning. För förbättrad redundans i *Standard* SKU skapar zonredundant nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md). Första gången som du skapar en zonredundant skala ange eller belastningsutjämnare, måste du utföra följande steg för att registrera ditt konto för dessa förhandsgranskningsfunktioner.

1. Registrera ditt konto för zonredundant skaluppsättning och nätverk funktioner med [az funktionen registrera](/cli/azure/feature#az_feature_register) på följande sätt:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Det kan ta några minuter att registrera för funktioner. Du kan kontrollera status för åtgärden med [az funktionen Visa](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    I följande exempel visas önskad status för funktionen som *registrerad*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. När zonredundant skalan angetts och nätverk resurser rapporterar som *registrerade*, registrera den *Compute* och *nätverk* providers med [az registrera providern](/cli/azure/provider#az_provider_register) på följande sätt:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Om du vill skapa en zonredundant skaluppsättning ange flera zoner med de `--zones` parameter. I följande exempel skapas en zonredundant skala uppsättning med namnet *myScaleSet* över zoner *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Det tar några minuter att skapa och konfigurera alla skaluppsättning resurser och virtuella datorer i zonerna som du anger. En komplett exempel på en zonredundant skala och nätverksresurser finns i avsnittet [exempelskriptet CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-powershell.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). Lägg till den `-Zone` parametern till den [ny AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) kommando och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). 

I följande exempel skapas en enskild zon scale set config med namnet *vmssConfig* i *östra USA 2* zon *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

För en komplett exempel på en enda zon skala och nätverksresurser finns i avsnittet [PowerShell-exempelskriptet](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Zonredundant skaluppsättning
Så här skapar du en zonredundant skala kan du använda en *Standard* SKU offentliga IP-adress och läsa in belastningsutjämning. För förbättrad redundans i *Standard* SKU skapar zonredundant nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md). Första gången som du skapar en zonredundant skala ange eller belastningsutjämnare, måste du utföra följande steg för att registrera ditt konto för dessa förhandsgranskningsfunktioner.

1. Registrera ditt konto för zonredundant skaluppsättning och nätverk funktioner med [registrera AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) på följande sätt:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Det kan ta några minuter att registrera för funktioner. Du kan kontrollera status för åtgärden med [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    I följande exempel visas önskad status för funktionen som *registrerad*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. När zonredundant skalan angetts och nätverk resurser rapporterar som *registrerade*, registrera den *Compute* och *nätverk* providers med [ Registrera AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) på följande sätt:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Om du vill skapa en zonredundant skaluppsättning ange flera zoner med de `-Zone` parameter. I följande exempel skapas en zonredundant scale set config med namnet *myScaleSet* över *östra USA 2* zoner *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Om du skapar en offentlig IP-adress med [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) eller en belastningsutjämnare med [ny AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), ange den *- SKU ”Standard”* skapa zonredundant nätverksresurser. Du måste skapa en säkerhetsgrupp för nätverk och regler för att tillåta all trafik. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md).

En komplett exempel på en zonredundant skala och nätverksresurser finns i avsnittet [PowerShell-exempelskriptet](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som detaljerad i komma igång-artikel för [Linux](virtual-machine-scale-sets-create-template-linux.md) eller [Windows](virtual-machine-scale-sets-create-template-windows.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). Lägg till den `zones` egenskapen till den *Microsoft.Compute/virtualMachineScaleSets* resurs Skriv i mallen och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*).

I följande exempel skapas en Linux enskild zon skaluppsättningen namngivna *myScaleSet* i *östra USA 2* zon *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

För en komplett exempel på en enda zon skala och nätverksresurser finns i avsnittet [exempel Resource Manager-mallen](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zonredundant skaluppsättning
Om du vill skapa en zonredundant skaluppsättning ange flera värden i den `zones` -egenskapen för den *Microsoft.Compute/virtualMachineScaleSets* resurstypen. I följande exempel skapas en zonredundant skala uppsättning med namnet *myScaleSet* över *östra USA 2* zoner *1,2,3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Om du skapar en offentlig IP-adress eller en belastningsutjämnare, ange den *”sku”: {”name”: ”Standard”} ”* egenskap för att skapa zonredundant nätverksresurser. Du måste skapa en säkerhetsgrupp för nätverk och regler för att tillåta all trafik. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md).

En komplett exempel på en zonredundant skala och nätverksresurser finns i avsnittet [exempel Resource Manager-mallen](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skala som angetts i en zon för tillgänglighet kan du lära dig hur du [distribuera program på den virtuella datorn skala anger](virtual-machine-scale-sets-deploy-app.md) eller [använda Autoskala med skalningsuppsättningar i virtuella](virtual-machine-scale-sets-autoscale-overview.md).
