---
title: Vad är Custom Speech Service?
titlesuffix: Azure Cognitive Services
description: Custom Speech Service är en molnbaserad tjänst där användarna kan anpassa talmodeller för tal till text-transkriptioner.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: overview
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: f90fc40a42806cfb002da2d9943eaa41736df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222751"
---
# <a name="what-is-custom-speech-service"></a>Vad är Custom Speech Service?

Custom Speech Service är en molnbaserad tjänst där användarna kan anpassa talmodeller för tal till text-transkriptioner.
Information om hur du använder Custom Speech Service finns på [Custom Speech Service-portalen](https://cris.ai).

Med Custom Speech Service kan du skapa anpassade språkmodeller och akustiska modeller skräddarsydda för din app och dina användare. Genom att ladda upp specifika tal- och/eller textdata till Custom Speech Service kan du skapa anpassade modeller som kan användas tillsammans med Microsofts befintliga avancerade talmodeller.

Om du till exempel lägger till röstinteraktion i en app mobiltelefon, surfplatta eller dator kan du skapa en anpassad språkmodell som kan kombineras med Microsofts akustiska modell för att skapa en tal till text-slutpunkt specifikt utformad för din app. Om appen har utformats för en viss miljö eller en viss användargrupp kan du också skapa och distribuera en akustisk modell med den här tjänsten.


## <a name="how-do-speech-recognition-systems-work"></a>Hur fungerar taligenkänningssystem?
Taligenkänningssystem består av flera komponenter som arbetar tillsammans. Två av de viktigaste komponenterna är den akustiska modellen och språkmodellen.

Den akustiska modellen är en klassificerare som märker korta ljudfragment till ett av ett antal fonem, eller ljudenheter, på ett angivet språk. Ordet ”tal” består till exempel av tre fonem – ”t a l”. Klassificeringarna görs 100 gånger per sekund.

Språkmodellen är en sannolikhetsfördelning över sekvenser av ord. Språkmodellen hjälper systemet att välja bland ordsekvenser som låter lika, baserat på ordsekvensernas sannolikhet. Till exempel låter ”känna igen tal” och ”pekoral” lika, men den första hypotesen är mycket mer sannolik, och tilldelas därför en högre poäng av språkmodellen.

Både den akustiska modellen och språkmodellen är statistiska modeller som lärs in från träningsdata. Därför fungerar de bäst när talet som modellen ska hantera när den används i appar liknar de data som observerats under träningen. De akustiska modellerna och språkmodellerna i Microsofts tal till text-motor har tränats med en enorm samling tal och text och har avancerade prestanda för de flesta användningsscenarierna, till exempel interaktion med Cortana på smartphone, surfplatta eller dator, med sökningar på webben efter röst eller diktering av textmeddelanden till en vän.

## <a name="why-use-the-custom-speech-service"></a>Varför använda Custom Speech Service?
Microsofts tal till text-motor är i världsklass men är inriktad på sådana scenarier som beskrivs ovan. Om du förväntar dig att röstfrågor ska innehålla vissa vokabulärobjekt, som produktnamn eller en jargong som förekommer väldigt sällan i vanligt talspråk, är det sannolikt att du får bättre prestanda om du anpassar språkmodellen.

Om du exempelvis skulle bygga en app som ska söka på MSDN med tal är det sannolikt att termer som ”objektorienterad”, ”namnrymd” eller ”.net” förekommer oftare än i vanliga röstprogram. Anpassning av språkmodellen gör att systemet kan lära sig det.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Custom Speech Service finns i [Custom Speech Service-portalen] (https://cris.ai).

* [Kom igång](cognitive-services-custom-speech-get-started.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
