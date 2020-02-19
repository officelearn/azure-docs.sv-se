---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3361f4723c5a9776cb156417e57d609175d11621
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445985"
---
Stegvisa ögonblicks bilder (för hands version) är tidpunkter för säkerhets kopiering av hanterade diskar som, när de tas, endast består av alla ändringar sedan den senaste ögonblicks bilden. När du försöker hämta eller på annat sätt använda en stegvis ögonblicks bild, används hela den virtuella hård disken. Den här nya funktionen för ögonblicks bilder av hanterade diskar kan eventuellt göra att de är mer kostnads effektiva eftersom du inte längre behöver lagra hela disken med varje enskild ögonblicks bild, såvida du inte väljer att. Precis som vanliga ögonblicks bilder kan du använda stegvisa ögonblicks bilder för att skapa en fullständig hanterad disk eller för att göra en vanlig ögonblicks bild.

Det finns några skillnader mellan en stegvis ögonblicks bild och en vanlig ögonblicks bild. Stegvisa ögonblicks bilder använder alltid standard lagring av hård diskar, oavsett diskens lagrings typ, medan vanliga ögonblicks bilder kan använda Premium-SSD. Om du använder vanliga ögonblicks bilder på Premium Storage för att skala upp VM-distributioner rekommenderar vi att du använder anpassade avbildningar på standard lagring i det [delade avbildnings galleriet](../articles/virtual-machines/linux/shared-image-galleries.md). Det hjälper dig att få en mer enorm skala med lägre kostnad. Dessutom kan stegvisa ögonblicks bilder erbjuda bättre tillförlitlighet med [zon redundant lagring](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Om ZRS är tillgängligt i den valda regionen kommer en stegvis ögonblicks bild att använda ZRS automatiskt. Om ZRS inte är tillgängligt i regionen kommer ögonblicks bilden att vara [Lokalt Redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) (LRS) som standard. Du kan åsidosätta det här beteendet och välja ett manuellt, men vi rekommenderar inte detta.

Stegvisa ögonblicks bilder erbjuder också en differentiell funktion som är unikt tillgänglig för hanterade diskar. De gör att du kan hämta ändringarna mellan två stegvisa ögonblicks bilder av samma hanterade diskar, ned till block nivån. Du kan använda den här funktionen för att minska dina data när du kopierar ögonblicks bilder över flera regioner.

## <a name="restrictions"></a>Begränsningar

- Stegvisa ögonblicks bilder är för närvarande endast tillgängliga i östra USA, östra USA 2, centrala USA, västra centrala USA, Östra Kanada, centrala Kanada, Nord Europa, Asien, sydöstra.
- Det går för närvarande inte att skapa stegvisa ögonblicks bilder när du har ändrat storleken på en disk.
- Det går för närvarande inte att flytta stegvisa ögonblicks bilder mellan prenumerationer.
- För närvarande kan du bara skapa SAS-URI: er på upp till fem ögonblicks bilder av en viss ögonblicks bild serie vid en given tidpunkt.
- Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration.
- Upp till sju stegvisa ögonblicks bilder per disk kan skapas var femte minut.
- Totalt 200 stegvisa ögonblicks bilder kan skapas för en enskild disk.

## <a name="powershell"></a>PowerShell

Du kan använda Azure PowerShell för att skapa en stegvis ögonblicks bild. Du behöver den senaste versionen av Azure PowerShell, följande kommando kommer antingen att installera den eller uppdatera den befintliga installationen till senaste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

När den är installerad loggar du in på PowerShell-sessionen med `az login`.

Om du vill skapa en stegvis ögonblicks bild med Azure PowerShell anger du konfigurationen med [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) med parametern `-Incremental` och skickar den som en variabel till [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) via `-Snapshot`-parametern.

Ersätt `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`och `<yourDesiredSnapShotNameHere>` med dina värden. sedan kan du använda följande skript för att skapa en stegvis ögonblicks bild:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Du kan identifiera stegvisa ögonblicks bilder från samma disk med `SourceResourceId` och `SourceUniqueId` egenskaper för ögonblicks bilder. `SourceResourceId` är Azure Resource Manager resurs-ID för den överordnade disken. `SourceUniqueId` är värdet som ärvts från diskens egenskap `UniqueId`. Om du skulle ta bort en disk och sedan skapa en ny disk med samma namn ändras värdet för egenskapen `UniqueId`.

Du kan använda `SourceResourceId` och `SourceUniqueId` för att skapa en lista över alla ögonblicks bilder som är associerade med en viss disk. Ersätt `<yourResourceGroupNameHere>` med ditt värde och Använd sedan följande exempel för att visa en lista över dina befintliga stegvisa ögonblicks bilder:

```PowerShell
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

## <a name="cli"></a>CLI

Du kan skapa en stegvis ögonblicks bild med Azure CLI, du behöver den senaste versionen av Azure CLI. 

I Windows installerar eller uppdaterar du den befintliga installationen till den senaste versionen med följande kommando:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
I Linux kan CLI-installationen variera beroende på operativ systemets version.  Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för din specifika Linux-version.

Om du vill skapa en stegvis ögonblicks bild använder du [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) med parametern `--incremental`.

I följande exempel skapas en stegvis ögonblicks bild, ersätter `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`och `<exampleLocation>` med dina egna värden och kör sedan exemplet:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Du kan identifiera stegvisa ögonblicks bilder från samma disk med `SourceResourceId` och `SourceUniqueId` egenskaper för ögonblicks bilder. `SourceResourceId` är Azure Resource Manager resurs-ID för den överordnade disken. `SourceUniqueId` är värdet som ärvts från diskens egenskap `UniqueId`. Om du skulle ta bort en disk och sedan skapa en ny disk med samma namn ändras värdet för egenskapen `UniqueId`.

Du kan använda `SourceResourceId` och `SourceUniqueId` för att skapa en lista över alla ögonblicks bilder som är associerade med en viss disk. I följande exempel visas en lista över alla stegvisa ögonblicks bilder som är associerade med en viss disk, men den kräver vissa inställningar.

I det här exemplet används JQ för att fråga efter data. Du måste [Installera JQ](https://stedolan.github.io/jq/download/)för att kunna köra exemplet.

Ersätt `<yourResourceGroupNameHere>` och `<exampleDiskName>` med dina värden kan du använda följande exempel för att visa en lista över dina befintliga stegvisa ögonblicks bilder, förutsatt att du även har installerat JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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

## <a name="next-steps"></a>Nästa steg

Om du vill se exempel kod som demonstrerar den differentiella kapaciteten för stegvisa ögonblicks bilder med hjälp av .NET, se [Kopiera Azure-Managed disks säkerhets kopior till en annan region med differentiella funktioner i stegvisa ögonblicks bilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
