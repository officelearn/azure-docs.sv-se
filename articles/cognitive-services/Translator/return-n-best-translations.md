---
title: Returnera översättningar för N-Best - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Returnera N-Best översättningar med hjälp av Microsoft Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: ea409a4295cb07800e3f48ab408135071d0faea6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464831"
---
# <a name="how-to-return-n-best-translations"></a>Hur du returnerar N-Best översättningar

> [!NOTE]
> Den här metoden är inaktuell. Det är inte tillgängliga i version 3.0 av Translator Text API.

GetTranslations() och GetTranslationsArray() sätt att Microsoft Translator-API är en valfri boolesk flagga ”IncludeMultipleMTAlternatives”.
Metoden returnerar upp till maxTranslations alternativ där delta har angetts i N-Best-listan över translator-motorn.

Signaturen är:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** om auktoriseringsrubriken används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| text | **Krävs** en sträng som representerar text för översättning. Storleken på texten får inte överstiga 10000 tecken.|
| från | **Krävs** en sträng som representerar språkkoden för text för översättning. |
| till | **Krävs** en sträng som representerar språkkoden att översätta text i. |
| maxTranslations | **Krävs** ett heltal som representerar det maximala antalet översättningar ska returneras. |
| alternativ | **Valfritt** A TranslateOptions-objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard.

* Kategori: Det går endast att och standard, alternativet är ”Allmänt”.
* ContentType: Det går endast att och standard, alternativet är ”text/plain”.
* Tillstånd: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* IncludeMultipleMTAlternatives: flagga för att bestämma om du vill returnera fler än ett alternativ från MT-motorn. Standard är FALSKT och innehåller endast 1 alternativ.

## <a name="ratings"></a>Klassificeringar
Klassificeringarna som tillämpas på följande sätt: Den bästa automatisk översättningen har en klassificering på 5.
Alternativen för översättning av automatiskt genererade (N-Best) har en klassificering på 0 och har en matchning på 100.

## <a name="number-of-alternatives"></a>Antalet alternativ
Antalet returnerade alternativ är upp till maxTranslations, men kan vara mindre.

## <a name="language-pairs"></a>Språkpar
Den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningarna. Det är tillgängligt för alla andra språkpar med Microsoft Translator stöds.
