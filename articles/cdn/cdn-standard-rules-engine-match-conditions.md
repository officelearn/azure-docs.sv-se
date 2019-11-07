---
title: Azure CDN från Microsofts standard regel motor matchnings villkor | Microsoft Docs
description: Referens dokumentation för Azure Content Delivery Network från Microsoft standard Rules Engine matchnings villkor.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615967"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Azure CDN från Microsofts standard regel motor matchnings villkor

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchnings villkor för Azure-Content Delivery Network (CDN) från Microsofts [standard regel motor](cdn-standard-rules-engine.md).

Den första delen av en regel är en uppsättning matchnings villkor. Varje regel kan ha upp till 4 matchnings villkor. Ett matchnings villkor identifierar vissa typer av begär Anden för vilka de åtgärder som definierats i regeln ska utföras. Om du använder flera matchnings villkor grupperas de tillsammans med och logiken.

Du kan till exempel använda ett matchnings villkor för att:

- Filtrera förfrågningar som genereras från en viss IP-adress eller land/region.
- Filtrera förfrågningar efter rubrik information.
- Filtrera förfrågningar från mobila eller Station ära enheter.

## <a name="match-conditions"></a>Matchnings villkor

Följande matchnings villkor är tillgängliga för användning. 

### <a name="device-type"></a>Enhets typ 

Matchnings villkoret för enhets typen identifierar begär Anden som görs från en mobil-eller fjärr skrivbords enhet baserat på dess egenskaper.  

**Obligatoriska fält**

Operator | Värde som stöds
---------|----------------
Lika med, inte lika med | Mobil, stationär


### <a name="http-version"></a>HTTP-version

Tillståndet för matchning av HTTP-version identifierar begär Anden baserat på den HTTP-version som begäran kommer till.

**Obligatoriska fält**

Operator | Värde som stöds
---------|----------------
Lika med, inte lika med | 2,0, 1,1, 1,0, 0,9, alla


### <a name="request-cookies"></a>Begär cookies

Villkoren för begäran om matchning av cookies identifierar begär Anden baserat på cookie-information i inkommande begäran.

**Obligatoriska fält**

Cookie-namn | Operator | Cookie-värde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

Viktig information
- Jokertecken, inklusive asterisker (*), stöds inte när du anger ett cookie-namn, men endast exakta cookie-namn matchar är giltiga för jämförelse.
- Det går bara att ange ett enda cookie-namn per instans av matchnings villkoret.
- Cookie-namn jämförelser är inte Skift läges känsliga.
- Ange flera cookie-värden genom att avgränsa var och en med ett enda blank steg. 
- Cookie-värden kan dra nytta av jokertecken.
- Om ett jokertecken inte har angetts uppfyller det bara matchnings villkoret. Om du till exempel anger "värde" matchar "värde", men inte "värde1". 

### <a name="post-argument"></a>Publicera argument

**Obligatoriska fält**

Argument namn | Operator | Argument värde | Skift läges omvandling
--------------|----------|----------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="query-string"></a>Frågesträng

Villkoren för Frågesträngs matchning identifierar begär Anden som innehåller en angiven frågesträng. Den här parametern har angetts till ett värde som matchar ett angivet mönster. Parametrar för frågesträng (till exempel parameter = värde) i URL: en för begäran avgör om det här villkoret är uppfyllt. Detta matchnings villkor identifierar en frågesträngparametern efter dess namn och accepterar ett eller flera värden för parametervärdet.

**Obligatoriska fält**

Operator | Frågesträng | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="remote-address"></a>Fjärradress

Villkoren för Fjärradress matchning identifierar begär Anden baserat på beställarens plats eller IP-adress.

**Obligatoriska fält**

Operator | Värden som stöds
---------|-----------------
Alla | Saknas
Geo-matchning | Lands koder
IP-matchning | IP-adresser (separerat utrymme)
Inte alla | Saknas
Ingen geo-matchning | Lands koder
Inte IP-matchning | IP-adresser (separerat utrymme)

