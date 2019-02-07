---
title: Skapa en Azure-skalningsuppsättning som använder Tillgänglighetszoner | Microsoft Docs
description: Lär dig hur du skapar Azure VM-skalningsuppsättningar som använder Tillgänglighetszoner för ökad redundans mot avbrott
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 32679e37062fbf77ce6ab03b6ea708c0d5eeea30
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816245"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Skapa en skalningsuppsättning för virtuella datorer som använder Tillgänglighetszoner

För att skydda dina skalningsuppsättningar för virtuella datorer från datacenternivå fel kan skapa du en skalningsuppsättning i olika Tillgänglighetszoner. Azure-regioner som stöd för Tillgänglighetszoner har minst tre separata zoner, var och en med sin egen oberoende strömkälla, nätverk och kylning. Mer information finns i [översikt över Tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

När du distribuerar en skalningsuppsättning i en eller flera zoner från och med API-version *2017-12-01*, har du möjlighet att distribuera med ”maximal spridning” eller ”statiska 5 fel domän sprida”. Med maximal spridning sprids skalningsuppsättningen dina virtuella datorer över så många feldomäner som möjligt i varje zon. Den här sprida kan vara för större eller mindre än fem feldomäner per zon. Sprider ut dina virtuella datorer mellan fem feldomäner per zon med ”statiska 5 fel domän spridning” skalningsuppsättningen. Om skalningsuppsättningen inte hittar fem olika feldomäner per zon att uppfylla begäran om minnesallokering, misslyckas denna begäran.

**Vi rekommenderar att du distribuerar med maximal spridning för de flesta arbetsbelastningar**, eftersom den här metoden ger de bästa spridning i de flesta fall. Om du behöver repliker sprids över olika maskinvaruenheter för isolering kan vi rekommenderar att sprids över Tillgänglighetszoner och använda maximal spridning i varje zon.

En feldomän i Instansvy för virtuell dator i skaluppsättning och metadata, oavsett hur många feldomäner som de virtuella datorerna är fördelade på instansen visas bara med maximal spridning. Sprida inom varje zon är implicit.

Om du vill använda maximal spridning *platformFaultDomainCount* till *1*. Om du vill använda statiska fem fel domän sprida *platformFaultDomainCount* till *5*. I API-versionen *2017-12-01*, *platformFaultDomainCount* som standard *1* zon och mellan zoner scale anger. För närvarande stöds endast statiska fem fel domän sprida för regionala scale sets.

### <a name="placement-groups"></a>Placeringsgrupper

När du distribuerar en skalningsuppsättning kan du också möjlighet att distribuera med en enda [placeringsgrupp](./virtual-machine-scale-sets-placement-groups.md) per Tillgänglighetszon eller med flera per zon. Valet är att ha en enda placeringsgrupp i regionen eller ha flera i regionen för regionala scale sets. För de flesta arbetsbelastningar rekommenderar vi flera placeringsgrupper, vilket möjliggör större skala. I API-versionen *2017-12-01*, skalningsuppsättningar standard till flera placeringsgrupper för zon och mellan zoner scale sets, men används standardvärdet enda placeringsgrupp för regionala scale sets.

> [!NOTE]
> Om du använder maximal spridning, måste du använda flera placeringsgrupper.

### <a name="zone-balancing"></a>Zon för belastningsutjämning

Slutligen för skalningsuppsättningar distribueras över flera zoner, har du också du möjlighet att välja ”zonbalans bästa prestanda” eller ”strikt zonbalans”. En skalningsuppsättning betraktas som ”belastningsutjämnade” om var och en zon samma antal virtuella datorer eller +\\-1 virtuell dator i andra zoner för skalningsuppsättningen. Exempel:

- En skalningsuppsättning med 2 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 anses vara belastningsutjämnad. Det finns endast en zon med ett annat antal virtuella datorer och det är endast 1 som är mindre än de andra zonerna. 
- En skalningsuppsättning med 1 virtuell dator i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 anses obalanserade. Zon 1 har 2 färre virtuella datorer än zon 2 och 3.

Det är möjligt att virtuella datorer i skalningsuppsättningen har skapats, men inte det gick att distribuera tillägg på de virtuella datorerna. Dessa virtuella datorer med datortillägg räknas fortfarande när du bestämmer om en skalningsuppsättning fördelas. Exempelvis kan en skalningsuppsättning med 3 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 anses belastningsutjämnade även om alla tillägg misslyckades i zon 1 och alla tillägg har uppdaterats i zoner 2 och 3.

Mån zonbalans skalningsuppsättningen försök att skala in och ut samtidigt saldo. Men om du av någon anledning detta inte är möjligt (till exempel om en zon slutar fungera kan skalningsuppsättningen kan inte skapa en ny virtuell dator i zonen), skalningsuppsättningen kan tillfälligt obalans har skala in eller ut. Lägger till virtuella datorer i zoner som behöver fler virtuella datorer för skalningsuppsättningen att balansera på flera skalbar försök skalningsuppsättningen. På samma sätt på efterföljande skala inloggningsförsök, skalningsuppsättningen tar bort virtuella datorer i zoner som behöver färre virtuella datorer för skalningsuppsättningen att balansera. Med ”strikt zonbalans” misslyckas alla försök att skala in eller ut om så skulle orsaka obalanserad i skalningsuppsättningen.

Om du vill använda mån zonbalans *zoneBalance* till *FALSKT*. Det här är standardinställningen i API-versionen *2017-12-01*. Om du vill använda strikt zonbalans *zoneBalance* till *SANT*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Zon och zonredundant skalningsuppsättningar

När du distribuerar en skalningsuppsättning för virtuell dator kan välja du att använda en enda Tillgänglighetszon i en region eller flera zoner.

När du skapar en skalningsuppsättning i en enskild zon, du bestämmer vilken zon dessa VM-instanser som körs i och skalningsuppsättningen är hanterad och skalar endast inom den zonen. En zonredundant skalningsuppsättning kan du skapa en enkel skalningsuppsättning som sträcker sig över flera zoner. När VM-instanser skapas som standard balanseras de jämnt mellan zoner. Om ett avbrott skulle inträffa i någon av zonerna en skalningsuppsättning inte automatiskt skala ut för att öka kapaciteten. Ett bra tips är att konfigurera regler för automatisk skalning baserat på användning av CPU eller minne. Regler för automatisk skalning att skalningsuppsättningen att svara på en förlust av VM-instanser i den en zon genom att skala ut nya instanser i återstående operativa zoner.

Om du vill använda Tillgänglighetszoner, måste din skalningsuppsättning skapas i en [stöds Azure-region](../availability-zones/az-overview.md#regions-that-support-availability-zones). Du kan skapa en skalningsuppsättning som använder Tillgänglighetszoner med någon av följande metoder:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Processen för att skapa en skalningsuppsättning som använder en Tillgänglighetszon är samma som beskrivs i den [komma igång artikel](quick-create-portal.md). När du väljer en Azure-region kan skapa du en skalningsuppsättning i en eller flera tillgängliga zoner som visas i följande exempel:

![Skapa en skalningsuppsättning i en enda Tillgänglighetszon](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Skalningsuppsättningen och kompletterande resurser, till exempel Azure load balancer och offentliga IP-adressen skapas i samma zon som du anger.

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Processen för att skapa en skalningsuppsättning som använder en Tillgänglighetszon är samma som beskrivs i den [komma igång artikel](quick-create-cli.md). Om du vill använda Tillgänglighetszoner, måste du skapa din skalningsuppsättning i en Azure-region.

Lägg till den `--zones` parameter ska den [az vmss skapa](/cli/azure/vmss) kommandot och ange vilken zon som ska användas (till exempel zon *1*, *2*, eller *3*). I följande exempel skapas en zon skalningsuppsättning med namnet *myScaleSet* i zonen *1*:

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

För en komplett exempel på en zon skala och nätverksresurser, se [CLI-Skriptexemplet](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zonredundant skalningsuppsättning

Att skapa en zonredundant skalningsuppsättning kan du använda en *Standard* SKU offentlig IP-adress och load balancer. För förbättrad redundans i *Standard* SKU skapar zonredundant nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md) och [Standard Load Balancer och Tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Skapa en zonredundant skalningsuppsättning genom att ange flera zoner med den `--zones` parametern. I följande exempel skapas en zonredundant skalningsuppsättning med namnet *myScaleSet* i flera zoner *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningen resurser och virtuella datorer i zonerna som du anger. Exempel på en zonredundant skalningsuppsättning ange och nätverksresurser, se [CLI-Skriptexemplet](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill använda Tillgänglighetszoner, måste du skapa din skalningsuppsättning i en Azure-region. Lägg till den `-Zone` parameter ska den [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) kommandot och ange vilken zon som ska användas (till exempel zon *1*, *2*, eller *3*).

I följande exempel skapas en zon skalningsuppsättning med namnet *myScaleSet* i *östra USA 2* zon *1*. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och lastbalanserare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zonredundant skalningsuppsättning

Skapa en zonredundant skalningsuppsättning genom att ange flera zoner med den `-Zone` parametern. I följande exempel skapas en zonredundant skalningsuppsättning med namnet *myScaleSet* över *östra USA 2* zoner *1, 2, 3*. Zonredundant Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och belastningsutjämnare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Processen att skapa en skalningsuppsättning som använder en Tillgänglighetszon är densamma som beskrivs i artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). Om du vill använda Tillgänglighetszoner, måste du skapa din skalningsuppsättning i en Azure-region. Lägg till den `zones` egenskap till den *Microsoft.Compute/virtualMachineScaleSets* resource Skriv i mallen och ange vilken zon som ska användas (till exempel zon *1*, *2*, eller *3*).

I följande exempel skapas en Linux zon skalningsuppsättning med namnet *myScaleSet* i *östra USA 2* zon *1*:

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

För en komplett exempel på en zon skala och nätverksresurser, se [det här exemplet Resource Manager-mall](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zonredundant skalningsuppsättning

Skapa en zonredundant skalningsuppsättning genom att ange flera värden i den `zones` -egenskapen för den *Microsoft.Compute/virtualMachineScaleSets* resurstyp. I följande exempel skapas en zonredundant skalningsuppsättning med namnet *myScaleSet* över *östra USA 2* zoner *1,2,3*:

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

Om du skapar en offentlig IP-adress eller en belastningsutjämnare, ange den *”sku”: {”name”: ”Standard”} ”* egenskapen att skapa zonredundant nätverksresurser. Du måste också skapa en Nätverkssäkerhetsgrupp och regler för att tillåta all trafik. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md) och [Standard Load Balancer och Tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Exempel på en zonredundant skalningsuppsättning ange och nätverksresurser, se [det här exemplet Resource Manager-mall](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en skalningsuppsättning i en Tillgänglighetszon kan du lära dig hur du [distribuera program på VM-skalningsuppsättningar](tutorial-install-apps-cli.md) eller [Använd automatisk skalning med VM-skalningsuppsättningar](tutorial-autoscale-cli.md).
