---
title: Azure PowerShell-skript sample - beräkna den totala fakturering storleken på en blob-behållare | Microsoft Docs
description: Beräkna den totala storleken på en behållare i Azure Blob storage för fakturering.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: c37b416578a76e9b12e29d68e413d851796ccc6f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
ms.locfileid: "26368552"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Beräkna den totala fakturering storleken för en blobbbehållare

Det här skriptet beräknar storleken på en behållare i Azure Blob storage för att uppskatta fakturering kostnader. Skriptet summeras storleken på blobbar i behållaren.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Detta PowerShell-skript beräknar storleken på en behållare för fakturering. Om du beräknar behållaren storleken för andra ändamål, se [beräkna den totala storleken på en behållare för Blob storage](../scripts/storage-blobs-container-calculate-size-powershell.md) för ett enklare skript som innehåller en uppskattning.

## <a name="determine-the-size-of-the-blob-container"></a>Bestämma storleken på blob-behållaren

Den totala storleken på blob-behållaren innehåller storleken på själva behållaren och storleken på alla blobbar i behållaren.

I följande avsnitt beskrivs hur lagringskapaciteten beräknas för blobbbehållare och blobbar. I följande avsnitt: Len(X) antal tecken i strängen.

### <a name="blob-containers"></a>BLOB-behållare

Beräkningen nedan beskrivs hur du beräkna hur mycket lagringsutrymme som används per blob-behållare:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Här är:
* 48 byte av omkostnader för varje behållare innehåller ändrades senast, behörigheter, inställningar för offentliga och vissa systemmetadata.

* Behållarens namn lagras som Unicode, så ta antalet tecken och multiplicera med två.

* För varje datablock blobbehållarens metadata som lagras, lagrar vi namnet (ASCII) plus längden på strängvärdet.

* 512 byte per signerade ID innehåller signerade identifierarnamn, starttid, förfallotiden och behörigheter.

### <a name="blobs"></a>Blobar

Följande beräkningar visar hur du beräkna hur mycket lagringsutrymme som förbrukas per blob.

* Blockblob (grundläggande blob eller snapshot):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Sidblob (grundläggande blob eller snapshot):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Här är:

* 124 byte av CPU-användningen för blob, vilket innefattar:
    - Senast ändrad
    - Storlek
    - Cache-Control
    - Innehållstyp
    - Content-Language
    - Innehållskodning
    - Content-MD5
    - Behörigheter
    - Snapshot-information
    - Lån
    - Vissa systemmetadata

* Blobbnamnet lagras som Unicode, så ta antalet tecken och multiplicera med två.

* Lägg till namnet (som lagras i ASCII-format) plus längden på strängvärdet för varje block på metadata som lagras.

* För blockblobbar:
    * 8 byte för i blockeringslistan.
    * Antal block gånger ID blockstorlek i byte.
    * Storleken på data i alla allokerade och ogenomförda block. 
    
    >[!NOTE]
    >När ögonblicksbilder används, innehåller den här storleken endast unika data för den här grundläggande eller ögonblicksbild blob. Om du inte använder en block efter en vecka, är de skräpinsamlats. Sedan kan räknas de inte in fakturering.

* För sidblobbar:
    * Flera sidramar med data antal 12 byte. Detta är antalet unika sidintervall som du kan se när du anropar den **GetPageRanges** API.

    * Storleken på data i byte för alla lagrade sidor. 
    
    >[!NOTE]
    >När ögonblicksbilder används, innehåller den här storleken endast unika sidor för grundläggande blob eller snapshot-blob som inventeras.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Nästa steg

- Se [beräkna den totala storleken på en behållare för Blob storage](../scripts/storage-blobs-container-calculate-size-powershell.md) för ett enkelt skript som innehåller en uppskattning av behållarens storlek.

- Läs mer om Azure Storage fakturering [Förstå Windows Azure Storage fakturering](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Läs mer om Azure PowerShell-modulen [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1).

- Du kan hitta ytterligare lagring PowerShell skriptexempel i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
