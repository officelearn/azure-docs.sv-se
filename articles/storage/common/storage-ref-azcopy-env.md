---
title: azoskopi env | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033747"
---
# <a name="azcopy-env"></a>azcopy miljö

Visar de miljövariabler som kan konfigurera AzCopys beteende.

## <a name="synopsis"></a>Synopsis

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Om du anger en miljövariabel med hjälp av kommandoraden kan variabeln läsas i kommandoradshistoriken. Överväg att rensa variabler som innehåller autentiseringsuppgifter från kommandoradshistoriken. Om du vill förhindra att variabler visas i historiken kan du använda ett skript för att fråga användaren om deras autentiseringsuppgifter och ange miljövariabeln.

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h, --hjälp|Visar hjälpinnehåll för kommandot env. |
|--show-känslig|Visar känsliga/hemliga miljövariabler.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
