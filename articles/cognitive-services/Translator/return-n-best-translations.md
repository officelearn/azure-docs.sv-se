---
title: Returnera N-bästa översättningar - Översättare Text
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837243"
---
# <a name="how-to-return-n-best-translations"></a>Så här returnerar du N-Best-översättningar

> [!NOTE]
> Den här metoden är föråldrad. Den är inte tillgänglig i V3.0 i Translator Text API.

Metoderna GetTranslations() och GetTranslationsArray() i Microsoft Translator API innehåller en valfri boolesk flagga "IncludeMultipleMTAlternatives".
Metoden returnerar upp till maxTranslations alternativ där deltat levereras från N-Best-listan över översättarens motor.

Signaturen är:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, från, till, maxTranslations, alternativ); |

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** Om auktoriseringshuvudet används lämnar du appid-fältet tomt annars anger du en sträng som innehåller "Bärare" + " + åtkomsttoken.|
| text | **Krävs** En sträng som representerar texten att översätta. Textens storlek får inte överstiga 10000 tecken.|
| Från | **Krävs** En sträng som representerar språkkoden för texten som ska översättas. |
| till | **Krävs** En sträng som representerar språkkoden att översätta texten till. |
| maxTranslations | **Krävs** Ett int som representerar det maximala antalet översättningar som ska returneras. |
| alternativ | **Valfritt** Ett TranslateOptions-objekt som innehåller värdena nedan. De är alla valfria och standard till de vanligaste inställningarna.

* Kategori: Det enda som stöds, och standardalternativet, är "allmänt".
* ContentType: Det enda som stöds, och standardalternativet, är "text/oformaterad".
* Tillstånd: Användartillstånd för att korrelera begäran och svar. Samma innehåll returneras i svaret.
* IncludeMultipleMTAlternatives: flagga för att avgöra om du vill returnera mer än ett alternativ från MT-motorn. Standard är falskt och innehåller endast 1 alternativ.

## <a name="ratings"></a>Klassificeringar
Betygen tillämpas på följande sätt: Den bästa automatiska översättningen har betyget 5.
De automatiskt genererade (N-Best) översättningsalternativen har betyget 0 och har en matchningsgrad på 100.

## <a name="number-of-alternatives"></a>Antal alternativ
Antalet returnerade alternativ är upp till maxTranslations, men kan vara mindre.

## <a name="language-pairs"></a>Språkpar
Den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Den är tillgänglig för alla andra språkpar som stöds av Microsoft Translator.
