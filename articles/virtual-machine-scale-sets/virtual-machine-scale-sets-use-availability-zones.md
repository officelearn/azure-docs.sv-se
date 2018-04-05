---
title: Skapa en Azure skala som använder tillgänglighet zoner | Microsoft Docs
description: Lär dig hur du skapar skalningsuppsättningar i virtuella Azure-datorn som använder tillgänglighet zoner för ökad redundans mot avbrott
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Skapa en skaluppsättning för virtuell dator som använder tillgänglighet zoner
Du kan skapa en skala in över tillgänglighet zoner för att skydda din skalningsuppsättningar i virtuella datorer från fel datacenter-nivå. Azure-regioner som har stöd för tillgänglighet zoner har minst tre separata zoner med sina egna oberoende power käll-, nätverks- och kylning. Mer information finns i [översikt av tillgänglighet zoner](../availability-zones/az-overview.md).


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Skaluppsättningar för en zon och zonredundant
När du distribuerar en skaluppsättning för virtuell dator kan välja du att använda en zon i en region tillgänglighet eller flera zoner.

När du skapar en skala som anges i en zon, styr vilken zon dessa VM-instanser körs i och skaluppsättning hanteras och autoscales endast inom zonen. Zonredundant skaluppsättning kan du skapa en enda skaluppsättning som sträcker sig över flera zoner. När VM-instanser som skapas som standard balanseras de jämnt mellan zoner. Bör avbrott uppstår i någon av zonerna kan en skaluppsättning inte automatiskt kan skaländras ut till öka kapaciteten. Ett bra tips är att konfigurera automatiska regler baserat på användning av CPU eller minne. Autoskala reglerna att skaluppsättningen att svara på en förlust av VM-instanser i en zonen genom att skala ut nya instanser i de återstående operativa zonerna.

