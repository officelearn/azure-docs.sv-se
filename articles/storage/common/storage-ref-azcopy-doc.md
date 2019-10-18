---
title: AzCopy-dok | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy doc.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513864"
---
# <a name="azcopy-doc"></a>azcopy dok

Genererar dokumentation för verktyget i markdown-format.

## <a name="synopsis"></a>Sammanfattning

Genererar dokumentation för verktyget i markdown-format och lagrar dem på den angivna platsen.

Som standard lagras filerna i en mapp med namnet "doc" i den aktuella katalogen.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visar hjälp innehåll för kommandot doc.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
