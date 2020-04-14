---
title: Åtgärder i standardreglersmotorn för Azure CDN | Microsoft-dokument
description: Referensdokumentation för åtgärder i standardreglersmotorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259960"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Åtgärder i standardreglersmotorn för Azure CDN

I [standardregelmotorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchningsvillkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av de åtgärder som du kan använda i standardregler motorn för Azure CDN.

Den andra delen av en regel är en åtgärd. En åtgärd definierar det beteende som tillämpas på den begärandetyp som ett matchningsvillkor eller en uppsättning matchningsvillkor identifierar.

## <a name="actions"></a>Åtgärder

Följande åtgärder är tillgängliga att använda i standardregler motorn för Azure CDN. 

### <a name="cache-expiration"></a>Cache förfallodatum

Använd den här åtgärden om du vill skriva över tid att live (TTL) värdet för slutpunkten för begäranden som reglerna matchar villkor anger.

#### <a name="required-fields"></a>Obligatoriska fält

Cache-beteende |  Beskrivning              
---------------|----------------
Bypass-cache | När det här alternativet är markerat och regeln matchar cachelagras inte innehållet.
Åsidosätt | När det här alternativet är markerat och regeln matchar skrivs TTL-värdet som returneras från ditt ursprung över med det värde som anges i åtgärden.
Ange om det saknas | När det här alternativet är markerat och regeln matchar, om inget TTL-värde returnerades från ditt ursprung, anger regeln TTL till det värde som anges i åtgärden.

#### <a name="additional-fields"></a>Ytterligare fält

Dagar | Timmar | Minuter | Sekunder
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Frågesträng för cachetangent

Använd den här åtgärden om du vill ändra cachenyckeln baserat på frågesträngar.

#### <a name="required-fields"></a>Obligatoriska fält

Beteende | Beskrivning
---------|------------
Inkludera | När det här alternativet är markerat och regeln matchar inkluderas frågesträngar som anges i parametrarna när cachenyckeln genereras. 
Cachelagra varje unik URL | När det här alternativet är markerat och regeln matchar har varje unik URL en egen cachenyckel. 
Exclude | När det här alternativet är markerat och regeln matchar utesluts frågesträngar som anges i parametrarna när cachenyckeln genereras.
Ignorera frågesträngar | När det här alternativet är markerat och regeln matchar beaktas inte frågesträngar när cachenyckeln genereras. 

### <a name="modify-request-header"></a>Ändra målhuvud

Använd den här åtgärden om du vill ändra rubriker som finns i begäranden som skickas till ditt ursprung.

#### <a name="required-fields"></a>Obligatoriska fält

Åtgärd | HTTP-huvudnamn | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar läggs huvudet som anges i **Sidhuvudsnamn** till i begäran med det angivna värdet. Om huvudet redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar läggs huvudet som anges i **Sidhuvudsnamn** till i begäran med det angivna värdet. Om huvudet redan finns skriver det angivna värdet över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat matchar regeln och det huvud som anges i regeln finns, tas huvudet bort från begäran. | Sträng

### <a name="modify-response-header"></a>Ändra svarshuvud

Använd den här åtgärden om du vill ändra rubriker som finns i svar som returneras till dina klienter.

#### <a name="required-fields"></a>Obligatoriska fält

Åtgärd | HTTP-huvudnamn | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar läggs huvudet som anges i **Sidhuvudsnamn** till i svaret med hjälp av det angivna **värdet**. Om huvudet redan finns läggs **värde** till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar läggs huvudet som anges i **Sidhuvudsnamn** till i svaret med hjälp av det angivna **värdet**. Om huvudet redan finns skriver **Value** över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat matchar regeln och det huvud som anges i regeln finns, tas huvudet bort från svaret. | Sträng

### <a name="url-redirect"></a>URL-omdirigering

Använd den här åtgärden för att omdirigera klienter till en ny URL. 

#### <a name="required-fields"></a>Obligatoriska fält

Field | Beskrivning 
------|------------
Typ | Välj svarstypen för att återgå till beställaren: Hittade (302), Flyttad (301), Tillfällig omdirigering (307) och Permanent omdirigering (308).
Protokoll | Matchförfrågan, HTTP, HTTPS.
Värdnamn | Välj det värdnamn som du vill att begäran ska omdirigeras till. Lämna tomt för att bevara den inkommande värden.
Sökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill bevara den inkommande banan.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill bevara den inkommande frågesträngen. 
Fragment | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt för att bevara det inkommande fragmentet. 

Vi rekommenderar starkt att du använder en absolut webbadress. Om du använder en relativ URL kan azure CDN-url:er omdirigeras till en ogiltig sökväg. 

### <a name="url-rewrite"></a>URL-omskrivning

Använd den här åtgärden om du vill skriva om sökvägen till en begäran som är på väg till ditt ursprung.

#### <a name="required-fields"></a>Obligatoriska fält

Field | Beskrivning 
------|------------
Källmönster | Definiera källmönstret i URL-sökvägen som ska ersättas. För närvarande använder källmönstret en prefixbaserad matchning. Om du vill matcha alla URL-sökvägar använder du ett snedstreck (**/**) som källmönstervärde.
Mål | Definiera målsökvägen som ska användas i omskrivningen. Målsökvägen skriver över källmönstret.
Bevara omatchad bana | Om värdet **är Ja**läggs den återstående sökvägen efter källmönstret till i den nya målsökvägen. 

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Matcha villkor i standardregler motorn](cdn-standard-rules-engine-match-conditions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
