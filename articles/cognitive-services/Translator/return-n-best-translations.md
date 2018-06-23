---
title: Returnera N bäst översättningar med Microsoft Translator texten API | Microsoft Docs
description: Returnera N bäst översättningar med hjälp av Microsoft översättare Text API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 3eafe50f69ae1a6748342e64a414ecee4467d0d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352599"
---
# <a name="how-to-return-n-best-translations"></a>Returnera N bäst översättningar

> [!NOTE]
> Den här metoden är föråldrad. Det är inte tillgänglig i V3.0 översättare Text API.

GetTranslations() och GetTranslationsArray() sätt att Microsoft översättare API är en valfri boolesk flagga ”IncludeMultipleMTAlternatives”.
Metoden returnerar upp till maxTranslations alternativ där delta har angetts i listan N bäst översättare-motorn.

Signaturen är:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** om Authorization-huvud används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| text | **Krävs** en sträng som representerar texten som ska översätta. Storleken på text får inte överskrida 10000 tecken.|
| från | **Krävs** en sträng som representerar språkkoden för texten som ska översätta. |
| till | **Krävs** en sträng som representerar språkkoden att översätta text till. |
| maxTranslations | **Krävs** ett heltal som representerar det maximala antalet översättningar som ska returneras. |
| alternativ | **Valfria** A TranslateOptions-objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard.

* Kategori: Det går endast att och standard, alternativet är ”Allmänt”.
* ContentType: Det går endast att och standardvärdet är alternativet ”text/plain”.
* Tillstånd: Användarens tillstånd för att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* IncludeMultipleMTAlternatives: flagga för att bestämma om du vill returnera fler än ett alternativ från Huvudmålservern-motorn. Standard är FALSKT och innehåller endast 1 alternativ.

## <a name="ratings"></a>Klassificering
Klassificeringarna tillämpas enligt följande: bästa automatisk översättning klassificeringsvärde 5.
Alternativen för översättning av automatiskt genererade (N bäst) har en klassificering på 0 och har en matchning 100.

## <a name="number-of-alternatives"></a>Antal alternativ
Antalet returnerade alternativ är upp till maxTranslations, men kan vara mindre.

## <a name="language-pairs"></a>Språkpar
Den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningar. Den är tillgänglig för alla andra Microsoft Translator stöds språkpar.
