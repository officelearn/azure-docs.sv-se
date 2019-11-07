---
title: Azure CDN från Microsofts standard regler för motor åtgärder | Microsoft Docs
description: Referens dokumentation för Azure CDN från Microsoft-standardregels motor åtgärder.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615993"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azure CDN från Microsoft standard-regel motor åtgärder

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga åtgärder för Azure Content Delivery Network (CDN) från Microsofts [standard regel motor](cdn-standard-rules-engine.md).

Den andra delen av en regel är en åtgärd. En åtgärd definierar det beteende som används för den typ av begäran som identifieras av en uppsättning matchnings villkor.

## <a name="actions"></a>Åtgärder

Följande åtgärder är tillgängliga för användning. 

## <a name="cache-expiration"></a>Förfallo tid för cache

Med den här åtgärden kan du skriva över TTL-värdet för slut punkten för begär Anden som anges av reglerna matchnings villkor.

**Obligatoriska fält**

Cache-beteende |                
---------------|----------------
Kringgå cacheminne | När det här alternativet är markerat och regeln matchar cachelagras inte innehållet.
Åsidosättningsinställning | När det här alternativet är markerat och regeln matchar, kommer det TTL-värde som returneras från ursprung att skrivas över med det värde som anges i åtgärden.
Ange om det saknas | När det här alternativet är markerat och regeln matchar, om det inte fanns något TTL-värde från ursprunget, anger regeln TTL till det värde som anges i åtgärden.

**Ytterligare fält**

Dagar | Timmar | Minuter | Sekunder
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>Frågesträng för cache-nyckel

Med den här åtgärden kan du ändra cache-nyckeln baserat på frågesträngar.

**Obligatoriska fält**

Beteende | Beskrivning
---------|------------
omfattar | När det här alternativet är markerat och regeln matchar, kommer frågesträngarna som anges i parametrarna att inkluderas när du genererar cache-nyckeln. 
Cachelagra varje unik URL | När det här alternativet är markerat och regeln matchar, kommer varje unik URL att ha sin egen cache-nyckel. 
Exclude | När det här alternativet är markerat och regeln matchar, kommer frågesträngarna som anges i parametrarna att undantas när du genererar cache-nyckeln.
Ignorera frågesträngar | När det här alternativet är markerat och regeln matchar, beaktas inte frågesträngar när du genererar cache-nyckeln. 

## <a name="modify-request-header"></a>Ändra rubrik för begäran

Med den här åtgärden kan du ändra huvuden som finns i begär Anden som skickas till ditt ursprung.

**Obligatoriska fält**

Åtgärd | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i huvud namn till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriva över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i huvud namn till i begäran med det angivna värdet. Om rubriken redan finns skrivs det befintliga värdet över av värdet. | Sträng
Ta bort | När det här alternativet är markerat och regeln matchar, och rubriken som anges i regeln finns, tas den bort från begäran. | Sträng

## <a name="modify-response-header"></a>Ändra svars huvud

Med den här åtgärden kan du ändra huvuden som finns i svar som returneras till dina slut klienter

**Obligatoriska fält**

Åtgärd | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i huvud namn till i svaret med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriva över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i huvud namn till i svaret med det angivna värdet. Om rubriken redan finns skrivs det befintliga värdet över av värdet. | Sträng
Ta bort | När det här alternativet är markerat och regeln matchar, och rubriken som anges i regeln finns, tas den bort från svaret. | Sträng

## <a name="url-redirect"></a>URL-omdirigering

Med den här åtgärden kan du omdirigera slut klienter till en ny URL. 

**Obligatoriska fält**

Fält | Beskrivning 
------|------------
Typ | Välj den svarstyp som ska returneras till beställaren. Alternativen är-302 hittades, 301 flyttade, 307 tillfällig omdirigering och 308 permanent omdirigering
Protokoll | Matcha begäran, HTTP eller HTTPS
Värdnamn | Välj det värdnamn som begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden.
Sökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill bevara den inkommande sökvägen.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill behålla inkommande frågesträng. 
Fragment | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt om du vill bevara det inkommande fragmentet. 

Vi rekommenderar starkt att du använder en absolut URL. Användning av en relativ URL kan omdirigera CDN-URL: er till en ogiltig sökväg. 

## <a name="url-rewrite"></a>URL-omskrivning

Med den här åtgärden kan du skriva om sökvägen för en begäran som har en väg till ditt ursprung.

**Obligatoriska fält**

Fält | Beskrivning 
------|------------
Käll mönster | Definiera käll mönstret i URL-sökvägen som ska ersättas. För närvarande använder käll mönstret en prefix-baserad matchning. Använd "/" som käll mönster värde för att matcha alla URL-sökvägar.
Mål | Definiera mål Sök vägen som ska användas vid omskrivning. Käll mönstret skrivs över
Bevara omatchad sökväg | Om ja, kommer den återstående sökvägen efter käll mönstret att läggas till i den nya mål Sök vägen. 


[Överst på sidan](#actions)

</br>

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Regelmotor – referens](cdn-standard-rules-engine-reference.md)
- [Regelmotor – matchningsvillkor](cdn-standard-rules-engine-match-conditions.md)
- [Använd HTTPS med standard regel motorn](cdn-standard-rules-engine.md)
