---
title: "Anslutna datadiskar med skaluppsättningar för virtuella Azure-datorer | Microsoft Docs"
description: "Lär dig hur du använder anslutna datadiskar med skaluppsättningar för virtuella datorer"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 91d36d5321f455a2af31093fa460ddf6640942d4
ms.lasthandoff: 03/31/2017


---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Skalningsuppsättningar för virtuella Azure-datorer och anslutna datadiskar
[Skaluppsättningar för virtuella Azure-datorer](/azure/virtual-machine-scale-sets/) stöder nu virtuella datorer med anslutna datadiskar. Datadiskar kan definieras i lagringsprofilen för skalningsuppsättningar som skapas med Azure Managed Disks. Tidigare var operativsystemsenheter och temp-enheter de enda direktanslutna lagringsalternativen tillgängliga för virtuella datorer i skalningsuppsättningar.

> [!NOTE]
>  När du skapar en skalningsuppsättning med anslutna datadiskar behöver du fortfarande montera och formatera diskarna från en virtuell dator för att använda dem (precis som för fristående virtuella Azure-datorer). Ett enkelt sätt att göra detta är att använda ett tillägg för anpassat skript som anropar ett standardskript för att partitionera och formatera alla datadiskar på en virtuell dator.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Skapa en skalningsuppsättning med anslutna datadiskar
Ett enkelt sätt att skapa en skalningsuppsättning med anslutna diskar är att använda [Azure CLI](https://github.com/Azure/azure-cli)-kommandot _vmss create_. I följande exempel skapas en Azure-resursgrupp och en skalningsuppsättning för virtuella datorer med 10 virtuella Ubuntu-datorer, där var och en har två anslutna datadiskar på 50 GB respektive 100 GB.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Observera att kommandot _vmss create_ ställer in vissa konfigurationsvärden som standard om du inte själv anger dem. Om du vill se de tillgängliga alternativ som du kan åsidosätta kan du försöka följande:
```bash
az vmss create --help
```
Ett annat sätt att skapa en skalningsuppsättning med anslutna datadiskar är att ange en skalningsuppsättning i en Azure Resource Manager-mall som innehåller ett _dataDisks_-avsnitt i _storageProfile_ och att sedan distribuera mallen. Exemplet med diskar på 50 GB och 100 GB ovan skulle definieras så här i mallen:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
Du kan se ett fullständigt exempel på en skalningsuppsättning med en ansluten disk som är klar att distribueras här: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Lägg till en datadisk till en befintlig skalningsuppsättning
Du kan lägga till en datadisk till en skalningsuppsättning för virtuella datorer med Azure CLI-kommandot _az vmss disk attach_. Se till att du anger en lun som inte redan används. Följande CLI-exempel lägger till en 50 GB-enhet till lun 3:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```
> [!NOTE]
> Olika VM-storlekar har olika begränsningar för antalet anslutna enheter de har stöd för. Kontrollera [storleksegenskaper för virtuella datorer](../virtual-machines/windows/sizes.md) innan du lägger till en ny disk.

Du kan även lägga till en disk genom att lägga till en ny post för egenskapen _dataDisks_ i _storageProfile_ för en skalningsuppsättning och tillämpa ändringen. För att testa detta kan du ta en befintlig skalningsuppsättning i [resursutforskaren i Azure](https://resources.azure.com/). Välj _Redigera_ och lägg till en ny disk i listan över datadiskar. T.ex. med exemplet ovan:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```
Välj sedan _PUT_ för att tillämpa ändringarna på skalningsuppsättningen. Det här exemplet skulle fungera så länge du använder en VM-storlek som har stöd för fler än två anslutna datadiskar.

> [!NOTE]
> När du gör en ändring på en skalningsuppsättning, som att till exempel lägga till eller ta bort en datadisk, tillämpas det på alla nyskapade virtuella datorer. Men ändringen tillämpas endast på befintliga virtuella datorer om egenskapen _upgradePolicy_ är inställd på "Automatiskt". Om den är inställd på "Manuellt" måste du manuellt tillämpa den nya modellen på befintliga virtuella datorer. Du kan göra detta i portalen med PowerShell-kommandot _Update-AzureRmVmssInstance_ eller med CLI-kommandot _az vmss update-instances_.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Ta bort en datadisk från en skalningsuppsättning
Du kan ta bort en datadisk från en skalningsuppsättning för virtuella datorer med Azure CLI-kommandot _az vmss disk detach_. Följande kommando tar till exempel bort disken som definierats på lun 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
På ett liknande sätt kan du även ta bort en disk från en skalningsuppsättning genom att ta bort en post från egenskapen _dataDisks_ i _storageProfile_ och tillämpa ändringen. 

## <a name="additional-notes"></a>Ytterligare information
Stöd för Azure Managed Disks och anslutna datadiskar för skalningsuppsättningar lades till i [_förhandversionen 2016-04-30-_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) av Microsoft.Compute-API:t. Du kan använda vilken SDK eller vilket kommandoradsverktyg som helst som skapats med den här versionen eller senare av API:et.

I den första implementeringen av stöd för anslutna diskar för skalningsuppsättningar går det inte att ansluta eller koppla från datadiskar till/från enskilda virtuella datorer i en skalningsuppsättning.

Azure Portal-stöd för anslutna datadiskar i skalningsuppsättningar är begränsat i början. Beroende på dina behov kan du använda Azure-mallar, CLI, PowerShell, SDK:er och REST API för att hantera anslutna diskar.



