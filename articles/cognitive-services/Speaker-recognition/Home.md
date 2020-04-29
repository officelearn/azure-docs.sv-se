---
title: Vad är API för talarigenkänning?
titleSuffix: Azure Cognitive Services
description: Identifiering av högtalare och högtalare med API för talarigenkänning i Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73464917"
---
# <a name="speaker-recognition-api---preview"></a>API för talarigenkänning – för hands version

Talarigenkänning API: er är Cloud-baserade API: er som tillhandahåller avancerade AI-algoritmer för att kontrol lera och identifiera högtalare. Talarigenkänning delas upp i två kategorier: högtalar verifiering och högtalar identifiering.

## <a name="speaker-verification"></a>Talarverifiering

Rösten har unika egenskaper som kan kopplas till en enskild person.  Program kan använda Voice som en ytterligare faktor för verifiering i scenarier som anrops Center och webb tjänster.

Talarverifiering-API: er fungerar som ett intelligent verktyg som hjälper dig att kontrol lera att användarna använder sina lösen fraser för röst och tal.

### <a name="enrollment"></a>Registrering

Registreringen för att kontrol lera talare är beroende av text, vilket innebär att högtalare måste välja en unik lösen fras som ska användas under både registrerings-och verifierings faserna.

I fasen för högtalar registrering registreras högtalar rösten som säger en speciell fras. Röst funktioner extraheras för att bilda en unik röst under skrift medan den valda frasen identifieras. Tillsammans skulle den här högtalar registrerings informationen användas för att verifiera högtalaren. Högtalar registrerings data lagras i ett skyddat system. Kunden bestämmer hur lång tid det ska vara. Kunder kan skapa, uppdatera och ta bort registrerings data för enskilda högtalare via API-anrop.  När prenumerationen tas bort, tas även alla högtalar registrerings data som är associerade med prenumerationen bort.

Kunderna bör se till att de har fått rätt behörighet från användarna för att kontrol lera talare.

### <a name="verification"></a>Verifiering

I verifierings fasen bör kunden anropa API-verifieringens API med det ID som är associerat med den person som ska verifieras.  Tjänsten extraherar röst funktioner och lösen frasen från inspelnings tal inspelningen. Sedan jämförs funktionerna med motsvarande element i högtalar registrerings data för den talare som kunden söker och bestämmer vilken matchning som helst.  Svaret returnerar "Accept" eller "avvisa" med olika konfidens nivåer.  Kunden bestämmer sedan hur resultatet ska användas för att avgöra om den här personen är den registrerade talare.

Tröskelns konfidensnivå bör anges baserat på scenariot och andra verifierings faktorer som används. Vi rekommenderar att du experimenterar med nivån konfidensnivå och att du funderar på lämplig inställning för varje program. API: erna är inte avsedda att avgöra om ljudet kommer från en Live-person eller en imiterad eller en inspelning av en registrerad talare.

Tjänsten behåller inte tal inspelningen eller de extraherade röst funktionerna som skickas till tjänsten under verifierings fasen.

Mer information om talarverifiering finns i API:et för [talare – verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Talaridentifiering

Program kan använda röst för att identifiera "vem som talar" med en grupp med registrerade högtalare. Talaridentifiering-API: er kan användas i scenarier som mötes produktivitet, anpassning och svars Center-avskrifter.

### <a name="enrollment"></a>Registrering

Registrering för talaridentifiering är textoberoende, vilket innebär att det inte finns några begränsningar på vad talaren säger i ljudfilen. Ingen lösen fras krävs.

I registrerings fasen registreras högtalar rösten och röst funktioner extraheras för att bilda en unik röst under skrift. Tal ljudet och de extraherade funktionerna lagras i ett skyddat system. Kunden styr hur lång tid det behåller sig. Kunder kan skapa, uppdatera och ta bort dessa högtalar registrerings data för enskilda högtalare via API-anrop. När prenumerationen tas bort, tas även alla högtalar registrerings data som är associerade med prenumerationen bort.

Kunderna bör se till att de har fått rätt behörighet från användarna om högtalar identifiering.

### <a name="identification"></a>Identification

I identifierings fasen extraherar högtalar identifierings tjänsten röst funktioner från inspelnings tal inspelningen. Sedan jämförs funktionerna med registrerings data för den angivna listan med högtalare. När en matchning hittas med en registrerad talare, returnerar svaret ID: t för högtalaren med en konfidensnivå.  Annars returnerar svaret "avvisa" när ingen talare är en matchning av en registrerad talare.

Tröskelns konfidensnivå måste anges baserat på scenariot. Vi rekommenderar att du experimenterar med nivån konfidensnivå och att du funderar på lämplig inställning för varje program. API: erna är inte avsedda att avgöra om ljudet kommer från en Live-person eller en imiterad eller en inspelning av en registrerad talare.

Tjänsten behåller inte tal inspelningen eller de extraherade röst funktionerna som skickas till tjänsten för identifierings fasen.

Mer information om talaridentifiering finns i API:et [Speaker - Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) (Talare – identifiering).
