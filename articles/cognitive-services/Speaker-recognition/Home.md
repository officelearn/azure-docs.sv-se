---
title: Vad är API för talarigenkänning?
titleSuffix: Azure Cognitive Services
description: Språkaltaleverifiering och högtalaridentifiering med API:et för högtalarigenkänning i Cognitive Services.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464917"
---
# <a name="speaker-recognition-api---preview"></a>API för högtalareigenkänning – förhandsgranskning

Api:er för stödankänning av högtalare är molnbaserade API:er som tillhandahåller avancerade AI-algoritmer för högtalarverifiering och högtalaridentifiering. Speaker Erkännande är indelad i två kategorier: högtalare verifiering och högtalare identifiering.

## <a name="speaker-verification"></a>Talarverifiering

Röst har unika egenskaper som kan associeras med en individ.  Program kan använda röst som en ytterligare faktor för verifiering, i scenarier som callcenter och webbtjänster.

Talareverifierings-API:er fungerar som ett intelligent verktyg för att verifiera användare som använder både röst- och tallösfraser.

### <a name="enrollment"></a>Registrering

Registrering för högtalarverifiering är textberoende, vilket innebär att högtalarna måste välja en specifik lösenfras som ska användas under både registrerings- och verifieringsfaser.

I högtalarregistreringsfasen spelas talarens röst in och säger en specifik fras. Röstfunktioner extraheras för att bilda en unik röstsignatur medan den valda frasen känns igen. Tillsammans skulle den här talarregistreringsdata användas för att verifiera högtalaren. Högtalarregistreringsdata lagras i ett säkert system. Kunden styr hur länge den ska behållas. Kunder kan skapa, uppdatera och ta bort registreringsdata för enskilda högtalare via API-anrop.  När prenumerationen tas bort tas även alla uppgifter om talarregistrering som är associerade med prenumerationen bort.

Kunder bör se till att de har fått rätt behörighet från användarna för talarverifiering.

### <a name="verification"></a>Verifiering

I verifieringsfasen ska kunden anropa informationsverifierings-API:et med det ID som är associerat med den person som ska verifieras.  Tjänsten extraherar röstfunktioner och lösenfrasen från indatatalinspelningen. Sedan jämförs funktionerna med motsvarande element i talarregistreringsdata för högtalaren som kunden försöker verifiera och bestämmer eventuella matchningar.  Svaret returnerar "acceptera" eller "avvisa" med olika konfidensnivåer.  Kunden bestämmer sedan hur resultaten ska användas för att avgöra om den här personen är den registrerade talaren.

Tröskelvärdet för konfidensnivå bör fastställas på grundval av scenariot och andra kontrollfaktorer som används. Vi rekommenderar att du experimenterar med konfidensnivån och överväger lämplig inställning för varje program. API:erna är inte avsedda att avgöra om ljudet kommer från en levande person eller en imitation eller en inspelning av en inskriven högtalare.

Tjänsten behåller inte talinspelningen eller de extraherade röstfunktionerna som skickas till tjänsten under verifieringsfasen.

Mer information om talarverifiering finns i API:et för [talare – verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Talaridentifiering

Program kan använda röst för att identifiera "vem som talar" med tanke på en grupp inskrivna högtalare. Api:er för högtalaridentifiering kan användas i scenarier som mötesproduktivitet, anpassning och transkription av callcenter.

### <a name="enrollment"></a>Registrering

Registrering för talaridentifiering är textoberoende, vilket innebär att det inte finns några begränsningar på vad talaren säger i ljudfilen. Inga lösenfras krävs.

I registreringsfasen spelas högtalarens röst in och röstfunktioner extraheras för att bilda en unik röstsignatur. Talljudet och funktionerna som extraheras lagras i ett säkert system. Kunden styr hur länge den behålls. Kunder kan skapa, uppdatera och ta bort dessa högtalarregistreringsdata för enskilda högtalare via API-anrop. När prenumerationen tas bort tas även alla uppgifter om talarregistrering som är associerade med prenumerationen bort.

Kunderna bör se till att de har fått rätt behörighet från användarna för högtalaridentifiering.

### <a name="identification"></a>Identification

I identifieringsfasen extraherar högtalaridentifieringstjänsten röstfunktioner från indatatalinspelningen. Sedan jämförs funktionerna med registreringsdata för den angivna listan med talare. När en matchning hittas med en registrerad högtalare returnerar svaret högtalarens ID med en konfidensnivå.  Annars returnerar svaret "avvisa" när ingen högtalare matchar en registrerad högtalare.

Tröskelvärdet för konfidensnivå bör fastställas baserat på scenariot. Vi rekommenderar att du experimenterar med konfidensnivån och överväger lämplig inställning för varje program. API:erna är inte avsedda att avgöra om ljudet kommer från en levande person eller en imitation eller en inspelning av en inskriven högtalare.

Tjänsten behåller inte talinspelningen eller de extraherade röstfunktionerna som skickas till tjänsten för identifieringsfasen.

Mer information om talaridentifiering finns i API:et [Speaker - Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) (Talare – identifiering).
