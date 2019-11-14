---
title: återuppta AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs Resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034145"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobb-CV

Återupptar det befintliga jobbet med angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--mål – SAS-sträng|Mål-SAS för målet för angivet JobId.|
|--Exkludera sträng|Filtrera: exkludera de här misslyckade överföringarna när jobbet återupptas. Filerna ska avgränsas med ";".|
|-h,--hjälp|Visa hjälp innehåll för kommandot Resume.|
|--inkludera sträng|Filter: ta bara med de här misslyckade överföringarna när jobbet återupptas. Filerna ska avgränsas med ";".|
|--Källa – SAS-sträng |käll-SAS för källan för angivet JobId.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
