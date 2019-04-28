---
title: Vad är API för talarigenkänning?
titleSuffix: Azure Cognitive Services
description: Använd avancerade algoritmer för talarverifiering och talaridentifiering med API för talarigenkänning i Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: dwlin
ms.openlocfilehash: 86162b4d4e752bc11c0d739f6e343c7a357ea0af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059937"
---
# <a name="speaker-recognition-api"></a>Talarigenkännings-API

Välkommen till API:er för talarigenkänning i Cognitive Services. API:erna för talarigenkänning är molnbaserade API:er som ger de mest avancerade algoritmerna för talarverifiering och talaridentifiering. API för talarigenkänning kan delas in i två kategorier: talarverifiering och talaridentifiering.


## <a name="speaker-verification"></a>Talarverifiering

Människans röst har unika egenskaper som kan användas för att identifiera en person, precis som ett fingeravtryck.  Användningen av rösten som en signal för åtkomstkontroll och autentiseringsscenarier har blivit ett nytt, innovativt verktyg som uppgraderar säkerheten med enklare autentisering för kunderna.

API:er för talarverifiering kan automatiskt verifiera och autentisera användare med hjälp av deras röst eller tal.

### <a name="enrollment"></a>Registrering

Registrering för talarverifiering är textberoende, vilket innebär att talare måste välja en specifik lösenfras som ska användas under både registrerings- och verifieringsfaserna.

Under registreringen spelas talarens röst in med en specifik fras. Sedan extraheras ett antal egenskaper, och den valda frasen identifieras. Tillsammans utgör både de extraherade egenskaperna och den valda frasen en unik röstsignatur.

### <a name="verification"></a>Verifiering

Under verifieringen jämförs en inmatningsröst och -fras med registreringens röstsignatur och fras så att det kan verifieras huruvida de kommer från samma person och om frasen är korrekt.

Mer information om talarverifiering finns i API:et [Speaker - Verification](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652) (Talare – verifiering).

## <a name="speaker-identification"></a>Talaridentifiering

API:er för talaridentifiering kan automatiskt identifiera den person som talar i en ljudfil givet en grupp med presumtiva talare. Det inmatade ljudet paras mot den angivna gruppen med talare och om det görs en matchning returneras talarens identitet.

Alla talare bör först genomgå en registreringsprocess så att deras röster registreras i systemet och det skapas ett röstavtryck.


### <a name="enrollment"></a>Registrering

Registrering för talaridentifiering är textoberoende, vilket innebär att det inte finns några begränsningar på vad talaren säger i ljudfilen. Talarens röst registreras och ett antal egenskaper extraheras för att skapa en unik röstsignatur.


### <a name="recognition"></a>Igenkänning

Ljudet från den okända talaren tillhandahålls tillsammans med gruppen med presumtiva talare under igenkänningen. Den inmatade rösten jämförs med alla talarna i syfte att bestämma vems röst den är, och om det sker en matchning returneras talarens identitet.

Mer information om talaridentifiering finns i API:et [Speaker - Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e) (Talare – identifiering).
