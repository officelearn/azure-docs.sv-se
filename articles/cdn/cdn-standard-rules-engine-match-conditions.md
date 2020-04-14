---
title: Matcha villkor i standardregler motorn för Azure CDN | Microsoft-dokument
description: Referensdokumentation för matchningsvillkor i standardreglersmotorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259942"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Matcha villkor i standardregler motorn för Azure CDN

I [standardregelmotorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchningsvillkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchningsvillkoren som du kan använda i standardreglersmotorn för Azure CDN.

Den första delen av en regel är ett matchningsvillkor eller en uppsättning matchningsvillkor. I standardregelmotorn för Azure CDN kan varje regel ha upp till fyra matchningsvillkor. Ett matchningsvillkor identifierar specifika typer av begäranden för vilka definierade åtgärder utförs. Om du använder flera matchningsvillkor grupperas matchningsvillkoren tillsammans med hjälp av AND-logiken.

Du kan till exempel använda ett matchningsvillkor för att:

- Filtrera begäranden baserat på en specifik IP-adress, ett land eller en viss region.
- Filtrera begäranden efter huvudinformation.
- Filtrera begäranden från mobila enheter eller stationära enheter.

## <a name="match-conditions"></a>Matchvillkor

Följande matchningsvillkor är tillgängliga att använda i standardreglersmotorn för Azure CDN. 

### <a name="device-type"></a>Enhetstyp 

Identifierar begäranden som görs från en mobil enhet eller en stationär enhet.  

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika, inte lika med | Mobil, Stationär dator

### <a name="http-version"></a>HTTP-version

Identifierar begäranden baserat på HTTP-versionen av begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika, inte lika med | 2.0, 1.1, 1.0, 0.9, Alla

### <a name="request-cookies"></a>Begär cookies

Identifierar förfrågningar baserat på cookie-information i den inkommande begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Namn på cookie | Operator | Cookie-värde | Handlägga omformning
------------|----------|--------------|---------------
Sträng | [Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Du kan inte använda jokerteckenvärden (inklusive\*asterisker ( )) när du anger ett cookie-namn. du måste använda ett exakt cookie-namn.
- Du kan bara ange ett enda cookienamn per instans av det här matchningsvillkoret.
- Jämförelser av cookienamn är skiftlägesokänsliga.
- Om du vill ange flera cookievärden använder du ett enda blanksteg mellan varje cookie-värde. 
- Cookie-värden kan dra nytta av jokerteckenvärden.
- Om ett jokerteckenvärde inte har angetts uppfyller endast en exakt matchning detta matchningsvillkor. "Värde" matchar till exempel "Värde" men inte "Värde1". 

### <a name="post-argument"></a>Inlägg argument

Identifierar begäranden baserat på argument som definierats för post-begäransmetoden som används i begäran. 

#### <a name="required-fields"></a>Obligatoriska fält

Argumentnamn | Operator | Argumentvärde | Handlägga omformning
--------------|----------|----------------|---------------
Sträng | [Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

### <a name="query-string"></a>Frågesträng

Identifierar begäranden som innehåller en specifik frågesträngparameter. Den här parametern är inställd på ett värde som matchar ett visst mönster. Frågesträngparametrar (till exempel **parameter=värde)** i begäran URL avgöra om detta villkor är uppfyllt. Det här matchningsvillkoret identifierar en frågesträngparameter med dess namn och accepterar ett eller flera värden för parametervärdet.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Frågesträng | Omformning av ärende
---------|--------------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

### <a name="remote-address"></a>Fjärradress

Identifierar begäranden baserat på beställarens plats eller IP-adress.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|-----------------
Alla | Ej tillämpligt
Geo Match | Landskod
IP-matchning | IP-adress (utrymmesavgränsad)
Inte någon | Ej tillämpligt
Inte Geo Match | Landskod
Inte IP-matchning | IP-adress (utrymmesavgränsad)

#### <a name="key-information"></a>Viktig information

- Använd CIDR-notation.
- Om du vill ange flera IP-adresser och IP-adressblock använder du ett enda blanksteg mellan värdena:
  - **IPv4-exempel:** *1.2.3.4 10.20.30.40* matchar alla begäranden som kommer från adressen 1.2.3.4 eller 10.20.30.40.
  - **IPv6-exempel**: *1:2:3:5:7:7:8 10:20:30:40:50:60:70:80* matchar alla begäranden som kommer från adressen 1:2:3:5:5:6:7:8 eller 10:20:30:40:50:60:70:80.
- Syntaxen för ett IP-adressblock är bas-IP-adressen följt av ett snedstreck och prefixstorleken. Ett exempel:
  - **IPv4-exempel:** *5.5.5.64/26* matchar alla begäranden som kommer från adresserna 5.5.5.64 till 5.5.5.127.
  - **IPv6-exempel**: *1:2:3:/48* matchar alla förfrågningar som kommer från adresser 1:2:3:0:0:0:0:0 till 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff.

### <a name="request-body"></a>Begärandetext

Identifierar begäranden baserat på specifik text som visas i brödtexten i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Begärandetext | Handlägga omformning
---------|--------------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

### <a name="request-header"></a>Begärandehuvud

Identifierar begäranden som använder ett visst huvud i begäran.

#### <a name="required-fields"></a>Obligatoriska fält

Huvudnamn | Operator | Huvudvärde | Handlägga omformning
------------|----------|--------------|---------------
Sträng | [Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

### <a name="request-method"></a>Metod för begäran

Identifierar begäranden som använder den angivna begäransmetoden.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika, inte lika med | FÅ, POSTA, SÄTTA, TA BORT, HUVUD, ALTERNATIV, SPÅRA

#### <a name="key-information"></a>Viktig information

- Endast GET-begäran metoden kan generera cachelagrat innehåll i Azure CDN. Alla andra begäransmetoder proxied genom nätverket. 

### <a name="request-protocol"></a>Protokoll för begär

Identifierar begäranden som använder det angivna protokollet som används.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värden som stöds
---------|----------------
Lika, inte lika med | HTTP, HTTPS

### <a name="request-url"></a>URL för begäran

Identifierar begäranden som matchar den angivna URL:en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | URL för begäran | Handlägga omformning
---------|-------------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- När du använder det här regelvillkoret måste du inkludera protokollinformation. Till exempel: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Filtillägg för URL

Identifierar begäranden som innehåller det angivna filtillägget i filnamnet i den begärande URL:en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Anknytning | Handlägga omformning
---------|-----------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- För förlängning ska du inte inkludera en inledande period. Använd till exempel *html* i stället för *.html*.

### <a name="url-file-name"></a>Namn på URL-fil

Identifierar begäranden som innehåller det angivna filnamnet i den begärande URL:en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Filnamn | Handlägga omformning
---------|-----------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Om du vill ange flera filnamn avgränsar du varje filnamn med ett enda blanksteg. 

### <a name="url-path"></a>URL-sökväg

Identifierar begäranden som innehåller den angivna sökvägen i den begärande URL:en.

#### <a name="required-fields"></a>Obligatoriska fält

Operator | Värde | Omformning av ärende
---------|-------|---------------
[Standardoperatorlista](#standard-operator-list) | Sträng, Int | Ingen transformering, till versaler, till gemener

#### <a name="key-information"></a>Viktig information

- Ett filnamnsvärde kan dra nytta av jokerteckenvärden. Varje filnamnsmönster kan till exempel bestå av en eller flera asterisker (*), där varje asterisk matchar en sekvens med ett eller flera tecken.

## <a name="reference-for-rules-engine-match-conditions"></a>Referens för regler motor match villkor

### <a name="standard-operator-list"></a>Standardoperatorlista

För regler som accepterar värden från standardoperatorlistan är följande operatorer giltiga:

- Alla
- Är lika med 
- Innehåller 
- Börjar med 
- Slutar med 
- Mindre än
- Mindre än eller lika med
- Större än
- Större än eller lika med
- Inte någon
- Innehåller inte
- Börjar inte med 
- Slutar inte med 
- Inte mindre än
- Inte mindre än eller lika med
- Inte större än
- Inte större än eller lika med

För numeriska operatorer som *Mindre än* och *Större än eller lika med*baseras jämförelsen som används på längd. I det här fallet ska värdet i matchningsvillkoret vara ett heltal som är lika med den längd du vill jämföra. 

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Åtgärder i standardregler motorn](cdn-standard-rules-engine-actions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
