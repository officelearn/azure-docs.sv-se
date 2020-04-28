---
title: AzCopy-jobb Visa | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034128"
---
# <a name="azcopy-jobs-show"></a>azcopy jobbvisning

Visar detaljerad information om angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

Om endast jobb-ID anges utan någon flagga returneras förlopps sammanfattningen för jobbet.

Antalet byte och procent färdigt som visas när du kör det här kommandot återspeglar bara filer som har slutförts i jobbet. De återspeglar inte delvis slutförda filer.

Om `with-status` flaggan anges, visas listan över överföringar i jobbet med det angivna värdet.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visar hjälp innehåll för kommandot show.|
|--med-status sträng|Visa endast överföringar av jobb med denna status, tillgängliga värden: startade, lyckades, misslyckades|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
