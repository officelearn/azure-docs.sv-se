---
title: Anpassade tal översikt över i Azure | Microsoft Docs
description: Anpassad tal tjänsten är en molnbaserad tjänst som gör att användare kan anpassa tal modeller för tal-till-text skrivfel.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352902"
---
# <a name="what-is-custom-speech-service"></a>Vad är anpassade tal Service?

Anpassade tal-tjänsten är en molnbaserad tjänst som ger användarna möjlighet att anpassa tal modeller för tal-till-Text skrivfel.
Om du vill använda anpassade tal tjänsten, referera till den [anpassade tal Tjänstportalen](https://cris.ai).

Tjänsten anpassad tal kan du skapa anpassade språk modeller och ljud är skräddarsydda för ditt program och dina användare. Du kan skapa anpassade modeller som kan användas tillsammans med Microsofts befintliga den senaste tal modeller genom att ladda upp din specifika tal och/eller textdata till tjänsten anpassade tal.

Om du vill lägga till röst interaktion till en mobiltelefon, surfplatta eller dator app, kan du till exempel skapa en anpassad språkmodell som kan kombineras med Microsofts ljud modellen för att skapa ett tal till text-slutpunkt som är särskilt utformat för din app. Om programmet har utformats för användning i en viss miljö eller av en viss användare population du också skapa och distribuera en anpassad ljud modell med den här tjänsten.


## <a name="how-do-speech-recognition-systems-work"></a>Hur fungerar speech recognition system?
Tal recognition system består av flera komponenter som arbetar tillsammans. Två av de viktigaste komponenterna är ljud modellen och språkmodell.

Ljud modellen är en klassificerare etikett kort fragment av ljud till någon av ett antal fonemen eller ljud enheter i ett visst språk. Till exempel består word ”tal” av fyra fonemen ”s p iy ch”. Klassificeringarna görs 100 gånger per sekund.

Språkmodellen är en sannolikhetsfördelning över sekvenser av ord. Språkmodellen hjälper systemet att välja bland ordsekvenser som låter lika, baserat på ordsekvensernas sannolikhet. Till exempel låter ”känna igen tal” och ”pekoral” lika, men den första hypotesen är mycket mer sannolik, och tilldelas därför en högre poäng av språkmodellen.

Både acoustic och språk-modeller är statistiska modeller som registrerats från träningsdata. Därför kan utföra de bäst när tal som de ställs inför när de används i program som liknar de data som observerats vid träning. Acoustic och språk-modeller i Microsoft tal till Text-motorn har tränats på enorma mängd tal och text och ger den senaste prestanda för de flesta vanliga Användningsscenarier som interagerar med Cortana på smartkortet telefon, surfplatta eller dator söker på webben efter röst eller dikterar textmeddelanden till en vän.

## <a name="why-use-the-custom-speech-service"></a>Varför använda anpassade tal tjänsten?
Även om Microsoft tal till Text-motorn är världsklass, är det riktat mot de scenarier som beskrivs ovan. Om du förväntar dig röst frågor i programmet för att innehålla viss ordförråd objekt, till exempel produktnamn eller språket som sällan inträffar i vanliga tal, är det dock troligt att kunna få bättre prestanda genom att anpassa språk modellen.

Om du exempelvis skulle bygga en app som ska söka på MSDN med tal är det sannolikt att termer som ”objektorienterad”, ”namnområde” eller ”.net” förekommer oftare än i vanliga röstprogram. Anpassning av språkmodellen gör att systemet kan lära sig det.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder tjänsten anpassad tal finns [anpassad tal Tjänstportalen] (https://cris.ai).

* [Kom igång](cognitive-services-custom-speech-get-started.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
