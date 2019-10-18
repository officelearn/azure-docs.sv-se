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
ms.openlocfilehash: 9e8dbbd40259c7a71f252d0d6e93dd6f135973de
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513536"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobb-CV

Återupptar det befintliga jobbet med angivet jobb-ID.

## <a name="synopsis"></a>Sammanfattning

```azcopy
azcopy jobs resume [jobID] [flags]
```

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

## <a name="see-also"></a>Se också

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
