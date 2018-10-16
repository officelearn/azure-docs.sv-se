---
title: Använda anpassade uttal med Custom Speech Service på Azure | Microsoft Docs
description: Lär dig hur du skapar en språkmodell med Custom Speech Service i Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: 1e9de9755ef4baee7e2a0e6c093e2cf27cb4281f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339822"
---
# <a name="enable-custom-pronunciation"></a>Aktivera anpassad uttal

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Anpassade uttal kan du definiera fonetiska, formulär och visning av ett ord eller en term. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver är en uttal-fil (en enkel txt-fil).

Här är hur det fungerar. Du kan ange flera anpassade uttal av poster i en enda txt-fil. Strukturen är följande:

```
Display form <Tab> Spoken form <Newline>
```

*Exempel*:

| Visa formulär | Talat formulär |
|----------|-------|
| C3PO | Se tre högsta användningsnivå o |
| L8R | sent är |
| CNTK | Se n te k|

## <a name="requirements-for-the-spoken-form"></a>Krav för talat formuläret
Formuläret talat måste vara gemener, vilket kan framtvingas under importen. Dessutom måste du ange checkar in importören data. Ingen flik i formuläret talat eller visa formulär tillåts. Det kan dock, förbjudas fler tecken i Visa formulär (exempelvis ~ och ^).

Varje txt-fil kan ha flera poster. Se exempelvis följande skärmbild:

![Skärmbild av anteckningar med flera poster för förkortning uttal](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

Formuläret talat är de fonetiska, visa formulär. Den består av bokstäver, ord eller stavelser. Det finns för närvarande inga ytterligare vägledning eller uppsättning standarder för att hjälpa dig att formulera talat formuläret. 

## <a name="supported-pronunciation-characters"></a>Stöds uttal tecken
Anpassade uttal är för närvarande stöd för engelska (en-US) och tyska (de-de). Teckenuppsättning som kan användas för att uttrycka talat form av en term (i anpassade uttal-fil) visas i följande tabell: 

| Språk | Karaktärer |
|---------- |----------|
| Engelska (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, frågor och, r, s, t, u, v, w, x, y, z |
| Tyska (de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, frågor och, r, s, t, u, v, w, x, y, z |

>[OBS] En term Visa formulär (i en uttal-fil) ska skrivas på samma sätt i en datamängd för anpassning av språk.

## <a name="requirements-for-the-display-form"></a>Krav för Visa formulär
Visningsformulär kan endast vara ett anpassat ord, termen, förkortning eller sammansatta ord som kombinerar befintliga ord. Du kan också ange alternativa uttal för vanliga ord. 

>[!NOTE]
Vi rekommenderar inte att du använder den här funktionen att reformulate vanliga ord eller ändra talat formuläret. Är det bättre att köra avkodaren för att se om några ovanliga ord (till exempel förkortningar, teknisk ord och främmande ord) inte avkodas korrekt. Om de kan du lägga till dem till anpassade uttal av filen. I språkmodell, bör du alltid och endast använda Visningsformulär ett ord. 

## <a name="requirements-for-the-file-size"></a>Krav för filstorleken
Storleken på den txt-fil som innehåller uttal av poster är begränsad till 1 MB. Vanligtvis behöver du inte ladda upp stora mängder data via den här filen. De flesta anpassade uttal filer är sannolikt några KB-artiklar i storlek. Kodning av txt-fil för alla språk bör vara UTF-8 BOM. ANSI är också tillåtet för på engelska.

## <a name="next-steps"></a>Nästa steg
* Förbättra noggrannheten genom att skapa din [anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md).
* [Skapa en anpassad tal till text-slutpunkt](cognitive-services-custom-speech-create-endpoint.md), som du kan använda från en app.
