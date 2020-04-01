---
title: Ladda upp en virtuell hårddisk till Azure eller kopiera en disk mellan regioner - Azure CLI
description: Lär dig hur du laddar upp en virtuell hårddisk till en Azure-hanterad disk och kopierar en hanterad disk över regioner, med hjälp av Azure CLI, via direkt överföring.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420949"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Ladda upp en virtuell hårddisk till Azure eller kopiera en hanterad disk till en annan region - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Krav

- Ladda ner den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure CLI](/cli/azure/install-azure-cli).
- Om du tänker ladda upp en virtuell hårddisk från lokalt: En fast storlek VHD som [har förberetts för Azure](../windows/prepare-for-upload-vhd-image.md), lagras lokalt.
- Eller en hanterad disk i Azure, om du tänker utföra en kopieringsåtgärd.

## <a name="getting-started"></a>Komma igång

Om du föredrar att ladda upp diskar via ett GUI kan du göra det med Hjälp av Azure Storage Explorer. Mer information finns i: [Använda Azure Storage Explorer för att hantera Azure-hanterade diskar](disks-use-storage-explorer-managed-disks.md)

Om du vill överföra din virtuella hårddisk till Azure måste du skapa en tom hanterad disk som är konfigurerad för den här uppladdningsprocessen. Innan du skapar en finns det ytterligare information som du bör känna till om dessa diskar.

Den här typen av hanterad disk har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en överföring men ingen [säker åtkomstsignatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och SAS har genererats.

> [!NOTE]
> I något av dessa lägen faktureras den hanterade disken med standardpriser för [hårddiskar](https://azure.microsoft.com/pricing/details/managed-disks/), oavsett vilken typ av disk det är. Till exempel kommer en P10 att faktureras som en S10. Detta kommer att `revoke-access` vara sant tills anropas på den hanterade disken, vilket krävs för att koppla disken till en virtuell dator.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Innan du kan skapa en tom standard hdd för uppladdning, behöver du filstorleken på den virtuella hårddisken du vill ladda upp, i byte. För att få det `wc -c <yourFileName>.vhd` kan `ls -al <yourFileName>.vhd`du använda antingen eller . Det här värdet används när parametern **--upload-size-bytes** anges.

Skapa en tom standard hdd för uppladdning genom att ange både **parametern --for-upload** och parametern **--upload-size-bytes** i en [disk skapa](/cli/azure/disk#az-disk-create) cmdlet:

Ersätt `<yourdiskname>` `<yourresourcegroupname>`, `<yourregion>` , med värden som du väljer. Parametern `--upload-size-bytes` innehåller ett exempelvärde `34359738880`på , ersätt det med ett värde som är lämpligt för dig.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Om du vill ladda upp antingen en premium SSD eller en vanlig SSD, ersätta **standard_lrs** med antingen **premium_LRS** eller **standardssd_lrs**. Ultra diskar stöds inte för nu.

Nu när du har skapat en tom hanterad disk som är konfigurerad för uppladdningsprocessen kan du ladda upp en virtuell hårddisk till den. Om du vill ladda upp en virtuell hårddisk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för din uppladdning.

Om du vill generera en skrivbar SAS `<yourdiskname>` `<yourresourcegroupname>`för den tomma hanterade disken ersätter du och använder sedan följande kommando:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Exempel returnerat värde:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk

Nu när du har en SAS för den tomma hanterade disken kan du använda den för att ange den hanterade disken som mål för ditt uppladdningskommando.

Använd AzCopy v10 för att ladda upp den lokala VHD-filen till en hanterad disk genom att ange den SAS URI som du har skapat.

Denna uppladdning har samma dataflöde som motsvarande [standard HDD](disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4 har du ett dataflöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70, kommer du att ha en genomströmning på upp till 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

När överföringen är klar och du inte längre behöver skriva några fler data till disken återkallar du SAS.After the upload is complete, and you no longer need to write any more data to the disk, revoke the SAS. Om du återkallar SAS ändras tillståndet för den hanterade disken och du kan koppla disken till en virtuell dator.

Ersätt `<yourdiskname>` `<yourresourcegroupname>`och använd sedan följande kommando för att göra disken användbar:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direktuppladdning förenklar också processen för att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller korsregion (till en annan region).

Följande skript kommer att göra detta åt dig, processen liknar de steg som beskrivs tidigare, med vissa skillnader eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du anger diskstorleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när du returnerar diskstorleken. Kopian misslyckas om du inte gör detta. Följande skript gör redan detta åt dig.

Ersätt `<sourceResourceGroupHere>`, `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourTargetLocationHere>` och (ett exempel på ett platsvärde skulle vara uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Nästa steg

Nu när du har laddat upp en virtuell hårddisk till en hanterad disk kan du ansluta disken som en [datadisk till en befintlig virtuell dator](add-disk.md) eller ansluta [disken till en virtuell dator som en OS-disk](upload-vhd.md#create-the-vm)för att skapa en ny virtuell dator. 

