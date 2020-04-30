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
ms.openlocfilehash: 3e7c9606a17736ea45b09a4d6981b4d55fa6dee6
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515561"
---
# <a name="azure-front-door-rules-engine-actions"></a>Motor åtgärder för Azures frontend-dörr

En regel i [AFD Rules Engine](front-door-rules-engine.md) består av noll eller flera matchnings villkor och åtgärder. Den här artikeln innehåller detaljerade beskrivningar av de åtgärder som du kan använda i AFD-regel motorn.

En åtgärd definierar det beteende som används för den typ av begäran som ett matchnings villkor eller en uppsättning matchnings villkor identifierar. I AFD-regel motor kan en regel innehålla upp till fem åtgärder, endast en som kan vara en åsidosättning av väg konfigurations åtgärder (vidarebefordra eller omdirigera). 

Följande åtgärder är tillgängliga för användning i Azures motor för front dörrs regler.  

## <a name="modify-request-header"></a>Ändra rubrik för begäran

Använd den här åtgärden för att ändra rubriker som finns i begär Anden som skickas till ditt ursprung.

### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns läggs värdet till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i begäran med det angivna värdet. Om rubriken redan finns skriver det angivna värdet över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat, regeln matchar och rubriken som anges i regeln finns, raderas rubriken från begäran. | Sträng

## <a name="modify-response-header"></a>Ändra svars huvud

Använd den här åtgärden för att ändra huvuden som finns i svar som returneras till dina klienter.

### <a name="required-fields"></a>Obligatoriska fält

Action | Namn på HTTP-huvud | Värde
-------|------------------|------
Lägg till | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns läggs **värdet** till i det befintliga värdet. | Sträng
Skriv över | När det här alternativet är markerat och regeln matchar, läggs rubriken som anges i **huvud namn** till i svaret med hjälp av det angivna **värdet**. Om rubriken redan finns skriver **värdet** över det befintliga värdet. | Sträng
Ta bort | När det här alternativet är markerat, regeln matchar och rubriken som anges i regeln är närvarande, tas rubriken bort från svaret. | Sträng

## <a name="route-configuration-overrides"></a>Åsidosättningar av väg konfiguration 

### <a name="route-type-redirect"></a>Typ av väg: omdirigera

Använd den här åtgärden för att omdirigera klienter till en ny URL. 

#### <a name="required-fields"></a>Obligatoriska fält

Field | Beskrivning 
------|------------
Omdirigera typ | Välj den svarstyp som ska returneras till begär ande: hittas (302), flyttad (301), tillfällig omdirigering (307) och permanent omdirigering (308).
Omdirigera protokoll | Matcha begäran, HTTP, HTTPS.
Målvärd | Välj värd namnet som du vill att begäran ska omdirigeras till. Lämna tomt om du vill bevara den inkommande värden.
Målsökväg | Definiera sökvägen som ska användas i omdirigeringen. Lämna tomt om du vill behålla den inkommande sökvägen.  
Frågesträng | Definiera frågesträngen som används i omdirigeringen. Lämna tomt om du vill behålla inkommande frågesträng. 
Målcachen | Definiera det fragment som ska användas i omdirigeringen. Lämna tomt om du vill behålla det inkommande fragmentet. 


### <a name="route-type-forward"></a>Typ av väg: vidarebefordra

Använd den här åtgärden för att vidarebefordra klienter till en ny URL. Den här åtgärden innehåller även under åtgärder för URL-omskrivning och cachelagring. 

Field | Beskrivning 
------|------------
Serverdelspool | Välj den backend-pool som du vill åsidosätta och hantera begär Anden från. Då visas alla dina förkonfigurerade Server dels grupper i din profil för din klient del. 
Protokoll för vidarebefordran | Matcha begäran, HTTP, HTTPS.
URL-omskrivning | Använd den här åtgärden för att skriva om sökvägen för en begäran som är en väg till ditt ursprung. Om aktive rad, se nedan för ytterligare fält som krävs
Cachelagring | Aktiverad, inaktiverad. Se nedan för ytterligare fält som krävs om det är aktiverat. 

#### <a name="url-rewrite"></a>URL-omskrivning

Använd den här inställningen om du vill konfigurera en valfri **anpassad vidarebefordrings Sök väg** som ska användas för att skapa begäran att vidarebefordra till Server delen.

Field | Beskrivning 
------|------------
Anpassad vidarebefordrings Sök väg | Definiera sökvägen för att vidarebefordra begär anden till. 

#### <a name="caching"></a>Cachelagring

Använd de här inställningarna för att styra hur filer cachelagras för förfrågningar som innehåller frågesträngar och om innehållet ska cachelagras baserat på alla parametrar eller på valda parametrar. Du kan använda ytterligare inställningar för att skriva över TTL-värdet (Time to Live) för att styra hur lång tid innehållet stannar i cacheminnet för begär Anden som regler matchar villkoren anger. Om du vill framtvinga cachelagring som åtgärd anger du fältet cachelagring till "Enabled". När du gör detta visas följande alternativ: 

Cache-beteende |  Beskrivning              
---------------|----------------
Ignorera frågesträngar | När till gången har cachelagrats ignorerar alla efterföljande förfrågningar frågesträngarna tills den cachelagrade till gången upphör att gälla.
Cachelagra varje unik URL | Varje begäran med en unik URL, inklusive frågesträngen, behandlas som en unik till gång med sin egen cache.
Ignorera angivna frågesträngar | Fråge strängar för URL: en som anges i inställningen frågeparametrar ignoreras för cachelagring.
Inkludera angivna frågesträngar | Fråge strängar för URL: en som anges i inställningen frågeparametrar används för cachelagring.

Ytterligare fält |  Beskrivning 
------------------|---------------
Dynamisk komprimering | Front dörren kan dynamiskt komprimera innehåll på gränsen, vilket resulterar i ett mindre och snabbare svar.
Frågeparametrar | En kommaavgränsad lista över tillåtna (eller otillåtna) parametrar som ska användas som grund för cachelagring.
Varaktighet för cache | Förfallo tid för cache i dagar, timmar, minuter, sekunder. Alla värden måste vara int. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel motor konfiguration](front-door-tutorial-rules-engine.md). 
- Lär dig mer om [regel motorns matchnings villkor](front-door-rules-engine-match-conditions.md)
- Läs mer om [Azures motor för front dörr](front-door-rules-engine.md)
