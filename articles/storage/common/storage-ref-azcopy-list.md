---
title: AzCopy-lista | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f4d54e4fd8ca0d43cf3d7087006bb51a15a97bcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289211"
---
# <a name="azcopy-list"></a>azcopy lista

Visar en lista över entiteterna i en specifik resurs.

## <a name="synopsis"></a>Sammanfattning

Endast BLOB-behållare stöds i den aktuella versionen.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot List.|
|--maskinläsbar|Visar en lista över fil storlekar i byte.|
|--megapixlar|Visar enheter i order av 1000, inte 1024.|
|--körs-stämmer|Räknar det totala antalet filer och deras storlek.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s Float|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
