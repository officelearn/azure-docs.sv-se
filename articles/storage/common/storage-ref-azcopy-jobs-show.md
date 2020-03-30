---
title: azcopy jobb visar | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot azcopy jobb show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034128"
---
# <a name="azcopy-jobs-show"></a>azcopy jobbvisning

Visar detaljerad information för det angivna jobb-ID:t.

## <a name="synopsis"></a>Synopsis

Om endast jobb-ID:et anges utan flagga returneras förloppssammanfattningen för jobbet.

Antal byte och procent slutförda som visas när du kör det här kommandot återspeglar bara filer som har slutförts i jobbet. De återspeglar inte delvis slutförda filer.

Om `with-status` flaggan är inställd visas listan över överföringar i jobbet med det angivna värdet.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --hjälp|Visar hjälpinnehåll för visa-kommandot.|
|--med-statussträng|Lista endast överföringar av jobb med den här statusen, tillgängliga värden: Startad, Lyckad, Misslyckades|

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
