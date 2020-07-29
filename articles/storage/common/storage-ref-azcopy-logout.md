---
title: AzCopy-utloggning | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy utloggnings kommando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 162946ca2de9e613f0ad92297944a9f5390ab378
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285262"
---
# <a name="azcopy-logout"></a>azcopy utloggning

Loggar ut användaren och avslutar åtkomsten till Azure Storage resurser.

## <a name="synopsis"></a>Sammanfattning

Det här kommandot tar bort all cachelagrad inloggnings information för den aktuella användaren.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot logga ut.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
