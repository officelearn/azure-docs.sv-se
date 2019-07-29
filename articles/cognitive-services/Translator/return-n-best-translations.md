---
title: Returnera N-bästa översättningar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Returnera N-bästa översättningar med hjälp av Microsoft Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 7aec6bb81f3ca99ead744767668c2e4f1d1d4d53
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594880"
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
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** Om Authorization-huvudet används, lämnar du fältet AppID tomt annars anger du en sträng som innehåller "Bearer" + "" + åtkomsttoken.|
| text | **Krävs** En sträng som representerar texten som ska översättas. Text storleken får inte överstiga 10000 tecken.|
| from | **Krävs** En sträng som representerar språk koden för den text som ska översättas. |
| till | **Krävs** En sträng som representerar språk koden som texten ska översättas till. |
| maxTranslations | **Krävs** En int som representerar det maximala antalet översättningar som ska returneras. |
| options | **Valfritt** Ett TranslateOptions-objekt som innehåller värdena i listan nedan. De är valfria och standard för de vanligaste inställningarna.

* Kategori Det enda som stöds och standard alternativet är "allmänt".
* Innehålls Det enda alternativ som stöds och standard alternativet är text/plain.
* Status: Användar tillstånd som hjälper till att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* IncludeMultipleMTAlternatives: flagga för att avgöra om fler än ett alternativ ska returneras från MT-motorn. Standardvärdet är falskt och innehåller endast 1 alternativ.

## <a name="ratings"></a>Drivande
Klassificeringarna tillämpas på följande sätt: Den bästa automatiska översättningen har en klassificering på 5.
De automatiskt genererade (N-bästa) översättnings alternativen har en klassificering på 0 och har en matchnings grad på 100.

## <a name="number-of-alternatives"></a>Antal alternativ
Antalet returnerade alternativ är upp till maxTranslations, men det kan vara mindre.

## <a name="language-pairs"></a>Språk par
Den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Det är tillgängligt för alla andra språk par som stöds av Microsoft Translator.
