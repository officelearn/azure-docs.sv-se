---
title: "Skapa en Azure skala med låg prioritet för virtuella datorer (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du skapar skalningsuppsättningar i virtuella Azure-datorn med låg prioritet virtuella datorer för att spara på kostnader"
services: virtual-machine-scale-sets
documentationcenter: 
author: mmccrory
manager: rajraj
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: 9e4970ecc538caab537281931b89bfd57d994cfa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Låg prioritet virtuella datorer på skaluppsättningar (förhandsgranskning)

Med låg prioritet virtuella datorer på skaluppsättningar kan du dra nytta av vår unutilized kapacitet på en betydande kostnadsbesparingar. Azure-infrastrukturen vid någon tidpunkt när Azure behöver kapacitet tillbaka, kommer att ta bort VM med låg prioritet. Därför är låg prioritet bra för arbetsbelastningar som kan hantera avbrott som batchbearbetning jobb, utveckling och testning miljöer, stora beräkning av arbetsbelastningar med mera.

Mängden tillgänglig unutilized kapacitet kan variera beroende på storleken, region, tid på dagen och mer. När du distribuerar låg prioritet virtuella datorer på skala anger allokerar Azure de virtuella datorerna om kapacitet är tillgänglig, men det finns inga SLA för dessa virtuella datorer. En låg prioritet skaluppsättning distribueras i en enda feldomän och erbjuder garanterar att inga hög tillgänglighet.

> [!NOTE]
> Skaluppsättningar för låg prioritet finns i förhandsgranskningen och redo för dina scenarier för utveckling och testning. 

## <a name="eviction-policy"></a>Princip för borttagning

När din låg prioritet för skaluppsättning för virtuella datorer har avlägsnats, de kommer att flyttas till tillståndet Stoppad (frigjord) som standard. Du kan distribuera avlägsnade instanser med den här principen för borttagning, men det finns ingen garanti för att allokera lyckas. Stoppade virtuella datorer räknas av mot din scale set instans kvot och du kommer att debiteras för underliggande diskarna. 

Om du vill att dina virtuella datorer i din låg prioritet skaluppsättningen som ska tas bort när de avlägsnas, du kan ange princip för borttagning ska tas bort i ditt [Azure Resource Manager-mall](#use-azure-resource-manager-templates). Du kan skapa nya virtuella datorer genom att öka egenskapen scale set instans antal med principen borttagning har angetts till ta bort. Avlägsnade VMs tas bort tillsammans med deras underliggande diskar och därför du debiteras inte för lagring. Du kan också använda funktionen automatisk skalning av skaluppsättningar för att automatiskt försök och kompensera för avlägsnade virtuella datorer, men det finns ingen garanti för att allokera lyckas. Det rekommenderas att du endast använda funktionen Autoskala på skaluppsättningar för låg prioritet när du ställer in principen borttagning för att ta bort om du vill undvika kostnader för diskar och träffa kvotgränser. 

> [!NOTE]
> Under förhandsgranskning, kommer du att kunna ställa in av principen med hjälp av [Azure Resource Manager-mallar](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Distribuera virtuella datorer låg prioritet på skala anger

Om du vill distribuera låg prioritet virtuella datorer på skalningsuppsättningar, och du kan ange den nya *prioritet* flaggan till *låg*. Alla virtuella datorer i din skaluppsättning anges till låg prioritet. Använd någon av följande metoder för att skapa en skala med låg prioritet virtuella datorer:
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0

Processen för att skapa en skala med låg prioritet virtuella datorer är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-cli.md). Lägg bara till de '--prioritet ' parametern cli anropa och Ställ in den på *låg* som visas i exemplet nedan:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Processen för att skapa en skala med låg prioritet virtuella datorer är samma som i den [komma igång artikel](virtual-machine-scale-sets-create-powershell.md).
Lägg bara till de '-prioritet ' parametern till den [ny AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) och ange det till *låg* som visas i exemplet nedan:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Använd Azure Resource Manager-mallar

Processen för att skapa en skalningsuppsättning med låg prioritet virtuella datorer är samma som detaljerad i komma igång-artikel för [Linux](virtual-machine-scale-sets-create-template-linux.md) eller [Windows](virtual-machine-scale-sets-create-template-windows.md). Lägga till egenskapen 'priority' till den *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* resurs Skriv i mallen och ange *låg* som värde. Se till att använda *2017-10-30-preview* API-version eller högre. 

Lägg till parametern 'evictionPolicy' för att ställa in principen för borttagning tas bort, och ange det till *ta bort*.

I följande exempel skapas en Linux låg prioritet skaluppsättningen namngivna *myScaleSet* i *Väst centrala oss*, som kommer *ta bort* de virtuella datorerna i skaluppsättningen på borttagning:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skala med låg prioritet virtuella datorer kan du prova med att distribuera vår [automatiskt skala mallen med låg prioritet](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Kolla in den [virtuella datorns skaluppsättning sida med priser](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) för prisinformation.