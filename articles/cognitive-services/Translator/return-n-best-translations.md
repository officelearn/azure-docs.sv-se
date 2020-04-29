---
title: Returnera N-bästa översättningar – Translator Text
titleSuffix: Azure Cognitive Services
description: Returnera N-bästa översättningar med hjälp av Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837243"
---
# <a name="how-to-return-n-best-translations"></a>Så här returnerar du N-bästa översättningar

> [!NOTE]
> Den här metoden är föråldrad. Den är inte tillgänglig i V 3.0 för Translator Text API.

Metoderna GetTranslations () och GetTranslationsArray () i API för Microsoft Translator inkluderar en valfri boolesk flagga "IncludeMultipleMTAlternatives".
Metoden kommer att returnera upp till maxTranslations alternativ där delta har angetts från den N-bästa listan över Translator-motorn.

Signaturen är:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft. translator. GetTranslations (appId, text, from, to, maxTranslations, Options); |

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** Om Authorization-huvudet används, lämnar du fältet AppID tomt annars anger du en sträng som innehåller "Bearer" + "" + åtkomsttoken.|
| text | **Krävs** En sträng som representerar texten som ska översättas. Text storleken får inte överstiga 10000 tecken.|
| Från | **Krävs** En sträng som representerar språk koden för den text som ska översättas. |
| till | **Krävs** En sträng som representerar språk koden som texten ska översättas till. |
| maxTranslations | **Krävs** En int som representerar det maximala antalet översättningar som ska returneras. |
| alternativ | **Valfritt** Ett TranslateOptions-objekt som innehåller värdena i listan nedan. De är valfria och standard för de vanligaste inställningarna.

* Kategori: det enda som stöds och standard alternativet är "allmänt".
* ContentType: det enda som stöds och standard alternativet är text/plain.
* Tillstånd: användar tillstånd för att hjälpa till att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* IncludeMultipleMTAlternatives: flagga för att avgöra om fler än ett alternativ ska returneras från MT-motorn. Standardvärdet är falskt och innehåller endast 1 alternativ.

## <a name="ratings"></a>Klassificeringar
Klassificeringarna tillämpas på följande sätt: den bästa automatiska översättningen har en klassificering på 5.
De automatiskt genererade (N-bästa) översättnings alternativen har en klassificering på 0 och har en matchnings grad på 100.

## <a name="number-of-alternatives"></a>Antal alternativ
Antalet returnerade alternativ är upp till maxTranslations, men det kan vara mindre.

## <a name="language-pairs"></a>Språk par
Den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Det är tillgängligt för alla andra språk par som stöds av Microsoft Translator.
