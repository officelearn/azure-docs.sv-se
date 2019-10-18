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
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513447"
---
# <a name="azcopy-jobs-show"></a>azcopy jobbvisning

Visar detaljerad information om angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

Om endast jobb-ID anges utan någon flagga returneras förlopps sammanfattningen för jobbet.

Antalet byte och procent färdigt som visas när du kör det här kommandot återspeglar bara filer som har slutförts i jobbet. De återspeglar inte delvis slutförda filer.

Om flaggan `with-status` anges, visas listan över överföringar i jobbet med det angivna värdet.

```azcopy
azcopy jobs show [jobID] [flags]
```

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

## <a name="see-also"></a>Se också

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
