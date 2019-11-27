---
title: Ladda upp en virtuell hård disk till Azure med Azure CLI
description: Lär dig hur du laddar upp en virtuell hård disk till en Azure-hanterad disk och kopierar en hanterad disk mellan regioner med hjälp av Azure CLI via direkt uppladdning.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 51c3933b5ee585c96ad81fe04d379b6771ae81e3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457604"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Ladda upp en virtuell hård disk till Azure med Azure CLI

Den här artikeln förklarar hur du laddar upp en virtuell hård disk från din lokala dator till en Azure-hanterad disk. Tidigare var du tvungen att följa en mer engagerad process som inkluderade mellanlagring av dina data i ett lagrings konto och hantering av lagrings kontot. Nu behöver du inte längre hantera ett lagrings konto eller mellanlagra data i det för att ladda upp en virtuell hård disk. I stället skapar du en tom hanterad disk och laddar upp en virtuell hård disk direkt till den. Detta fören klar överföringen av lokala virtuella datorer till Azure och gör att du kan ladda upp en virtuell hård disk på upp till 32 TiB direkt i en stor hanterad disk.

Om du tillhandahåller en säkerhets kopierings lösning för virtuella IaaS-datorer i Azure rekommenderar vi att du använder direkt uppladdning för att återställa kundens säkerhets kopior till hanterade diskar. Om du överför en virtuell hård disk från en dator som är extern till Azure beror hastigheten på din lokala bandbredd. Om du använder en virtuell Azure-dator är bandbredden samma som standard hård diskar.

För närvarande stöds direkt uppladdning för standard hård diskar, standard SSD och Premium SSD-hanterade diskar. Det stöds ännu inte för Ultra SSD.

## <a name="prerequisites"></a>Förutsättningar

- Ladda ned den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure CLI](/cli/azure/install-azure-cli).
- En VHD-fil, lagrad lokalt
- Om du tänker Ladda upp en virtuell hård disk från en lokal plats: en virtuell hård disk som [har förberetts för Azure](../windows/prepare-for-upload-vhd-image.md), lagrad lokalt.
- Eller en hanterad disk i Azure om du vill utföra en kopierings åtgärd.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Om du vill överföra din virtuella hård disk till Azure måste du skapa en tom hanterad disk som har kon figurer ATS för den här överförings processen. Innan du skapar en sådan finns det ytterligare information som du bör känna till om de här diskarna.

Den här typen av hanterade diskar har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en uppladdning, men [att ingen säker åtkomst-signatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och att SAS har genererats.

I något av dessa tillstånd debiteras den hanterade disken med [standard priset för hård](https://azure.microsoft.com/pricing/details/managed-disks/)diskar, oavsett vilken typ av disk som används. Till exempel kommer en P10 att faktureras som en S10. Detta är sant tills `revoke-access` anropas på den hanterade disken, vilket krävs för att ansluta disken till en virtuell dator.

Innan du kan skapa en tom standard hård disk för uppladdning måste du ha fil storleken på den virtuella hård disk som du vill ladda upp, i byte. För att få det kan du använda antingen `wc -c <yourFileName>.vhd` eller `ls -al <yourFileName>.vhd`. Det här värdet används när du anger parametern **--upload-size-bytes** .

Skapa en tom standard hård disk för uppladdning genom att ange både parametrarna **-– för-upload** och parametern **--upload-size-byte** i en [disk Create](/cli/azure/disk#az-disk-create) -cmdlet:

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Om du vill ladda upp antingen en Premium SSD eller en standard SSD ersätter du **standard_lrs** med antingen **premium_LRS** eller **standardssd_lrs**. Ultra SSD stöds ännu inte.

Nu har du skapat en tom hanterad disk som är konfigurerad för överförings processen. Om du vill ladda upp en virtuell hård disk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för överföringen.

Använd följande kommando för att generera en skrivbar SAS för din tomma hanterade disk:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Exempel på returnerat värde:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Ladda upp VHD

Nu när du har en SAS för din tomma hanterade disk kan du använda den för att ange den hanterade disken som mål för ditt upload-kommando.

Använd AzCopy v10 för att ladda upp din lokala VHD-fil till en hanterad disk genom att ange den SAS-URI som du skapade.

Den här uppladdningen har samma data flöde som motsvarande [standard-hårddisk](disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4, kommer du att ha ett data flöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70 har du ett data flöde på upp till 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Om din SAS går ut under uppladdningen och du inte har anropat `revoke-access` ännu, kan du hämta en ny SAS för att fortsätta med överföringen med `grant-access`.

När uppladdningen är klar och du inte längre behöver skriva mer data till disken ska du återkalla SAS. Att återkalla SAS ändrar statusen för den hanterade disken och låter dig ansluta disken till en virtuell dator.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direkt överföring fören klar också processen att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller mellan regioner (till en annan region).

Följ skriptet gör detta åt dig, processen liknar de steg som beskrivs ovan, med vissa skillnader eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du tillhandahåller disk storleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när den returnerar disk storleken. Kopieringen Miss kommer om du inte gör det. Följande skript använder redan det här.

Ersätt `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`och `<yourTargetLocationHere>` (ett exempel på ett plats värde är uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

```bash
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

Nu när du har laddat upp en virtuell hård disk till en hanterad disk kan du koppla disken som en [datadisk till en befintlig virtuell dator](add-disk.md) eller [ansluta disken till en virtuell dator som en operativ system disk](upload-vhd.md#create-the-vm)för att skapa en ny virtuell dator. 

