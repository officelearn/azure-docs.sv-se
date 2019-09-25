---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224572"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Skapa en stegvis ögonblicks bild (för hands version) för hanterade diskar

Stegvisa ögonblicks bilder (för hands version) är tidpunkter för säkerhets kopiering av hanterade diskar som, när de tas, endast består av alla ändringar sedan den senaste ögonblicks bilden. När du försöker hämta eller på annat sätt använda en stegvis ögonblicks bild, används hela den virtuella hård disken. Den här nya funktionen för ögonblicks bilder av hanterade diskar kan eventuellt göra att de är mer kostnads effektiva eftersom du inte längre behöver lagra hela disken med varje enskild ögonblicks bild, såvida du inte väljer att. Precis som vanliga ögonblicks bilder kan du använda stegvisa ögonblicks bilder för att skapa en fullständig hanterad disk eller för att göra en vanlig ögonblicks bild.

Det finns några skillnader mellan en stegvis ögonblicks bild och en vanlig ögonblicks bild. Stegvisa ögonblicks bilder använder alltid standard lagring av hård diskar, oavsett diskens lagrings typ, medan vanliga ögonblicks bilder kan använda Premium-SSD. Om du använder vanliga ögonblicks bilder på Premium Storage för att skala upp VM-distributioner, rekommenderar vi att du använder anpassade avbildningar på standard lagring i det [delade avbildnings galleriet](../articles/virtual-machines/linux/shared-image-galleries.md). Det hjälper dig att få en mer enorm skala med lägre kostnad. Dessutom kan stegvisa ögonblicks bilder erbjuda bättre tillförlitlighet med [zon redundant lagring](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Om ZRS är tillgängligt i den valda regionen kommer en stegvis ögonblicks bild att använda ZRS automatiskt. Om ZRS inte är tillgängligt i regionen kommer ögonblicks bilden att vara [Lokalt Redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) (LRS) som standard. Du kan åsidosätta det här beteendet och välja ett manuellt, men vi rekommenderar inte detta.

Stegvisa ögonblicks bilder erbjuder också en differentiell funktion som är unikt tillgänglig för hanterade diskar. De gör att du kan hämta ändringarna mellan två stegvisa ögonblicks bilder av samma hanterade diskar, ned till block nivån. Du kan använda den här funktionen för att minska dina data när du kopierar ögonblicks bilder över flera regioner.

Om du ännu inte har registrerat dig för för hands versionen och du vill börja använda stegvisa ögonblicks bilder kan du skicka ett AzureDisks@microsoft.com e-postmeddelande till oss till för att få åtkomst till den offentliga för hands versionen.

## <a name="restrictions"></a>Begränsningar

- Det går för närvarande inte att skapa stegvisa ögonblicks bilder när du har ändrat storleken på en disk.
- Det går för närvarande inte att flytta stegvisa ögonblicks bilder mellan prenumerationer.
- För närvarande kan du bara skapa SAS-URI: er på upp till fem ögonblicks bilder av en viss ögonblicks bild serie vid en given tidpunkt.
- Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration.
- Upp till sju stegvisa ögonblicks bilder per disk kan skapas var femte minut.
- Totalt 200 stegvisa ögonblicks bilder kan skapas för en enskild disk.

## <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell för att skapa en stegvis ögonblicks bild. Du kan installera den senaste versionen av PowerShell lokalt. Du behöver den senaste versionen av Azure PowerShell, följande kommando kommer antingen att installera den eller uppdatera den befintliga installationen till senaste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

När den är installerad loggar du in på PowerShell-sessionen `az login`med.

Ersätt `<yourDiskNameHere>`, `<yourResourceGroupNameHere>` och`<yourDesiredSnapShotNameHere>` med dina värden, kan du använda följande skript för att skapa en stegvis ögonblicks bild:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-mall

Du kan också använda Azure Resource Manager mallar för att skapa en stegvis ögonblicks bild. Du måste kontrol lera att API version är inställt på **2019-03-01** och att den stegvisa egenskapen också har angetts till true. Följande fragment är ett exempel på hur du skapar en stegvis ögonblicks bild med Resource Manager-mallar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="cli"></a>CLI

Du kan skapa en stegvis ögonblicks bild med Azure CLI med [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Ett exempel kommando skulle se ut så här:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Du kan också identifiera vilka ögonblicks bilder som är stegvisa ögonblicks bilder i CLI med med `--query` hjälp av parametern på [AZ Snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Du kan använda den parametern för att direkt fråga egenskaperna **SourceResourceId** och **SourceUniqueId** för ögonblicks bilder. SourceResourceId är Azure Resource Manager resurs-ID för den överordnade disken. **SourceUniqueId** är värdet som ärvs från diskens egenskap **UniqueID** . Om du tar bort en disk och sedan skapar en disk med samma namn kommer värdet för egenskapen **UniqueID** att ändras.

Exempel på båda frågorna ser ut så här:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Nästa steg

Om du ännu inte har registrerat dig för för hands versionen och du vill börja använda stegvisa ögonblicks bilder kan du skicka ett AzureDisks@microsoft.com e-postmeddelande till oss till för att få åtkomst till den offentliga för hands versionen.
