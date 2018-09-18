---
title: Frågor och svar om API för visuellt
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor och svar om API för visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c0a4e981a290b9a758c8401a75e546c61618b45
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983909"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Vanliga och frågor svar om API för visuellt

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga API för visuellt innehåll community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta [hjälp och Support på UserVoice](https://cognitive.uservoice.com/)

-----

**Fråga**: *kan jag träna API för visuellt innehåll att använda anpassade taggar?  Jag skulle vilja feed i bilder av kattraser att träna AI och sedan ta emot RAS-värdet på en begäran om AI.*

**Svar**: den här funktionen är för närvarande inte tillgänglig. Dock arbetar våra tekniker för att göra den här funktionen att visuellt.

-----

**Fråga**: *visuellt innehåll kan användas lokalt utan Internetanslutning?*

**Svar**: för närvarande erbjuder vi inte en lokal eller lokal lösning.

-----

**Fråga**: *vilka språk som stöds med visuellt innehåll?*

**Svar**: språk som stöds är:

| | | Språk som stöds | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Danska (da-DK)  | Nederländska (nl-NL)     | Svenska           | Finska (fi-FI)            |Franska (fr-FR)
| Tyska (de-DE)  | Grekiska (el-GR)     | Ungerska (hu-HU) | Italienska (it-IT)            | Japanska (ja-JP)
| Koreanska (ko-KR)  | Norska (nb-NO) | Polska (pl-PL)    | Portugisiska (pt-BR) (pt-PT) | Ryska (ru-RU)
| Spanska (es-ES)   | Svenska (SA-SV)     | Turkiska (tr Dataflödesenheter)   |                            |

-----

**Fråga**: *visuellt innehåll kan användas för att läsa licens nivåer?*

**Svar**: The API för visuellt innehåll ger bra text-identifiering med OCR, men det inte är för tillfället optimerat för licens-nivåer. Vi försöker hela tiden förbättra våra tjänster och har lagt till OCR för automatisk licens lj erkännande till vår lista för framtida versioner.

-----

**Fråga:** *vilka språk som stöds för handskriftsigenkänning?*

**Svar**: för närvarande stöds endast på engelska.

-----

**Fråga**: *vilka typer av skriva Surface-enheter som stöds för handskriftsigenkänning?*

**Svar**: tekniken fungerar med olika typer av Surface-enheter, inklusive whiteboardtavlor, faktablad och gula anteckningslappar.

-----

**Fråga**: *hur lång tid åtgärden igenkänning av handskrift tar?*

**Svar**: hur lång tid det tar beror på längden på texten. För längre texter kan det ta upp till några sekunder. När identifiera handskriven Text-åtgärden är klar kan du därför behöva vänta innan du kan hämta resultat med åtgärden hämta handskriven Text resultatet för åtgärden.

-----

**Fråga**: *hur fungerar handskrift igenkänning av teknik referensen text som har infogats med en hatt mitt i en rad?*

**Svar**: sådan text returneras som en separat rad av åtgärden för igenkänning av handskrift.

-----

**Fråga**: *hur taligenkänningsteknik handskrift hanterar överskred ut ord eller rader?*

**Svar**: om orden passeras med flera rader att återge dem okänt, åtgärden igenkänning av handskrift inte hämta dem. Men om orden passeras med hjälp av en enda rad, att överbrygga behandlas som bruset och orden fortfarande få slutpunktsstatus uppfattas av åtgärden för igenkänning av handskrift.

-----

**Fråga**: *vilka text skärmorienteringar stöds för taligenkänningsteknik handskrift?*

**Svar**: Text orienterad i vinkel av upp till cirka 30 grader mot 40 grader kan hämta slutpunktsstatus uppfattas av åtgärden för igenkänning av handskrift.

-----