Om du vill använda tillgänglighet zoner din skaluppsättning måste skapas i en [stöd för Azure-region](../availability-zones/az-overview.md#regions-that-support-availability-zones). Du kan skapa en skalningsuppsättning som använder tillgänglighet zoner med någon av följande metoder:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>Överväganden för tillgänglighet
Från och med API version 2017-12-01, när du distribuerar en skala som angetts i en eller flera zoner har möjlighet att distribuera med ”max sprida” eller ”statiska 5 fel domän sprida”. Med max sprida sprids uppsättningen skala din virtuella dator över som många fault domäner som möjligt inom varje zon. Den här sprida kan vara över större eller mindre än fem fault domäner per zon. Å andra sidan med ”statiska 5 fel domän spridning” skaluppsättning sprider ut dina virtuella datorer över exakt 5 feldomäner per zon. Om skaluppsättning inte kan hitta 5 distinkta feldomäner per zon att uppfylla begäran om minnesallokering, misslyckas denna begäran.

**Vi rekommenderar att du distribuerar med max sprida för de flesta arbetsbelastningar** eftersom max sprida ger den bästa spridning i de flesta fall. Om du behöver repliker sprids över distinkta maskinvara isolering enheter, rekommenderar vi sprida över tillgänglighet zoner och använder max sprida inom varje zon. Observera att med max sprida endast visas en feldomän i scale set VM-instansvyn och instans metadata oavsett hur många feldomäner som de virtuella datorerna är faktiskt spridda över; sprida inom varje zon är implicit.

Ange ”platformFaultDomainCount” 1 om du vill använda max spridning. Om du vill använda statiska 5 fel domän sprider värdet ”platformFaultDomainCount” 5. I API version 2017-12-01 ”platformFaultDomainCount” som standard 1 för skalningsuppsättningar i en zon och mellan zon. För närvarande stöds endast statiska 5 fel domän sprida för regional skaluppsättningar.

Dessutom, när du distribuerar en skaluppsättning har möjlighet att distribuera med en enda [placering grupp](./virtual-machine-scale-sets-placement-groups.md) per zon för tillgänglighet eller med flera per zon (för regional skaluppsättningar valet är att ha en enda placering grupp i den region eller har flera i region). För de flesta arbetsbelastningar bör du använda flera placering grupper, vilket gör att för större skala. Skala anger standard till flera grupper för placering för en zon och mellan zon skalningsuppsättningar i API version 2017-12-01, men för enskild placering grupp för regional skaluppsättningar standard.

>[!NOTE]
> Om du använder max sprida, måste du använda flera grupper för placering.

Slutligen för skalningsuppsättningar distribution över flera zoner, ha du också möjlighet att välja ”bästa prestanda zonen balance” eller ”strikt zonen balance”. En skalningsuppsättning anses ”belastningsutjämnade” om antalet virtuella datorer i varje zon är inom ett antal virtuella datorer i alla zoner för skalan. För instans, en skala som anges med 2 virtuella datorer i zonen 1, 3 virtuella datorer i zonen 2 och 3 virtuella datorer i zonen 3 anses belastningsutjämnade. Men en skala som med 1 VM i zonen 1, 3 virtuella datorer i zonen 2 och 3 virtuella datorer i zon 3 anses obalanserade. Det är möjligt att virtuella datorer i skaluppsättning har skapats, medan misslyckas tillägg på dessa virtuella datorer. Dessa virtuella datorer med tillägget fel räknas fortfarande när du bestämmer om en skalningsuppsättning balanseras. Till exempel en skala som anges med 3 virtuella datorer i zonen 1, 3 virtuella datorer i zonen 2 och 3 virtuella datorer i zonen 3 anses belastningsutjämnade även om alla tillägg misslyckades i zonen 1 och alla tillägg lyckades zoner 2 och 3. Bästa prestanda zonen balansen skaluppsättning försöker skala in och ut samtidigt saldo. Men om du av någon anledning detta inte är möjligt (till exempel en zon kraschar, så skaluppsättning går inte att skapa en ny virtuell dator i zonen) sedan skaluppsättning tillåter tillfälliga obalanserad för att kunna skala in eller ut. Lägger till virtuella datorer i zoner som behöver flera virtuella datorer för skaluppsättningen att balansera på efterföljande skala ut försök skaluppsättning. På samma sätt på efterföljande skala inloggningsförsök skaluppsättning tar bort virtuella datorer i zoner som behöver färre virtuella datorer för skalan ska balanseras har ställt in. Med ”strikt zonen balance” misslyckas skaluppsättning å andra sidan, om du försöker skala in eller ut om det skulle orsaka obalanserad.

Om du vill använda bästa prestanda zonen balansen värdet ”zoneBalance” FALSKT (standard i version 2017-12-01-API). Ange ”zoneBalance” för att använda strikt zonen saldo till true.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](quick-create-portal.md). Se till att du har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). När du väljer en Azure-regionen stöds kan skapa du en skala som angetts i någon av de tillgängliga zonerna som visas i följande exempel:

![Skapa en skala som anges i en enda tillgänglighet zon](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Skaluppsättning och ge support för resurser, till exempel Azure belastningsutjämnare och offentliga IP-adressen skapas i samma zon som du anger.


## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](quick-create-cli.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll).

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
Så här skapar du en zonredundant skala kan du använda en *Standard* SKU offentliga IP-adress och läsa in belastningsutjämning. För förbättrad redundans i *Standard* SKU skapar zonredundant nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md). 

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
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](quick-create-powershell.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). Lägg till den `-Zone` parametern till den [ny AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) kommando och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). 

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
Så här skapar du en zonredundant skala kan du använda en *Standard* SKU offentliga IP-adress och läsa in belastningsutjämning. För förbättrad redundans i *Standard* SKU skapar zonredundant nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md).

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
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som detaljerad i komma igång-artikel för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). För att använda zoner för tillgänglighet, måste du skapa din skalningsuppsättning i en Azure-regionen stöds och har [registrerad för tillgänglighet zoner Förhandsgranska](http://aka.ms/azenroll). Lägg till den `zones` egenskapen till den *Microsoft.Compute/virtualMachineScaleSets* resurs Skriv i mallen och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*).

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

För en komplett exempel på en enda zon skala och nätverksresurser finns i avsnittet [exempel Resource Manager-mallen](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)

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

En komplett exempel på en zonredundant skala och nätverksresurser finns i avsnittet [exempel Resource Manager-mallen](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)


## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skala som angetts i en zon för tillgänglighet kan du lära dig hur du [distribuera program på den virtuella datorn skala anger](virtual-machine-scale-sets-deploy-app.md) eller [använda Autoskala med skalningsuppsättningar i virtuella](virtual-machine-scale-sets-autoscale-overview.md).
