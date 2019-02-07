---
title: Skapa en Azure-skalningsuppsättning som använder lågprioriterade virtuella datorer (förhandsversion) | Microsoft Docs
description: Lär dig hur du skapar Azure VM-skalningsuppsättningar som använder lågprioriterade virtuella datorer för att minska kostnaderna
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 85b974e954fd99f6f10426f1961ea9f4a5343c59
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811111"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Lågprioriterade virtuella datorer på skalningsuppsättningar (förhandsversion)

Använda lågprioriterade virtuella datorer på skalningsuppsättningar kan du dra nytta av våra unutilized kapacitet på en betydande kostnadsbesparingar. Azure-infrastrukturen när som helst tidpunkt när Azure behöver kapacitet tillbaka kommer att ta bort lågprioriterade virtuella datorer. Därför är lågprioriterade virtuella datorer användbara för arbetsbelastningar som kan hantera avbrott som satsvis bearbetning jobb, miljöer för utveckling/testning, stora beräkningsbelastningar med mera.

Storleken på den tillgängliga unutilized kapacitet kan variera beroende på storlek, region, tid på dagen med mera. När du distribuerar lågprioriterade virtuella datorer på skala skalningsuppsättningar allokerar Azure de virtuella datorerna om att det finns tillgängligt, men det finns inget serviceavtal för dessa virtuella datorer. En skalningsuppsättning med låg prioritet har distribuerats i en enda feldomän och ger ingen hög tillgänglighet garanterar.

## <a name="eviction-policy"></a>Princip för borttagning

Du kan ange avlägsningsprincipen när du skapar lågprioriterade skalningsuppsättningar *frigöra* (standard) eller *ta bort*. 

Den *frigöra* principen flyttar dina avlägsnade virtuella datorer till tillståndet stoppad-frigjord så att du kan distribuera om avlägsnade instanser. Det finns dock ingen garanti för att lyckas allokeringen. De frigörs virtuella datorerna räknas av mot din kvot för scale set-instans och du debiteras för underliggande diskarna. 

Om du vill ha dina virtuella datorer i skalningsuppsättningen med låg prioritet som ska tas bort när de avlägsnas, du kan ange avlägsningsprincipen *ta bort*. Du kan skapa nya virtuella datorer med avlägsningsprincipen inställd på att ta bort, genom att öka skala set instance count-egenskapen. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar och därför du debiteras inte för lagringen. Du kan också använda funktionen för automatisk skalning i skalningsuppsättningar automatiskt försök och kompensera för avlägsnade virtuella datorer, men det finns ingen garanti att allokeringen ska lyckas. Det rekommenderas att du bara använda funktionen för automatisk skalning på skalningsuppsättningar med låg prioritet när du ställer in avlägsningsprincipen tas bort för att undvika kostnaden för dina diskar och träffa kvotgränser. 

> [!NOTE]
> I förhandsversionen kommer du att kunna ange princip för borttagning med den [Azure-portalen](#use-the-azure-portal) och [Azure Resource Manager-mallar](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Distribuera virtuella datorer med låg prioritet på skala anger

Om du vill distribuera lågprioriterade virtuella datorer på skalningsuppsättningar, och du kan ange den nya *prioritet* flaggan till *låg*. Alla virtuella datorer i din skalningsuppsättning ska anges till med låg prioritet. Använd någon av följande metoder för att skapa en skalningsuppsättning med virtuella datorer med låg prioritet:
- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-mallar](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Processen för att skapa en skalningsuppsättning som använder lågprioriterade virtuella datorer är samma som beskrivs i den [komma igång artikel](quick-create-portal.md). När du distribuerar en skalningsuppsättning, kan du ställa in flaggan med låg prioritet och avlägsningsprincipen: ![Skapa en skalningsuppsättning med virtuella datorer med låg prioritet](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Processen för att skapa en skalningsuppsättning med virtuella datorer med låg prioritet är samma som beskrivs i den [komma igång artikel](quick-create-cli.md). Lägg bara till den ”--prioritet” parametern till cli anropa och ge den värdet *låg* som visas i exemplet nedan:

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

Processen för att skapa en skalningsuppsättning med virtuella datorer med låg prioritet är samma som beskrivs i den [komma igång artikel](quick-create-powershell.md).
Lägg bara till den '-prioritet ”parametern för att den [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) och ge den värdet *låg* som visas i exemplet nedan:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Processen att skapa en skalningsuppsättning som använder virtuella datorer med låg prioritet är densamma som beskrivs i artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). Lägg till egenskapen ”prioritet” till den *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* resource Skriv i mallen och ange *låg* som värde. Se till att använda *2018-03-01* API-versionen eller senare. 

För att kunna ange princip för borttagning tas bort, lägga till parametern ”evictionPolicy” och ge den värdet *ta bort*.

I följande exempel skapas en Linux med låg prioritet skalningsuppsättning med namnet *myScaleSet* i *USA, västra centrala*, som kommer *ta bort* de virtuella datorerna i skalningsuppsättningen på borttagning:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
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
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Kan jag omvandla befintliga skalningsuppsättningar till lågprioriterade skalningsuppsättningar?
Nej, ställa in flaggan med låg prioritet stöds bara vid tidpunkten för skapandet.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Kan jag skapa en skalningsuppsättning med både vanliga virtuella datorer och lågprioriterade virtuella datorer?
Nej, inte en skalningsuppsättning stöd för fler än en typ av prioritet.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Hur hanteras kvot för lågprioriterade virtuella datorer?
Lågprioriterade virtuella datorer och vanliga virtuella datorer delar samma kvot pool. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Kan jag använda automatisk skalning med lågprioriterade skalningsuppsättningar?
Ja, du kan ange regler för automatisk skalning på din skalningsuppsättning med låg prioritet. Om dina virtuella datorer avlägsnas, kan automatisk skalning försöka att skapa nya virtuella datorer med låg prioritet. Kom ihåg att du inte garanteras den här kapaciteten dock. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Automatisk skalning fungerar med båda avlägsningsprinciper (frigöra och ta bort)?
Vi rekommenderar att du ställer in din avlägsningsprincipen för att ta bort när du använder automatisk skalning. Det beror på att frigjort instanser räknas mot din kapacitet antal på skalningsuppsättningen. När du använder automatisk skalning, överskrids sannolikt dina mål instansantalet snabbt på grund av frigjort, avlägsnade instanser. 

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en skalningsuppsättning med virtuella datorer med låg prioritet kan du prova med att distribuera vår [automatisk skalning mallen med hjälp av lågprioriterade](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Kolla in den [sidan med priser för virtual machine scale Sets](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) för prisinformation.