Viktig information:

- Använd CIDR-notering.
- Ange flera IP-adresser och/eller IP-adressblock genom att begränsa var och en av dem med ett enda blank steg. Till exempel:
  - **IPv4-exempel**: 1.2.3.4 10.20.30.40 matchar alla begär Anden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matchar alla begär Anden som kommer från adress 1:2:3:4:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixets storlek. Till exempel:
  - **IPv4-exempel**: 5.5.5.64/26 matchar alla begär Anden som kommer från adresser 5.5.5.64 via 5.5.5.127.
  - **IPv6-exempel**: 1:2:3:/48 matchar alla begär Anden som kommer från adresser 1:2:3:0:0:0:0:0 till och med 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.

### <a name="request-body"></a>Begärandetext

**Obligatoriska fält**

Operator | Begärandetext | Skift läges omvandling
---------|--------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="request-header"></a>Begärandehuvud

**Obligatoriska fält**
Rubrik namn | Operator | Huvud värde | Skift läges omvandling
------------|----------|--------------|---------------
Sträng | [Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

### <a name="request-method"></a>Metod för begäran

**Obligatoriska fält**

Operator | Värde som stöds
---------|----------------
Lika med, inte lika med | HÄMTA, POSTA, PLACERA, TA BORT, HUVUD, ALTERNATIV, SPÅRA

Viktig information:

- Endast GET Request-metoden kan generera cachelagrat innehåll i CDN. Alla andra metoder för begäran är proxy via nätverket. 

### <a name="request-protocol"></a>Protokoll för begäran

**Obligatoriska fält**

Operator | Värde som stöds
---------|----------------
Lika med, inte lika med | HTTP, HTTPS

### <a name="request-url"></a>URL för begäran

**Obligatoriska fält**

Operator | URL för begäran | Skift läges omvandling
---------|-------------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

Viktig information:

- Se till att du inkluderar protokoll information när du anger URL för begäran. Till exempel "https://www. [dindomän]. com "

### <a name="url-file-extension"></a>URL-filtillägg

**Obligatoriska fält**

Operator | Anknytning | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

Viktig information:

- Inkludera inte en inledande period för tillägg. Använd till exempel HTML i stället för. html.

### <a name="url-file-name"></a>URL-filnamn

**Obligatoriska fält**

Operator | Filnamn | Skift läges omvandling
---------|-----------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

Viktig information:

- Om du vill ange flera fil namn avgränsar du varje fil namn med ett enda blank steg. 

### <a name="url-path"></a>URL-sökväg

**Obligatoriska fält**

Operator | Värde | Skift läges omvandling
---------|-------|---------------
[Lista med standard operatorer](#standard-operator-list) | Sträng, heltal | Ingen transformering, till versaler, till gemener

Viktig information:

- Ett fil namns värde kan dra nytta av jokertecken. Varje fil namns mönster kan till exempel bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regel motorns matchnings villkor

### <a name="standard-operator-list"></a>Lista med standard operatorer

För regler som innehåller listan med standard operatorer är följande operatorer giltiga:

- Alla
- Lika med 
- Contains 
- Börjar med 
- Slutar med 
- Mindre än
- Mindre än eller lika med
- Större än
- Större än eller lika med
- Inte alla
- Innehåller inte
- Börjar inte med 
- Slutar inte med 
- Inte mindre än
- Inte mindre än eller lika med
- Inte större än
- Inte större än eller lika med

För numeriska operatorer, som "mindre än" eller "större än eller lika med", är jämförelsen baserad på längd. I det här fallet ska värdet i matchnings villkoret vara ett heltal som är lika med den längd som du vill jämföra. 

---

[Överst på sidan](#match-conditions)

</br>

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Content Delivery Network](cdn-overview.md)
- [Regelmotor – referens](cdn-standard-rules-engine-reference.md)
- [Regel motor åtgärder](cdn-standard-rules-engine-actions.md)
- [Använd HTTPS med standard regel motorn](cdn-standard-rules-engine.md)
