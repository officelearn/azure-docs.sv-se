---
title: Azure-front dörr | Microsoft Docs
description: Den här artikeln innehåller en översikt för Azure Front Door. Ta reda på om det är rätt val för belastnings utjämning av användar trafik för ditt program.
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
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515554"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Vad är regel motor för Azures frontend-dörr? 

Med regel motorn kan du anpassa hur HTTP-begäranden hanteras i gränsen och ger mer kontroll över hur ditt webb program fungerar. Regel motorn för Azures front dörr består av flera viktiga funktioner, inklusive:

- Huvudbaserad routning – dirigera förfrågningar baserat på mönster i innehållet i begärandehuvuden, cookies och frågesträngar.
- Parameter-baserad routning – dra nytta av en serie matchnings villkor, inklusive post argument, frågesträngar, cookies och metoder för begäran, för att dirigera begär Anden baserat på HTTP-parametrarna för begäran. 
- Route-konfigurationer åsidosätter: 
    - Använd omdirigerings funktioner för att returnera 301/302/307/308 omdirigerar till klienten för att omdirigera till nya värdnamn, sökvägar och protokoll. 
    - Använd vidarebefordrande funktioner för att skriva om URL: en för begäran utan att göra en traditionell omdirigering och vidarebefordra begäran till lämplig server del i den konfigurerade backend-poolen. 
    - Anpassa konfigurationen för cachelagring och ändra en väg dynamiskt från vidarebefordran till cachelagring baserat på matchnings villkor. 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Arkitektur 

Regel motorn hanterar begär Anden vid gränsen. När du har konfigurerat regel motorn och en begäran träffar din slut punkt för din frontend-WAF körs först, följt av den regel motor konfiguration som är kopplad till klient delen/domänen. När en regel motor konfiguration körs, innebär det att den överordnade routningslänken redan är en matchning. Oavsett om alla åtgärder i varje regel i regel motor konfigurationen körs, omfattas alla matchnings villkor i regeln uppfylls. Om en begäran inte matchar något av villkoren i din regel Motors konfiguration, körs standard regeln för routning. 

I konfigurationen nedan konfigureras exempelvis en regel motor för att lägga till ett svars huvud som ändrar Max åldern för cache-kontrollen om matchnings villkoret är uppfyllt. 

![svars huvud åtgärd](./media/front-door-rules-engine/rules-engine-architecture-3.png)

I ett annat exempel ser vi att regel motorn har kon figurer ATS för att skicka en användare till en mobil version av platsen om matchnings villkoret, enhets typen, är true. 

![Åsidosätt konfigurering av väg konfiguration](./media/front-door-rules-engine/rules-engine-architecture-1.png)

I båda dessa exempel, när inget av matchnings villkoren uppfylls, är den angivna väg regeln det som körs. 

## <a name="terminology"></a>Terminologi 

Med AFD-regel motor kan du skapa en serie med regler för motor konfiguration, som var och en består av en uppsättning regler. Här följer en del av en praktisk terminologi som du kommer att komma åt när du konfigurerar din regel motor. 

- *Regel motor konfiguration*: en uppsättning regler som tillämpas på en regel för en enda väg. Varje konfiguration är begränsad till 5 regler. Du kan skapa upp till 10 konfigurationer. 
- Regel *motor regel*: en regel som består av upp till 10 matchnings villkor och 5 åtgärder.
- *Matchnings villkor*: det finns flera matchnings villkor som kan användas för att analysera inkommande begär Anden. En regel kan innehålla upp till 10 matchnings villkor. Matchnings villkor utvärderas med operatorn **och** . Du hittar en fullständig lista över matchnings villkor [här](front-door-rules-engine-match-conditions.md). 
- *Åtgärd*: åtgärder styr vad som händer i dina inkommande begär Anden – åtgärder för begäran/svars huvud, vidarebefordran, omdirigering och omskrivning är alla tillgängliga idag. En regel kan innehålla upp till 5 åtgärder. en regel får dock bara innehålla en åsidosättning av Route-konfigurationen.  Du hittar en fullständig lista över åtgärder [här](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel motor konfiguration](front-door-tutorial-rules-engine.md). 
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
