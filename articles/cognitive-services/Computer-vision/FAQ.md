---
title: Vanliga frågor och svar - visuellt innehåll
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor och svar om API för visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 2e04d86e7067db98c8399cfc3e202457b8f253d6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351957"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Vanliga och frågor svar om API för visuellt

> [!TIP]
> Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga API för visuellt innehåll community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta [hjälp och Support på UserVoice](https://cognitive.uservoice.com/)

-----

**Fråga**: *Kan jag träna API för visuellt innehåll att använda anpassade taggar?  Jag skulle vilja feed i bilder av kattraser att träna AI och sedan ta emot RAS-värdet på en begäran om AI.*

**Svar**: Den här funktionen är för närvarande inte tillgänglig. Dock arbetar våra tekniker för att göra den här funktionen att visuellt.

-----

**Fråga**: *Visuellt användas lokalt utan Internetanslutning?*

**Svar**: För närvarande erbjuder vi inte en lokal eller lokal lösning.

-----

**Fråga**: *Vilka språk som stöds med visuellt innehåll?*

**Svar**: Språk som stöds:

| | | Språk som stöds | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Danska (da-DK)  | Nederländska (nl-NL)     | Svenska           | Finska (fi-FI)            |Franska (fr-FR)
| Tyska (de-DE)  | Grekiska (el-GR)     | Ungerska (hu-HU) | Italienska (it-IT)            | Japanese (ja-JP)
| Koreanska (ko-KR)  | Norska (nb-NO) | Polska (pl-PL)    | Portugisiska (pt-BR) (pt-PT) | Ryska (ru-RU)
| Spanska (es-ES)   | Svenska (SA-SV)     | Turkiska (tr-TR)   |                            |

-----

**Fråga**: *Kan användas för visuellt innehåll att läsa licens nivåer?*

**Svar**: API för visuellt innehåll ger bra text-identifiering med OCR, men det inte är för tillfället optimerat för licens-nivåer. Vi försöker hela tiden förbättra våra tjänster och har lagt till OCR för automatisk licens lj erkännande till vår lista för framtida versioner.

-----

**Fråga**: *Vilka språk som stöds för handskriftsigenkänning?*

**Svar**: För närvarande stöds endast på engelska.

-----

**Fråga**: *Vilka typer av skriva ytor stöds för handskriftsigenkänning?*

**Svar**: Tekniken fungerar med olika typer av Surface-enheter, inklusive whiteboardtavlor, faktablad och gula anteckningslappar.

-----

**Fråga**: *Hur lång tid tar åtgärden igenkänning av handskrift?*

**Svar**: Hur lång tid det tar beror på längden på texten. För längre texter kan det ta upp till några sekunder. När identifiera handskriven Text-åtgärden är klar kan du därför behöva vänta innan du kan hämta resultat med åtgärden hämta handskriven Text resultatet för åtgärden.

-----

**Fråga**: *Hur hanterar taligenkänningsteknik handskrift text som har infogats med en hatt mitt i en rad?*

**Svar**: Sådan text returneras som en separat rad av åtgärden för igenkänning av handskrift.

-----

**Fråga**: *Hur hanterar taligenkänningsteknik handskrift överskred ut ord eller rader?*

**Svar**: Om orden passeras med flera rader att återge dem okänt, över åtgärden igenkänning av handskrift inte dem. Men om orden passeras med hjälp av en enda rad, att överbrygga behandlas som bruset och orden fortfarande få slutpunktsstatus uppfattas av åtgärden för igenkänning av handskrift.

-----

**Fråga**: *Vilka text skärmorienteringar stöds för taligenkänningsteknik handskrift?*

**Svar**: Text orienterad i vinkel av upp till cirka 30 grader mot 40 grader kan hämta slutpunktsstatus uppfattas av åtgärden för igenkänning av handskrift.

-----
