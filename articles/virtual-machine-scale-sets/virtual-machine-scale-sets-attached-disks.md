---
title: Anslutna datadiskar med skaluppsättningar för virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du använder anslutna datadiskar med skaluppsättningar för virtuella datorer
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: 551d90661f845aa98a41ed7de0b75c657c234f52
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741410"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Anslutna datadiskar med skaluppsättningar för virtuella Azure-datorer
För att utöka din tillgängliga lagring, stöder Azure [VM-skalningsuppsättningar](/azure/virtual-machine-scale-sets/) virtuella datorinstanser med anslutna datadiskar. Du kan koppla in datadiskar när skalningsuppsättningen skapas eller till en befintlig skalningsuppsättning.

> [!NOTE]
>  När du skapar en skalningsuppsättning med anslutna datadiskar så behöver du montera och formatera diskarna från en virtuell dator för att använda dem (precis som för fristående virtuella Azure-datorer). Ett enkelt sätt att slutföra denna process är att använda ett anpassat skripttillägg som anropar ett skript för att partitionera och formatera alla datadiskar på en virtuell dator. Exempel på detta finns i [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Skapa och hantera diskar i en skalningsuppsättning
Detaljerad information om hur du skapar en skalningsuppsättning med anslutna datadiskar, förbereder och formaterar, eller lägger till och tar bort datadiskar, finns i någon av följande självstudier:

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

Resten av den här artikeln beskriver specifika användningsfall som Service Fabric-kluster som kräver datadiskar eller att koppla in befintliga datadiskar med innehåll till en skalningsuppsättning.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Skapa ett Service Fabric-kluster med anslutna datadiskar
Varje [nodtyp](../service-fabric/service-fabric-cluster-nodetypes.md) i ett [Service Fabric](/azure/service-fabric)-kluster som körs i Azure backas upp av en VM-skalningsuppsättning.  Genom att använda en Azure Resource Manager-mall, kan du ansluta datadiskar till skalningsuppsättningen som utgör Service Fabric-klustret. Du kan använda en [befintlig mall](https://github.com/Azure-Samples/service-fabric-cluster-templates) som utgångspunkt. I mallen tar du med ett _dataDisks_-avsnitt i _storageProfile_ för _Microsoft.Compute/virtualMachineScaleSets_-resurserna. Distribuera sedan mallen. I följande exempel ansluts en datadisk på 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Du kan automatiskt partitionera, formatera och montera datadiskarna när klustret distribueras.  Lägg till ett anpassat skripttillägg till _extensionProfile_ för _virtualMachineProfile_ i skaluppsättningarna.

Förbered datadiskarna i ett Windows-kluster automatiskt genom att lägga till följande:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Förbered datadiskarna i ett Linux-kluster automatiskt genom att lägga till följande:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Lägga till redan fyllda datadiskar i en befintlig skalningsuppsättning
Datadiskar som definieras i skalningsuppsättningsmodellen är alltid tomma. Du kan dock ansluta en befintlig datadisk till en specifik virtuell dator i en skalningsuppsättning. Den här funktionen finns för närvarande som en förhandsvisningsversion, med exempel på [github](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Om du vill distribuera data på alla virtuella datorer i skalningsuppsättningen kan du duplicera datadisken och ansluta den till var och en av de virtuella datorerna i skalningsuppsättningen. Du kan sedan skapa en anpassad avbildning som innehåller alla data och etablera skalningsuppsättningen från den anpassade avbildningen, eller så kan du använda Azure Files eller en liknande datalagringsprodukt.


## <a name="additional-notes"></a>Ytterligare information
Stöd för Azure Managed Disks och anslutna datadiskar för skalningsuppsättningar finns i [_förhandversionen 2016-04-30-_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) av Microsoft.Compute-API:t eller senare versioner.

Azure Portal-stöd för anslutna datadiskar i skalningsuppsättningar är begränsat i början. Beroende på dina behov kan du använda Azure-mallar, CLI, PowerShell, SDK:er och REST API för att hantera anslutna diskar.


