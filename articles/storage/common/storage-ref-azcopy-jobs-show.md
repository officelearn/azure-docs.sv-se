---
title: AzCopy-jobb Visa | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195963"
---
# <a name="azcopy-jobs-show"></a>AzCopy-jobb Visa

Visar detaljerad information om angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

Om endast jobb-ID anges utan någon flagga returneras förlopps sammanfattningen för jobbet.

`with-status` Om flaggan anges, visas listan över överföringar i jobbet med det angivna värdet.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visar hjälp innehåll för kommandot show.|
|--med-status sträng|Visa endast överföringar av jobb med denna status, tillgängliga värden: Startade, lyckades, misslyckades|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
