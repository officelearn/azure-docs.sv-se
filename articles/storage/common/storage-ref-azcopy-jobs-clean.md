---
title: azcopy jobb ren | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för det rensar kommandot för azkoposkopijobb.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033726"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Ta bort alla logg- och planfiler för alla jobb

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Alternativ

**-h, --hjälp**                Hjälp för ren.

**--with-status sträng** Ta bara bort jobb med den här statusen, tillgängliga värden: Avbruten, Slutförd, Misslyckades, InProgress, Alla (standard "Alla")

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

**--cap-mbps uint32 --cap-mbps**      Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.

**--utdata-typ** sträng Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text")

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
