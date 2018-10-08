---
title: Vad är talarigenkänning?
titlesuffix: Azure Cognitive Services
description: Använd avancerade algoritmer för talarverifiering och talaridentifiering med API för talarigenkänning.
services: cognitive-services
author: dwlin
manager: cgronlun
ms.service: cognitive-services
ms.component: speaker-recognition
ms.topic: overview
ms.date: 03/20/2016
ms.author: dwlin
ms.openlocfilehash: 13a95aff8b2b0d5dad0574e6107958a20576702a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227341"
---
# <a name="speaker-recognition-api"></a>Talarigenkännings-API

Välkommen till API:erna för talarigenkänning. API:erna för talarigenkänning är molnbaserade API:er som ger de mest avancerade algoritmerna för talarverifiering och talaridentifiering. API för talarigenkänning kan delas in i två kategorier: talarverifiering och talaridentifiering.


## <a name="speaker-verification"></a>Talarverifiering


Människans röst har unika egenskaper som kan användas för att identifiera en person, precis som ett fingeravtryck.  Användningen av rösten som en signal för åtkomstkontroll och autentiseringsscenarier har blivit ett nytt, innovativt verktyg som uppgraderar säkerheten med enklare autentisering för kunderna.

API:er för talarverifiering kan automatiskt verifiera och autentisera användare med hjälp av deras röst eller tal.

### <a name="enrollment"></a>Registrering

Registrering för talarverifiering är textberoende, vilket innebär att talare måste välja en specifik lösenfras som ska användas under både registrerings- och verifieringsfaserna. 

Under registreringen spelas talarens röst in med en specifik fras. Sedan extraheras ett antal egenskaper, och den valda frasen identifieras. Tillsammans utgör både de extraherade egenskaperna och den valda frasen en unik röstsignatur.

### <a name="verification"></a>Verifiering
###
Under verifieringen jämförs en inmatningsröst och -fras med registreringens röstsignatur och fras så att det kan verifieras huruvida de kommer från samma person och om frasen är korrekt.

Mer information om talarverifiering finns i API:et för [talare – verifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Talaridentifiering

API:er för talaridentifiering kan automatiskt identifiera den person som talar i en ljudfil givet en grupp med presumtiva talare. Det inmatade ljudet paras mot den angivna gruppen med talare och om det görs en matchning returneras talarens identitet.

Alla talare bör först genomgå en registreringsprocess så att deras röster registreras i systemet och det skapas ett röstavtryck.


### <a name="enrollment"></a>Registrering

Registrering för talaridentifiering är textoberoende, vilket innebär att det inte finns några begränsningar på vad talaren säger i ljudfilen. Talarens röst registreras och ett antal egenskaper extraheras för att skapa en unik röstsignatur. 


### <a name="recognition"></a>Igenkänning

Ljudet från den okända talaren tillhandahålls tillsammans med gruppen med presumtiva talare under igenkänningen. Den inmatade rösten jämförs med alla talarna i syfte att bestämma vems röst den är, och om det sker en matchning returneras talarens identitet.


Mer information om talaridentifiering finns i API:et för [talare – identifiering](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
