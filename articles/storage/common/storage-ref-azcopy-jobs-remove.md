---
title: azcopy jobb bort | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot azcopy jobb ta bort.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034163"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Ta bort alla filer som är associerade med det angivna jobb-ID:et.

> [!NOTE] 
> Du kan anpassa platsen där logg- och planfiler sparas. Läs [kommandot askacopy env](storage-ref-azcopy-env.md) om du vill veta mer.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Alternativ

**-h, --hjälp**                Hjälp för att ta bort.

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

**--cap-mbps uint32 --cap-mbps**      Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.

**--utdata-typ** sträng Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text")

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
