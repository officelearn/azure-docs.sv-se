---
title: Vanliga frågor - Datorseende
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om API:et för datorseende i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564601"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>API för datorseende vanliga frågor

> [!TIP]
> Om du inte hittar svar på dina frågor i den här vanliga frågor och svar kan du prova att fråga API-communityn för visuellt innehåll på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta [hjälp och support på UserVoice](https://cognitive.uservoice.com/)

---

**Fråga:** *Kan jag träna Computer Vision API att använda anpassade taggar?  Till exempel skulle jag vilja mata in bilder av kattraser för att "träna" AI, sedan få rasen värde på en AI-begäran.*

**Svar**: Den här funktionen är inte tillgänglig för närvarande. Våra ingenjörer arbetar dock för att få denna funktion till Computer Vision.

---

**Fråga:** *Kan Computer Vision användas lokalt utan internetuppkoppling?*

**Svar**: Vi erbjuder för närvarande ingen lokal eller lokal lösning.

---

**Fråga:** *Kan Computer Vision användas för att läsa registreringsskyltar?*

**Svar**: Vision API erbjuder bra textdetektering med OCR, men det är för närvarande inte optimerat för registreringsskyltar. Vi försöker ständigt förbättra våra tjänster och har lagt till OCR för automatisk registreringsskylt erkännande till vår lista över funktionsförfrågningar.

---

**Fråga**: *Vilka typer av skrivytor stöds för handskriftsigenkänning?*

**Svar**: Tekniken fungerar med olika typer av ytor, inklusive whiteboards, vitt papper och gula klisterlappar.

---

**Fråga**: *Hur lång tid tar handskriftsigenkänningen?*

**Svar**: Hur lång tid det tar beror på textens längd. För längre texter kan det ta upp till flera sekunder. När åtgärden Identifiera handskriven text har slutförts kan du därför behöva vänta innan du kan hämta resultaten med hjälp av åtgärden Hämta handskriven text.

---

**Fråga:** *Hur hanterar handskriftsigenkänningstekniken text som infogades med en smekt mitt i en rad?*

**Svar**: Sådan text returneras som en separat rad av handskriftsigenkänningen.

---

**Fråga**: *Hur hanterar handskriftsigenkänningstekniken överstrukna ord eller linjer?*

**Svar**: Om orden stryks bort med flera rader för att göra dem oigenkännliga, plockas inte upp dem i handskriftsigenkänningsåtgärden. Men om orden stryks över med en enda linje behandlas den korsningen som brus, och orden plockas fortfarande upp av handskriftsigenkänningen.

---

**Fråga**: *Vilka textorienteringar stöds för handskriftsigenkänningstekniken?*

**Svar**: Textorienterad i vinklar på upp till cirka 30 grader till 40 grader kan plockas upp av handskriftsigenkänningen.

---
