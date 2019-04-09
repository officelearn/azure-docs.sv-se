---
title: Anpassa uttal – Speech Services
titlesuffix: Azure Cognitive Services
description: Lär dig hur du anpassar uttal med Speech-tjänsten. Du kan definiera fonetiska, formulär och visning av ett ord eller en term med anpassade uttal. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: f825cf8f381a7a2974b150a74a091412b24b09bc
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005160"
---
# <a name="enable-custom-pronunciation"></a>Aktivera anpassad uttal

Du kan definiera fonetiska, formulär och visning av ett ord eller en term (förkortning) med hjälp av anpassade uttal. Det är användbart för att hantera anpassade villkor, till exempel produktnamn eller förkortningar. Allt du behöver för att komma igång är en uttal-fil – en enkel txt-fil.

Här är hur det fungerar. Du kan ange flera anpassade uttal av poster i en enda txt-fil. Strukturen är följande:

```
Display form <Tab> Spoken form <Newline>
```

Flera exempel visas i följande tabell:

| Visa formulär | Talat formulär |
|----------|-------|
| 3CPO | Se tre högsta användningsnivå o |
| L8R | sent är |
| CNTK | s n t k|

## <a name="requirements-for-the-spoken-form"></a>Krav för talat formuläret

Formuläret talat måste vara gemener, som du kan tvinga under importen. Du måste också ange checkar in importören data. Ingen flik i formuläret talat eller visa formulär tillåts. Men det kan vara mer tillåts inte tecken i Visa formulär (exempelvis ~ och ^).

Varje txt-fil kan ha flera poster, enligt följande bild:

![Exempel på förkortningen uttal](media/stt/custom-speech-pronunciation-file.png)

Formuläret talat är de fonetiska, visa formulär. Den består av bokstäver, ord eller stavelser. Det finns för närvarande inga ytterligare vägledning eller uppsättning standarder för att hjälpa dig att formulera talat formuläret.

## <a name="supported-pronunciation-characters"></a>Stöds uttal tecken

Anpassade uttal är för närvarande stöd för engelska (en-US) och tyska (de-de). Teckenuppsättningar som du kan använda för att uttrycka talat form av en term (i anpassade uttal-fil) visas i följande tabell:

| Språk | Karaktärer |
|---------- |----------|
| Engelska (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, frågor och, r, s, t, u, v, w, x, y, z |
| Tyska (de-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> En term Visa formulär (i en uttal-fil) ska skrivas på samma sätt i en datauppsättning för anpassning av språk.

## <a name="requirements-for-the-display-form"></a>Krav för Visa formulär

Visningsformulär kan vara endast ett anpassat ord, en förkortning eller sammansatta ord som kombinerar befintliga ord.

>[!NOTE]
>Vi rekommenderar inte att du använder den här funktionen att reformulate vanliga ord eller ändra talat formuläret. Det är bättre kontrollera om några ovanliga ord (till exempel förkortningar, teknisk ord eller främmande ord) är felaktigt transribed innan den här funktionen används. Om de kan du lägga till dem till anpassade uttal av filen. I språkmodell, bör du alltid och endast använda Visningsformulär ett ord.

## <a name="requirements-for-the-file-size"></a>Krav för filstorleken
Storleken på txt-fil som innehåller uttal av poster är begränsad till 1 MB (1KB för den kostnadsfria nivån av nycklar). Vanligtvis behöver du inte ladda upp stora mängder data via den här filen. De flesta anpassade uttal filer är sannolikt bara några få kilobyte (KB-artiklar) i storlek. Kodning av txt-fil för alla språk bör vara UTF-8 BOM. ANSI är också tillåtet för på engelska.

## <a name="next-steps"></a>Nästa steg
* Förbättra noggrannheten genom att skapa en [anpassad akustisk modell](how-to-customize-acoustic-models.md).
* Förbättra noggrannheten genom att skapa en [anpassad språkmodell](how-to-customize-language-model.md).
