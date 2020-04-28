---
title: AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 27c06656d95c5165b33b6056a3cf3b554f0e5469
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034114"
---
# <a name="azcopy-jobs"></a>azcopy jobb

Underordnade kommandon som är relaterade till hantering av jobb.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot jobb.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
- [azcopy jobblista](storage-ref-azcopy-jobs-list.md)
- [azcopy jobb-CV](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobbvisning](storage-ref-azcopy-jobs-show.md)
