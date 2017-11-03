---
title: "Azure PowerShell-skript Sample - beräkna blob-behållaren storlek | Microsoft Docs"
description: "Beräkna storleken på en behållare i Azure Blob storage addera storleken på var och en av dess blobbar."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en behållare för Blob storage

Det här skriptet beräknar storleken på en behållare i Azure Blob storage addera storleken på blobbar i behållaren.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Förstå storleken på Blob storage-behållare

Total storlek på Blob storage-behållare innehåller storleken på själva behållaren och storleken på alla blobbar i behållaren.

Nedan beskrivs hur lagringskapaciteten beräknas för Blob-behållare och Blobbar. I den nedan Len(X): antal tecken i strängen.

### <a name="blob-containers"></a>BLOB-behållare

Nedan visas hur du beräkna hur mycket lagringsutrymme som förbrukas per blob-behållare:

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Följande är en uppdelning:
* 48 byte av omkostnader för varje behållare innehåller ändrades senast, behörigheter, inställningar för offentliga och vissa systemmetadata.
* Behållarens namn lagras som Unicode så ta antalet tecken och multiplicera med 2.
* För varje blobbehållarens metadata lagras, lagrar vi namnet (som lagras i ASCII-format), plus längden på strängvärdet.
* 512 byte per signerade ID innehåller signerade identifierarnamn, starttid, förfallotiden och behörigheter.

### <a name="blobs"></a>Blobar

Nedan visas hur du beräkna hur mycket lagringsutrymme som förbrukas per blob:

* Blockblob (grundläggande blob eller snapshot)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* Sidblob (grundläggande blob eller snapshot)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

Följande är en uppdelning:

* 124 byte av CPU-användningen för blob, vilket innefattar ändrades senast, storlek, Cache-Control, Content-Type, Content-Language Innehållskodning, Content-MD5, behörigheter, ögonblicksbild information, lån och vissa systemmetadata.
* Blobbnamnet lagras som Unicode så ta antalet tecken och multipel av 2.
* Sedan för varje metadata lagras, längden på namnet (som lagras i ASCII-format), plus längden på strängvärdet.
* Sedan för Blockblobbar
    * 8 byte för lista över blockerade
    * Antal block tider ID blockstorlek i byte
    * Plus storleken på data i alla allokerade och ogenomförda block. Tänk när ögonblicksbilder används den här storleken endast innehåller unika data för den här grundläggande eller ögonblicksbild blob. Om ogenomförda blockerar inte används efter en vecka, kommer de att vara skräpinsamlats och sedan då de inte längre räknas som en faktureringen efter.
* Sedan av Sidblobar
    * Flera sidramar med data gånger 12 byte. Detta är antalet unika sidintervall som du kan se när du anropar API: T för GetPageRanges.
    * Plus storleken på data i byte för alla lagrade sidor. Observera när ögonblicksbilder används, innehåller den här storleken endast unika sidor för grundläggande blob eller ögonblicksbild blob som inventeras.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Nästa steg

Mer information om fakturering för Azure Storage finns [Förstå Windows Azure Storage fakturering](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare lagringsutrymme PowerShell-skript-exempel finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
