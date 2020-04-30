---
title: Data begränsningar för API för textanalys
titleSuffix: Azure Cognitive Services
description: Data begränsningar för API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 4af2d060c11b804c5fa09bfdabbcb9753f7d5885
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204416"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Data-och hastighets begränsningar för API för textanalys
<a name="data-limits"></a>

Använd den här artikeln för att hitta gränserna för storlek och frekvenser som du kan skicka data till API för textanalys. 

## <a name="data-limits"></a>Databegränsningar

> [!NOTE]
> * Om du behöver analysera större dokument än gränsen tillåter kan du dela upp texten i mindre text segment innan du skickar dem till API: et. 
> * Ett dokument är en enskild sträng med text tecken.  

| Gräns | Värde |
|------------------------|---------------|
| Maximal storlek på ett enskilt dokument | 5 120 tecken som mäts av [StringInfo. LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximal storlek på hela begäran | 1 MB |

Det maximala antalet dokument som du kan skicka i en enskild begäran beror på vilken API-version och funktion du använder.

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

> [!NOTE]
> Om din v3 API-begäran överskrider dessa gränser, men ligger inom v2-gränser, returneras en varning i API-svaret. Från och med 7/15/2020 kommer en 400-felkod att returneras i stället. 

Följande gränser har ändrats i v3 för API: et. Att överskrida gränserna nedan genererar en varning i API-svaret.


| Funktion | Maximalt antal dokument per begäran | 
|----------|-----------|
| Språkidentifiering | 1000 |
| Attitydanalys | 10 |
| Extrahering av nyckelfraser | 10 |
| Igenkänning av namngiven enhet | 5 |
| Entity Linking | 5 |

#### <a name="version-2"></a>[Version 2](#tab/version-2)

| Funktion | Maximalt antal dokument per begäran | 
|----------|-----------|
| Språkidentifiering | 1000 |
| Attitydanalys | 1000 |
| Extrahering av nyckelfraser | 1000 |
| Igenkänning av namngiven enhet | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>Hastighets begränsningar

Din hastighets gräns varierar beroende på [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Dessa begränsningar är desamma för båda versionerna av API: et.

| Nivå          | Förfrågningar per sekund | Begär Anden per minut |
|---------------|---------------------|---------------------|
| S/multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Begär Anden mäts för varje Textanalys funktion separat. Du kan till exempel skicka maximalt antal begär Anden för din pris nivå till varje funktion på samma tillfälle.  


## <a name="see-also"></a>Se även

* [Vad är API för textanalys](../overview.md)
* [Pris information](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
