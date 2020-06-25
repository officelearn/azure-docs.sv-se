---
title: Azure Front Door
description: Den här artikeln innehåller en översikt över funktionen för motor funktioner i Azures frontend-dörr.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: ee981d08e53765003e88870d35b291a5802e6848
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322011"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Vad är regel motor för Azures frontend-dörr? 

Med regel motorn kan du anpassa hur HTTP-begäranden hanteras i gränsen och ger mer kontroll över hur ditt webb program fungerar. Regel motorn för Azures front dörr består av flera viktiga funktioner, inklusive:

- Använd HTTPS, se till att alla dina slutanvändare interagerar med ditt innehåll via en säker anslutning.
- Implementera säkerhets rubriker för att förhindra webbläsarbaserade sårbarheter som HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options och Access-Control-Allow-Origin för cross-origin Resource Sharing (CORS)-scenarier. Säkerhetsbaserade attribut kan också definieras med cookies.
- Dirigera begär anden till mobila eller Station ära versioner av programmet baserat på mönster i innehåll i begärandehuvuden, cookies eller frågesträngar.
- Använd omdirigerings funktioner för att returnera 301, 302, 307 och 308 omdirigeras till klienten för att omdirigera till nya värdnamn, sökvägar eller protokoll.
- Ändra konfigurationen för cachelagring för din väg dynamiskt baserat på inkommande begär Anden.
- Skriv sökvägen till URL: en för begäran och vidarebefordra begäran till lämplig server del i den konfigurerade backend-poolen.

## <a name="architecture"></a>Arkitektur 

Regel motorn hanterar begär Anden vid gränsen. När du har konfigurerat regel motorn och en begäran träffar din slut punkt för din frontend-WAF körs först, följt av den regel motor konfiguration som är kopplad till klient delen/domänen. När en regel motor konfiguration körs, innebär det att den överordnade routningslänken redan är en matchning. Oavsett om alla åtgärder i varje regel i regel motor konfigurationen körs, omfattas alla matchnings villkor i regeln uppfylls. Om en begäran inte matchar något av villkoren i din regel Motors konfiguration, körs standard regeln för routning. 

I konfigurationen nedan konfigureras exempelvis en regel motor för att lägga till ett svars huvud som ändrar Max åldern för cache-kontrollen om matchnings villkoret är uppfyllt. 

![svars huvud åtgärd](./media/front-door-rules-engine/rules-engine-architecture-3.png)

I ett annat exempel ser vi att regel motorn har kon figurer ATS för att skicka en användare till en mobil version av platsen om matchnings villkoret, enhets typen, är true. 

![Åsidosätt konfigurering av väg konfiguration](./media/front-door-rules-engine/rules-engine-architecture-1.png)

I båda dessa exempel, när inget av matchnings villkoren uppfylls, är den angivna väg regeln det som körs. 

## <a name="terminology"></a>Terminologi 

Med AFD-regel motor kan du skapa en serie med regler för motor konfiguration, som var och en består av en uppsättning regler. Här följer en del av en praktisk terminologi som du kommer att komma åt när du konfigurerar din regel motor. 

- *Regel motor konfiguration*: en uppsättning regler som tillämpas på en regel för en enda väg. Varje konfiguration är begränsad till 25 regler. Du kan skapa upp till 10 konfigurationer. 
- Regel *motor regel*: en regel som består av upp till 10 matchnings villkor och 5 åtgärder.
- *Matchnings villkor*: det finns flera matchnings villkor som kan användas för att analysera inkommande begär Anden. En regel kan innehålla upp till 10 matchnings villkor. Matchnings villkor utvärderas med operatorn **och** . Du hittar en fullständig lista över matchnings villkor [här](front-door-rules-engine-match-conditions.md). 
- *Åtgärd*: åtgärder styr vad som händer i dina inkommande begär Anden – åtgärder för begäran/svars huvud, vidarebefordran, omdirigering och omskrivning är alla tillgängliga idag. En regel kan innehålla upp till 5 åtgärder. en regel får dock bara innehålla en åsidosättning av Route-konfigurationen.  Du hittar en fullständig lista över åtgärder [här](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel motor konfiguration](front-door-tutorial-rules-engine.md). 
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
