---
title: Vanliga frågor och svar – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om API för visuellt innehåll i Azure Cognitive Services.
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564601"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Vanliga frågor och svar om API för visuellt innehåll

> [!TIP]
> Om du inte kan hitta svar på dina frågor i dessa vanliga frågor och svar kan du prova att be API för visuellt innehåll community om [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta [Hjälp och support på UserVoice](https://cognitive.uservoice.com/)

---

**Fråga**: *Kan jag träna API för visuellt innehåll att använda anpassade taggar?  Anta till exempel att du vill mata in bilder av Cat-raser till "träna" AI och sedan ta emot ras-värdet i en AI-begäran.*

**Svar**: Den här funktionen är inte tillgänglig för tillfället. Våra tekniker arbetar dock för att få den här funktionen att Visuellt innehåll.

---

**Fråga**: *Kan Visuellt innehåll användas lokalt utan Internet anslutning?*

**Svar**: Vi erbjuder för närvarande inte någon lokal eller lokal lösning.

---

**Fråga**: *Kan Visuellt innehåll användas för att läsa licens plattor?*

**Svar**: Syn-API: et erbjuder en text identifiering med OCR, men den är för närvarande inte optimerad för licens plattor. Vi försöker ständigt förbättra våra tjänster och har lagt till OCR för automatisk licens plåts igenkänning till vår lista över funktions förfrågningar.

---

**Fråga**: *Vilka typer av Skriv ytor stöds för hand SKRIFTS igenkänning?*

**Svar**: Tekniken fungerar med olika typer av ytor, inklusive whiteboardtavlor, white paper och gula Fästisar.

---

**Fråga**: *Hur lång tid tar hand SKRIFTS igenkännings åtgärden?*

**Svar**: Hur lång tid det tar beror på textens längd. För längre texter kan det ta upp till flera sekunder. När åtgärden identifiera handskriven text har slutförts kan du därför behöva vänta innan du kan hämta resultatet med hjälp av åtgärden Hämta handskriven text åtgärd.

---

**Fråga**: *Hur hanterar hand SKRIFTS igenkännings tekniken text som infogades med en cirkumflex mitt i en rad?*

**Svar**: Sådan text returneras som en separat rad vid hand SKRIFTS igenkännings åtgärden.

---

**Fråga**: *Hur hanteras tekniken för hand SKRIFTS igenkänning över ord eller rader?*

**Svar**: Om orden är överkorsade med flera rader för att rendera dem som okända, väljer inte hand SKRIFTS igenkännings åtgärden. Men om orden korsas ut med en enda rad, behandlas korsningen som brus, och orden hämtas fortfarande av hand SKRIFTS igenkännings åtgärden.

---

**Fråga**: *Vilka text orienteringar stöds för tekniken för hand SKRIFTS igenkänning?*

**Svar**: Text som är orienterad i vinklar på upp till cirka 30 grader till 40 grader kan hämtas av hand SKRIFTS igenkännings åtgärden.

---
