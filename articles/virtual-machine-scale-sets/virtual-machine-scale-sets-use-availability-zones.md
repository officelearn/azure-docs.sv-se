---
title: Skapa en Azure-skalningsuppsättning som använder tillgänglighetszoner
description: Lär dig hur du skapar Azure-skalningsuppsättningar för virtuella datorer som använder tillgänglighetszoner för ökad redundans mot avbrott
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: jushiman
ms.openlocfilehash: c8795f46e47b2ab43898f6f436b9ee6026a22fa7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011573"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Skapa en skalningsuppsättning för virtuella datorer som använder tillgänglighetszoner

Om du vill skydda skalningsuppsättningarna för virtuella datorer från fel på datacenternivå kan du skapa en skalningsuppsättning över tillgänglighetszoner. Azure-regioner som stöder tillgänglighetszoner har minst tre separata zoner, var och en med sin egen oberoende kraftkälla, nätverk och kylning. Mer information finns i [Översikt över tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Överväganden för tillgänglighet

När du distribuerar en skalningsuppsättning i en eller flera zoner från och med API-version *2017-12-01*har du möjlighet att distribuera med "max spridning" eller "statisk 5-feldomänspridning". Med maximal spridning sprider skalningsuppsättningen dina virtuella datorer över så många feldomäner som möjligt inom varje zon. Den här spridningen kan vara över större eller färre än fem feldomäner per zon. Med "statisk 5 feldomänspridning" sprider skalningsuppsättningen dina virtuella datorer över exakt fem feldomäner per zon. Om skalningsuppsättningen inte kan hitta fem olika feldomäner per zon för att uppfylla allokeringsbegäran misslyckas begäran.

**Vi rekommenderar distribution med max spridning för de flesta arbetsbelastningar,** eftersom den här metoden ger den bästa spridningen i de flesta fall. Om du behöver repliker som ska spridas över olika maskinvaruisoleringsenheter rekommenderar vi att du sprider sig över tillgänglighetszoner och använder maxspridning inom varje zon.

Med maximal spridning ser du bara en feldomän i den skalningsuppsättning VM-instansvyn och i instansmetadata oavsett hur många feldomäner de virtuella datorerna är spridda över. Spridningen inom varje zon är implicit.

Om du vill använda max spridning ställer du in *platformFaultDomainCount* till *1*. Om du vill använda statisk fem feldomänspridning ställer du in *platformFaultDomainCount* till *5*. I API-version *2017-12-01*är *platformFaultDomainCount* standard *1* för skaluppsättningar med en zon och över zoner. För närvarande stöds endast statisk fem feldomänspridning för regionala (icke-zonal) skalningsuppsättningar.

### <a name="placement-groups"></a>Placeringsgrupper

När du distribuerar en skalningsuppsättning har du också möjlighet att distribuera med en enda [placeringsgrupp](./virtual-machine-scale-sets-placement-groups.md) per tillgänglighetszon eller med flera per zon. För regionala (icke-zonal) skalningsuppsättningar är valet att ha en enda placeringsgrupp i regionen eller att ha flera i regionen. För de flesta arbetsbelastningar rekommenderar vi flera placeringsgrupper, vilket möjliggör större skala. I API-version *2017-12-01*anger skalningsuppsättningar standard till flera placeringsgrupper för skalningsuppsättningar med en zon och flera zoner, men de är standard för enskilda placeringsgrupper för regionala (icke-zonal) skaluppsättningar.

> [!NOTE]
> Om du använder maxspridning måste du använda flera placeringsgrupper.

### <a name="zone-balancing"></a>Zonbalansering

Slutligen, för skalningsuppsättningar som distribueras över flera zoner, har du också möjlighet att välja "bästa ansträngning zonbalans" eller "strikt zonbalans". En skalningsuppsättning anses vara "balanserad" om varje zon\\har samma antal virtuella datorer eller + - 1 virtuell dator i alla andra zoner för skalningsuppsättningen. Ett exempel:

- En skalningsuppsättning med 2 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 anses vara balanserad. Det finns bara en zon med ett annat antal virtuella datorer och det är bara 1 mindre än de andra zonerna. 
- En skalningsuppsättning med 1 virtuell dator i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 anses vara obalanserad. Zon 1 har 2 färre virtuella datorer än zon 2 och 3.

Det är möjligt att virtuella datorer i skalningsuppsättningen har skapats, men tillägg på dessa virtuella datorer kan inte distribueras. Dessa virtuella datorer med tilläggsfel räknas fortfarande när du avgör om en skalningsuppsättning är balanserad. Till exempel anses en skalningsuppsättning med 3 virtuella datorer i zon 1, 3 virtuella datorer i zon 2 och 3 virtuella datorer i zon 3 vara balanserad även om alla tillägg misslyckades i zon 1 och alla tillägg lyckades i zon 2 och 3.

Med zonbalans med bäst insats försöker skalningsuppsättningen skala in och ut samtidigt som balansen bibehålls. Men om detta av någon anledning inte är möjligt (till exempel om en zon går nedåt, kan skalningsuppsättningen inte skapa en ny virtuell dator i den zonen), tillåter skalningsuppsättningen tillfällig obalans för att framgångsrikt skala in eller ut. Vid efterföljande skalningsförsök lägger skalningsuppsättningen till virtuella datorer i zoner som behöver fler virtuella datorer för att skalan ska vara balanserad. På samma sätt tar skalningsuppsättningen bort virtuella datorer från zoner som behöver färre virtuella datorer för att skalan ska vara balanserad. Med "strikt zonbalans" misslyckas skalningsuppsättningen alla försök att skala in eller ut om detta skulle orsaka obalans.

Om du vill använda zonbalansen med bäst insats anger du *zoneBalance* till *false*. Den här inställningen är standard i API version *2017-12-01*. Om du vill använda strikt zonsaldo anger du *zoneBalance* till *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Uppsättningar med enzons- och zon redundanta skalning

När du distribuerar en skaluppsättning för virtuella datorer kan du välja att använda en enda tillgänglighetszon i en region eller flera zoner.

När du skapar en skalningsuppsättning i en enda zon styr du vilken zon alla dessa VM-instanser körs i och skalningsuppsättningen hanteras och automatisk skalning endast inom den zonen. Med en zonundant skaluppsättning kan du skapa en enda skalningsuppsättning som sträcker sig över flera zoner. När VM-instanser skapas är de som standard jämnt balanserade mellan zoner. Om ett avbrott inträffar i en av zonerna skalas inte en skalningsuppsättning automatiskt ut för att öka kapaciteten. En bra metod skulle vara att konfigurera regler för automatisk skalning baserat på CPU- eller minnesanvändning. Reglerna för automatisk skalning gör att skalningsuppsättningen kan svara på en förlust av VM-instanser i den zonen genom att skala ut nya instanser i de återstående driftzonerna.

Om du vill använda tillgänglighetszoner måste skalningsuppsättningen skapas i en [Azure-region som stöds](../availability-zones/az-overview.md#services-support-by-region). Du kan skapa en skalningsuppsättning som använder tillgänglighetszoner med någon av följande metoder:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Processen för att skapa en skalningsuppsättning som använder en tillgänglighetszon är densamma som beskrivs i [artikeln komma igång](quick-create-portal.md). När du väljer en Azure-region som stöds kan du skapa en skalningsuppsättning i en eller flera tillgängliga zoner, vilket visas i följande exempel:

![Skapa en skalningsuppsättning i en enda tillgänglighetszon](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Skalningsuppsättningen och stödresurserna, till exempel Azure-belastningsutjämnaren och den offentliga IP-adressen, skapas i den enda zon som du anger.

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Processen för att skapa en skalningsuppsättning som använder en tillgänglighetszon är densamma som beskrivs i [artikeln komma igång](quick-create-cli.md). Om du vill använda tillgänglighetszoner måste du skapa skalningsuppsättningen i en Azure-region som stöds.

Lägg `--zones` till parametern i kommandot [az vmss create](/cli/azure/vmss) och ange vilken zon som ska användas (till exempel zon *1,* *2*eller *3*). I följande exempel skapas en skalningsuppsättning med en zon med namnet *myScaleSet* i zon *1:*

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

Ett fullständigt exempel på en skalningsuppsättning med en zon och nätverksresurser finns i [det här exemplet CLI-skriptet](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zon redundant skalningsuppsättning

Om du vill skapa en zonundant skaluppsättning använder du en standard-SKU-offentlig IP-adress och belastningsutjämnare. *Standard* För förbättrad redundans skapar *Standard* SKU zonupptrorade nätverksresurser. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md) och standard [belastningsutjämning och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Om du vill skapa en zonundant `--zones` skaluppsättning anger du flera zoner med parametern. I följande exempel skapas en zonundant skaluppsättning med namnet *myScaleSet* i zonerna *1,2,3:*

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

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer i zonen/zonerna som du anger. Ett fullständigt exempel på en zonundant skalningsuppsättning och nätverksresurser finns i [det här exemplet CLI-skriptet](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill använda tillgänglighetszoner måste du skapa skalningsuppsättningen i en Azure-region som stöds. Lägg `-Zone` till parametern i kommandot [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) och ange vilken zon som ska användas (till exempel zon *1,* *2*eller *3*).

I följande exempel skapas en skalningsuppsättning med en zon med namnet *myScaleSet* i *östra USA 2* zon *1*. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och lastbalanserare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzVmss `
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

### <a name="zone-redundant-scale-set"></a>Zon redundant skalningsuppsättning

Om du vill skapa en zonundant `-Zone` skaluppsättning anger du flera zoner med parametern. I följande exempel skapas en zonundant skaluppsättning med namnet *myScaleSet* i *östra USA 2-zonerna* *1, 2, 3*. Zonuppsagda Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och belastningsutjämnare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```powershell
New-AzVmss `
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

Processen för att skapa en skalningsuppsättning som använder en tillgänglighetszon är densamma som beskrivs i artikeln komma igång för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). Om du vill använda tillgänglighetszoner måste du skapa skalningsuppsättningen i en Azure-region som stöds. Lägg `zones` till egenskapen i resurstypen *Microsoft.Compute/virtualMachineScaleSets* i mallen och ange vilken zon som ska användas (till exempel zon *1,* *2*eller *3*).

I följande exempel skapas en linux-skala med en zon med namnet *myScaleSet* i *östra USA 2* zon *1:*

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

Ett fullständigt exempel på en skalningsuppsättning med en zon och nätverksresurser finns i [den här exempelmallen Resurshanteraren](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zon redundant skalningsuppsättning

Om du vill skapa en zonundant `zones` skaluppsättning anger du flera värden i egenskapen för resurstypen *Microsoft.Compute/virtualMachineScaleSets.* I följande exempel skapas en zonundant skalningsuppsättning med namnet *myScaleSet* i *östra USA 2-zoner* *1,2,3:*

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

Om du skapar en offentlig IP-adress eller en belastningsutjämnare anger du egenskapen *"sku": { "name": "Standard"}"* för att skapa zonuppsagda nätverksresurser. Du måste också skapa en nätverkssäkerhetsgrupp och regler för att tillåta all trafik. Mer information finns i [Azure Load Balancer Standard översikt](../load-balancer/load-balancer-standard-overview.md) och standard [belastningsutjämning och tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

Ett fullständigt exempel på en zonundant skaluppsättning och nätverksresurser finns i [det här exemplet med Resource Manager-mallen](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en skalningsuppsättning i en tillgänglighetszon kan du lära dig hur du [distribuerar program på skalningsuppsättningar för virtuella datorer](tutorial-install-apps-cli.md) eller [Använd automatisk skalning med skalningsuppsättningar för virtuella datorer](tutorial-autoscale-cli.md).
