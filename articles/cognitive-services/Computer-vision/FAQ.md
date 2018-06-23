---
title: Vanliga frågor och svar för datorn visionen API | Microsoft Docs
description: Få svar på vanliga frågor och svar om datorn Vision API i kognitiva Microsoft-tjänster.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351543"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Vanliga och frågor svar om datorn Vision API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på frågor här, kan du pröva att besvara datorn Vision API-gemenskapen på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta [hjälp och Support på UserVoice](https://cognitive.uservoice.com/)

-----

**Fråga**: *kan jag lära datorn Vision API för att använda anpassade taggar?  Jag skulle vilja mata in bilder för kattraser träna AI och sedan ta emot RAS-värdet i en AI-begäran.*

**Svaret**: den här funktionen är inte tillgängligt. Dock arbetar våra tekniker för att få den här funktionen till datorn Vision.

-----

**Fråga**: *kan datorn Vision användas lokalt utan Internetanslutning?*

**Svaret**: vi för närvarande inte ger en lokal eller en lokal lösning.

-----

**Fråga**: *vilka språk som stöds med datorn Vision?*

**Svaret**: språk som stöds:

| | | Språk som stöds | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Danska (da-DK)  | Nederländska (nl-NL)     | Svenska           | Finska (fi-FI)            |Franska (fr-FR)
| Tyska (de-DE)  | Grekiska (el-GR)     | Ungerska (hu-HU) | Italienska (it-IT)            | Japanska (ja-JP)
| Koreanska (ko-KR)  | Norska (nb-NO) | Polska (pl-PL)    | Portugisiska (pt-BR) (pt-PT) | Ryska (ru-RU)
| Spanska (es-ES)   | Svenska (sv SV)     | Turkiska (tr TU)   |                            |

-----

**Fråga**: *kan datorn Vision användas för att läsa licens nivåer?*

**Svaret**: Vision API: N ger bra text identifiering med OCR, men det är inte för närvarande är optimerad för licens nivåer. Vi försöker ständigt förbättra våra tjänster och lagt till OCR för automatisk licens skylt recognition till vår lista över funktioner som efterfrågas.

-----

**Fråga:** *vilka språk som stöds för handskriftsigenkänning?*

**Svaret**: för närvarande stöds endast på engelska.

-----

**Fråga**: *vilka typer av skriva hämtar stöds för handskriftsigenkänning?*

**Svaret**: tekniken som fungerar med olika typer av, inklusive whiteboardtavlor, faktablad och gul anteckningar.

-----

**Fråga**: *hur lång tid åtgärden handskriftsigenkänning recognition tar?*

**Svaret**: hur lång tid det tar beror på längden på texten. För längre texter, kan det ta upp till flera sekunder. Efter att identifiera handskriven Text slutförts, kan du därför behöva vänta innan du kan hämta resultat med åtgärden hämta handskriven Text resultat.

-----

**Fråga**: *hur fungerar handskriftsigenkänning recognition teknik referensen text som har infogats med en hatt mitt i en rad?*

**Svaret**: sådan text returneras som en separat rad av handskriftsigenkänning recognition igen.

-----

**Fråga**: *hur handskriftsigenkänning recognition tekniken hanterar korsade out ord eller rader?*

**Svaret**: om orden passeras med flera rader att återge dem inte kan tolkas, handskriftsigenkänning recognition åtgärden hämtar inte dem. Om ord passeras med hjälp av en enda rad, den korsande behandlas som brus och orden fortfarande få tas upp av handskriftsigenkänning recognition igen.

-----

**Fråga**: *vilken text riktlinjer stöds för handskriftsigenkänning recognition tekniken?*

**Svaret**: Text orienterad vinklar av upp till cirka 30 grader till 40 grader hämta hämtas av handskriftsigenkänning recognition igen.

-----
