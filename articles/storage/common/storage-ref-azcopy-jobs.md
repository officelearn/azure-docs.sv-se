---
title: azcopy jobb | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot askakopiering.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 27c06656d95c5165b33b6056a3cf3b554f0e5469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034114"
---
# <a name="azcopy-jobs"></a>azcopy jobb

Underkommandon som är relaterade till hantering av jobb.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --hjälp|Visa hjälpinnehåll för jobbkommandot.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
- [azcopy jobblista](storage-ref-azcopy-jobs-list.md)
- [azcopy jobb-CV](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobbvisning](storage-ref-azcopy-jobs-show.md)
