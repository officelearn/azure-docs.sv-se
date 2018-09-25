---
title: Anpassad tal tjänstöversikt på Azure | Microsoft Docs
description: Custom Speech Service är en molnbaserad tjänst som gör att användarna kan anpassa talmodeller för tal till text avskrift.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948374"
---
# <a name="what-is-custom-speech-service"></a>Vad är Custom Speech Service?

Med Custom Speech Service är en molnbaserad tjänst som ger användarna möjlighet att anpassa talmodeller för tal till Text avskrift.
Om du vill använda Custom Speech Service, som avser den [Custom Speech Service Portal](https://cris.ai).

Custom Speech Service kan du skapa anpassade språkmodeller och akustiska modeller som är skräddarsydda för ditt program och dina användare. Genom att ladda upp din specifika tal och/eller textdata med Custom Speech Service, kan du skapa anpassade modeller som kan användas tillsammans med Microsofts befintliga den senaste talmodeller.

Om du lägger till röst interaktion till en mobiltelefon, surfplatta eller dator app, kan du till exempel skapa en anpassad språkmodell som kan kombineras med Microsofts akustiska modell för att skapa en tal till text-slutpunkt som utformats specifikt för din app. Om ditt program är avsedd för användning i en viss miljö eller genom att en viss användarpopulation, kan du också skapa och distribuera en anpassad akustisk modell med den här tjänsten.


## <a name="how-do-speech-recognition-systems-work"></a>Hur fungerar tal erkännande system?
Tal erkännande system består av flera komponenter som arbetar tillsammans. Två av de viktigaste komponenterna är den akustiska modellen och språkmodellen.

Den akustiska modellen är en klassificerare som märker korta ljudfragment till ett av flera fonem, eller ljudenheter, på ett visst språk. Till exempel består ordet ”tal” av tre fonem – ”s t a l”. Klassificeringarna görs 100 gånger per sekund.

Språkmodellen är en sannolikhetsfördelning över sekvenser av ord. Språkmodellen hjälper systemet att välja bland ordsekvenser som låter lika, baserat på ordsekvensernas sannolikhet. Till exempel låter ”känna igen tal” och ”pekoral” lika, men den första hypotesen är mycket mer sannolik, och tilldelas därför en högre poäng av språkmodellen.

Både den- och språkdata modeller är statistiska modeller som registrerats från träningsdata. Därför fungerar bäst när det tal som de stöta på när de används i program liknar de data som observerats under utbildningen. - Och språkdata modeller i Microsoft tal till Text-motorn har tränats på enorma uppsättning tal och text och ger den senaste prestanda för de vanligaste Användningsscenarier, till exempel använda Cortana på din smart telefon, surfplatta eller dator, söka på webben med tal eller dikterar textmeddelanden till en vän.

## <a name="why-use-the-custom-speech-service"></a>Varför använda Custom Speech Service?
Microsoft tal till Text-motorn är i världsklass, är den riktad mot de scenarier som beskrivs ovan. Om du förväntar dig att röstfrågor ska innehålla vissa vokabulärobjekt, som produktnamn eller jargong som inträffar sällan i vanligt talspråk, är det dock sannolikt att du får bättre prestanda genom att anpassa språkmodellen.

Om du exempelvis skulle bygga en app som ska söka på MSDN med tal är det sannolikt att termer som ”objektorienterad”, ”namnområde” eller ”.net” förekommer oftare än i vanliga röstprogram. Anpassning av språkmodellen gör att systemet kan lära sig det.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Custom Speech Service finns i [Custom Speech Service Portal] (https://cris.ai).

* [Kom igång](cognitive-services-custom-speech-get-started.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
