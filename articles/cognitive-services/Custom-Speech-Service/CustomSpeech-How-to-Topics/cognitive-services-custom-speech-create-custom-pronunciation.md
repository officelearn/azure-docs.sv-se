---
title: Använda anpassade uttal med anpassade tal tjänsten i Azure | Microsoft Docs
description: Lär dig hur du skapar en språkmodell med anpassade tal tjänsten i kognitiva tjänster.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351684"
---
# <a name="enable-custom-pronunciation"></a>Aktivera anpassad uttal
Anpassade uttal kan du definiera fonetisk formuläret och visning av ett ord eller en term. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver är en uttal-fil (en enkel txt-fil).

Här är hur det fungerar. Du kan ange flera anpassade uttal poster i en enda txt-fil. Strukturen är följande:

```
Display form <Tab> Spoken form <Newline>
```

*Exempel*:

| Visa formulär | Talade formulär |
|----------|-------|
| C3PO | finns tre pea o |
| L8R | sen är |
| CNTK | Se n te k|

## <a name="requirements-for-the-spoken-form"></a>Krav för talade formuläret
Formuläret talade måste vara gemener, vilket kan framtvingas under importen. Dessutom måste du ange kontroller i data-Importverktyget. Ingen flik i formuläret talade eller visa formuläret tillåts. Det kan dock, mer är förbjuden tecken i formuläret visas (till exempel ~ och ^).

Varje txt-fil kan ha flera poster. Se exempelvis följande skärmbild:

![Skärmbild av anteckningar med flera poster för förkortning uttal](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

Formuläret talade är fonetisk sekvens av formuläret visas. Det består av bokstäver, ord eller stavelser. Det finns för närvarande ingen ytterligare vägledning eller uppsättning standarder som hjälper dig att formulera talade formuläret. 

## <a name="supported-pronunciation-characters"></a>Stöds uttal tecken
Anpassade uttal stöds för närvarande för engelska (en-US) och tyska (de-de). Teckenuppsättningen som kan användas för att uttrycka talade form av en term (i anpassade uttal-fil) visas i följande tabell: 

| Språk | Karaktärer |
|---------- |----------|
| Engelska (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Tyska (de-de) | ä, ö, ü, ẞ, a och b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

>[OBS] En term Visa formulär (i en uttal-fil) ska skrivas på samma sätt i en datauppsättning för anpassning av språk.

## <a name="requirements-for-the-display-form"></a>Krav för formuläret visas
Endast kan ett visa-formulär ett anpassat ord har löpt ut, förkortning eller sammansatta ord som kombinerar befintliga ord. Du kan också ange alternativa uttal för vanliga ord. 

>[!NOTE]
Vi rekommenderar inte att du använder den här funktionen att reformulate vanliga ord eller ändra talade formuläret. Det är bättre att köra avkodaren för att se om några ovanliga ord (till exempel förkortningar, teknisk ord och ord) inte avkodas korrekt. Om de kan du lägga till dem i filen anpassade uttal. I modellen språk bör du alltid och endast använda Visa form av ett ord. 

## <a name="requirements-for-the-file-size"></a>Krav för storlek
Storleken på den txt-fil som innehåller uttal poster är begränsad till 1 MB. Vanligtvis behöver du inte överföra stora mängder data via den här filen. De flesta anpassade uttal filer är troligt att några KBs i storlek. Kodning av txt-fil för alla språk ska BOM för UTF-8. ANSI är också acceptabel för på engelska.

## <a name="next-steps"></a>Nästa steg
* Förbättra taligenkänning genom att skapa din [anpassade ljud modellen](cognitive-services-custom-speech-create-acoustic-model.md).
* [Skapa en anpassad till text till tal-slutpunkt](cognitive-services-custom-speech-create-endpoint.md), som du kan använda från en app.
