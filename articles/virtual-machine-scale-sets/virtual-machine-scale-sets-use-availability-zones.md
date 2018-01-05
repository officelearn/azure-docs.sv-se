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
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Skapa en skaluppsättning för virtuell dator som använder tillgänglighet zoner (förhandsgranskning)
Du kan skapa en skala som angetts i en zon för tillgänglighet för att skydda din skalningsuppsättningar i virtuella datorer från fel datacenter-nivå. Azure-regioner som har stöd för tillgänglighet zoner har minst tre separata zoner med sina egna oberoende power käll-, nätverks- och kylning. Mer information finns i [översikt av tillgänglighet zoner](../availability-zones/az-overview.md).

Om du vill använda tillgänglighet zoner din skaluppsättning måste skapas i en [stöd för Azure-region](../availability-zones/az-overview.md#regions-that-support-availability-zones). Du kan skapa en skalningsuppsättning som använder tillgänglighet zoner med någon av följande metoder:

- [Azure-portalen](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-portal.md). När du väljer en Azure-regionen stöds kan skapa du en skala som angetts i någon av de tillgängliga zonerna som visas i följande exempel:

![Skapa en skala som anges i en enda tillgänglighet zon](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Skaluppsättning och ge support för resurser, till exempel Azure belastningsutjämnare och offentliga IP-adressen skapas i samma zon som du anger.


## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-cli.md). Om du vill använda tillgänglighet zoner, måste du skapa nivå i en Azure-regionen stöds. Lägg till den `--zones` parametern till den [az vmss skapa](/cli/azure/vmss#az_vmss_create) kommando och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). I följande exempel skapas en uppsättning med namnet skala *myScaleSet* i zonen *1*:

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

Det tar några minuter att skapa och konfigurera alla skaluppsättning resurser och virtuella datorer i zonen som du anger.


## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-powershell.md). Om du vill använda tillgänglighet zoner, måste du skapa nivå i en Azure-regionen stöds. Lägg till den `-Zone` parametern till den [ny AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) kommando och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). I följande exempel skapas en scale set config med namnet *vmssConfig* i *östra USA 2* zon *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Den faktiska skaluppsättning så skapar, de ytterligare enligt anvisningarna i den [komma igång artikel](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Processen för att skapa en skalningsuppsättning som använder en tillgänglighet zon är samma som detaljerad i komma igång-artikel för [Linux](virtual-machine-scale-sets-create-template-linux.md) eller [Windows](virtual-machine-scale-sets-create-template-windows.md). Om du vill använda tillgänglighet zoner, måste du skapa nivå i en Azure-regionen stöds. Lägg till den `zones` egenskapen till den *Microsoft.Compute/virtualMachineScaleSets* resurs Skriv i mallen och ange vilken zon som ska användas (till exempel zonen *1*, *2*, eller *3*). I följande exempel skapas en Linux-skala uppsättning med namnet *myScaleSet* i *östra USA 2* zon *1*:

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

Den faktiska skaluppsättning så skapar, de ytterligare enligt anvisningarna i i komma igång-artikel [Linux](virtual-machine-scale-sets-create-template-linux.md) eller [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skala som angetts i en zon för tillgänglighet kan du lära dig hur du [distribuera program på den virtuella datorn skala anger](virtual-machine-scale-sets-deploy-app.md) eller [använda Autoskala med skalningsuppsättningar i virtuella](virtual-machine-scale-sets-autoscale-overview.md).
