---
title: Om ett tal till Text | Microsoft Docs
description: 'En översikt över funktionerna i tal Text-API: et.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355689"
---
# <a name="about-the-speech-to-text-api"></a>Om ett tal till Text API

Den **tal till Text** API *transcribes* ljudströmmar till text som programmet kan visa för användaren eller vidta åtgärder som kommandot indata. API: er kan användas med en SDK-klientbibliotek (för plattformar som stöds och språk) eller ett REST-API.

Den **tal till Text** API innehåller följande funktioner:

- Avancerad speech recognition teknik från Microsoft, samma som används av Cortana, Office och andra Microsoft-produkter.

- Realtid kontinuerlig Igenkännande. **Tal till Text** tillåter användare att transkribera ljud till text i realtid. Det stöder också ta emot mellanresultat ord som hittills har tolkats. Tjänsten känner automatiskt i slutet av tal. Användare kan också välja ytterligare formateringsalternativ, inklusive versaler och skiljetecken, svordomar maskering och text normalisering.

- Optimerad **tal till Text** resultat för interaktiv konversationen och dictation scenarier. 

- Stöd för många talade språk i flera dialekter. En fullständig lista över språk som stöds i varje recognition läge finns [språk som stöds](supported-languages.md#speech-to-text).

- Anpassad språk och acoustic modeller, så kan du anpassa ditt program till användarnas möjlighet att tala, tala miljö och särskilda ordförråd.

- Så här fungerar med naturligt språk. Genom integrering med [språk förstå](https://docs.microsoft.com/azure/cognitive-services/luis/) (THOMAS) du kan härleda avsikter och entiteter från tal. Användarna behöver inte ha någon appens ordförråd, men kan beskriver vad de vill ha med egna ord.

## <a name="api-capabilities"></a>API-funktioner

Vissa funktioner i den **tal till Text** API är inte tillgängliga via REST. I följande tabell sammanfattas behörigheterna för varje metod för åtkomst till API: et.

| Användningsfall | REST | SDK:er |
|-----|-----|-----|----|
| Transkribera en kort utterance, till exempel ett kommando (längd < 15 s). Inga mellanliggande resultat | Ja | Ja |
| Transkribera ett längre utterance (> 15 s) | Nej | Ja |
| Transkribera strömmande ljud med valfritt mellanliggande resultat | Nej | Ja |
| Förstå talare avsikter via THOMAS | Nej\* | Ja |

\* *THOMAS avsikter och entiteter kan erhållas med hjälp av en separat THOMAS-prenumeration. Med den här prenumerationen SDK kräver THOMAS och ange entiteten och avsikt resultat samt transcriptions tal. Med REST-API kan du anropa THOMAS själv för att härleda avsikter och entiteter med prenumerationen THOMAS.*

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)
