---
title: Vad är Live Video Analytics på IoT Edge – Azure
description: Det här avsnittet innehåller en översikt över video analys på IoT Edge. Plattformen erbjuder funktioner som du kan använda för att förbättra IoT-lösningarna. Du kan till exempel avbilda, spela in, analysera direktsänd video och publicera resultaten (video och/eller video analys) till Azure-tjänster.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: 81a67322d0a5e524d75b9bf3e481be7157e09e63
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266805"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Vad är Live Video Analytics på IoT Edge? (förhandsversion)

Med Live Video Analytics på IoT Edge får du en plattform för att bygga intelligenta video program som omfattar gränsen och molnet. Plattformen ger möjlighet att samla in, spela in och analysera direktsänd video tillsammans med att publicera resultaten (video och/eller video analys) till Azure-tjänster (i molnet och/eller på gränsen). Plattformen kan användas för att förbättra IoT-lösningar med video analys. Live video analys på IoT Edge funktioner kan kombineras med andra Azure IoT Edge moduler som Stream Analytics på IoT Edge, Cognitive Services på IoT Edge och Azure-tjänster i molnet, till exempel Media Services, Event Hub, Cognitive Services osv. för att bygga kraftfulla hybrid program (till exempel Edge + Cloud).

Live video analys på IoT Edge är utformad för att vara en utöknings bar plattform, så att du kan ansluta olika moduler för video analys (till exempel kognitiva tjänst behållare, anpassade Edge-moduler som har skapats med hjälp av maskin inlärnings modeller med öppen källkod eller anpassade modeller som har tränats med dina egna data) och använda dem för att analysera direktsänd video utan att behöva bekymra dig om komplexiteten

## <a name="accelerate-iot-solutions-development"></a>Påskynda utvecklingen av IoT-lösningar 

IoT-lösningar som kombinerar video analys med signaler från andra IoT-sensorer och/eller affärs data kan hjälpa dig att automatisera eller under lätta affärs beslut, vilket ger produktivitets förbättringar. Med live video analys på IoT Edge kan du snabbt bygga sådana lösningar. Du kan fokusera på att skapa video analys moduler och logik som är specifika för din verksamhet och att låta plattformen dölja de komplicerade hanterings-och körnings video pipelinen.

Med live video analys på IoT Edge kan du fortsätta att använda dina [CCTV-kameror](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) med dina befintliga [video hanterings system (VM)](https://en.wikipedia.org/wiki/Video_management_system) och bygga video analys appar oberoende av varandra. Live video analys på IoT Edge kan användas tillsammans med SDK: er och verktyg för visuellt innehåll för att bygga nyskapande IoT-lösningar. Diagrammet nedan illustrerar detta.

![Utveckla IoT-lösningar med real tids video analys på IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Miljöer som stöds

Linux AMD64-och x64-miljöer stöds.

## <a name="get-started"></a>Kom igång

Läs följande begrepps artiklar och försök sedan med en snabb start för att köra rörelse identifiering på en Live-videofeed.

### <a name="concepts"></a>Begrepp

* [Medie diagram](media-graph-concept.md)
* [Video inspelning](video-recording-concept.md)
* [Videouppspelning](video-playback-concept.md)
* [Kontinuerlig inspelning av video](continuous-video-recording-concept.md)
* [Händelse-baserad videoinspelning](event-based-video-recording-concept.md)
* [Video analys utan videoinspelning](analyze-live-video-concept.md)

## <a name="next-steps"></a>Nästa steg

* Följ [snabb starten: kör live video analys med din egen modell](use-your-model-quickstart.md) artikel för att se hur du kan köra rörelse identifiering i en Live-videofeed.
* Granska [terminologi](terminology.md)
* Utforska [material med öppen källkod i Real video analys](https://github.com/Azure/live-video-analytics)

