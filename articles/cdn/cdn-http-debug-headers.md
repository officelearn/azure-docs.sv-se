---
title: X-EC-Debug HTTP-huvuden för Azure CDN regelmotor | Microsoft Docs
description: Begärandehuvudet X-EC-Debug debug cache tillhandahåller ytterligare information om den princip som tillämpas på den begärda tillgången. Dessa huvuden är specifika för Verizon.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: v-deasim
ms.openlocfilehash: 3a99e322d81748c54585e7dd0eb06959bfeb9569
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC-Debug HTTP-huvuden för Azure CDN regelmotor
Begärandehuvudet debug cache `X-EC-Debug`, tillhandahåller ytterligare information om den princip som tillämpas på den begärda tillgången. Dessa huvuden är specifika för **Azure CDN Premium från Verizon** produkter.

## <a name="usage"></a>Användning
Svaret från POP-servrar till en användare omfattar den `X-EC-Debug` huvud endast när följande villkor är uppfyllda:

- Den [Debug Cache svarshuvuden funktionen](cdn-rules-engine-reference-features.md#debug-cache-response-headers) har aktiverats på regelmotor för den angivna begäranden.
- Den angivna begäranden definierar en uppsättning debug cache-svarshuvuden som ska inkluderas i svaret.

## <a name="requesting-debug-cache-information"></a>Begär felsökningsinformation om cachelagring
Använd följande direktiv i den angivna begäranden för att definiera cache felsökningsinformation som ska inkluderas i svaret:

Huvudet i begäran | Beskrivning |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Cache-statuskod](#cache-status-code-information)
X-EC-Debug: x-ec-cache-fjärråtkomst | [Cache-statuskod](#cache-status-code-information)
X-EC-Debug: x-ec-check-Cacheable ställs | [Cacheable ställs](#cacheable-response-header)
X-EC-Debug: x ec-cache-nyckel | [Cache-nyckel](#cache-key-response-header)
X-EC-Debug: x ec-cache-tillstånd | [Cache-tillstånd](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Felsöka cachesvar huvuden kan begäras genom att inkludera följande huvud och de angivna direktiven i förfrågan:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Exempel X-EC-Debug-huvud

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Cache statusinformation för kod
Rubriken X EC Debug kan identifiera en server och hur den hanteras via följande direktiv svaret:

Sidhuvud | Beskrivning
-------|------------
X-EC-Debug: x-ec-cache | Det här sidhuvudet rapporteras när innehållet dirigeras via CDN. Den identifierar POP-servern som uppfyllt begäran.
X-EC-Debug: x-ec-cache-fjärråtkomst | Det här sidhuvudet rapporteras endast när det begärda innehållet cachelagrades på ett ursprung shield server eller en ADN gateway-servern.

### <a name="response-header-format"></a>Svaret huvudformat

Huvudet X-EC-Debug rapporterar cache kod statusinformation i följande format:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Termer som används i syntaxen ovan svar huvud definieras enligt följande:
- StatusCode: Anger hur det begärda innehållet hanterades av CDN som representeras via en cache-statuskod.
    
    Statuskoden TCP_DENIED rapporteras i stället för att ingen när en obehörig begäran nekas på grund av Token-baserad autentisering. Statuskoden NONE även fortsättningsvis användas när du visar Cache statusrapporter eller raw loggdata.

- Plattform: Anger den plattform som innehållet har begärts. Följande koder är giltiga för det här fältet:

    Kod  | Plattform
    ------| --------
    ECAcc | Stora HTTP
    ECS   | HTTP-liten
    ECD   | Application Delivery Network (ADN)

- POP: Anger den [POP](cdn-pop-abbreviations.md) som hanterade begäran. 

### <a name="sample-response-headers"></a>Exempel svarshuvuden

Följande exempel huvuden innehåller cache kod statusinformation för en begäran:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Cacheable ställs svarshuvud
Den `X-EC-Debug: x-ec-check-cacheable` svarshuvud anger om det begärda innehållet kan har cachelagrats.

Den här svarshuvud anger inte om cachelagring ägde rum. I stället anger om begäran var berättigad för cachelagring.

### <a name="response-header-format"></a>Svaret huvudformat

Den `X-EC-Debug` svarshuvud rapportering om en begäran gick cachelagrade finns i följande format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Begreppet i syntaxen ovan svar huvud definieras enligt följande:

Värde  | Beskrivning
-------| --------
JA    | Anger att det begärda innehållet berättigad för cachelagring.
NEJ     | Anger att det begärda innehållet var inte tillgänglig för cachelagring. Den här statusen kan vara något av följande skäl: <br /> -Kundspecifika konfiguration: en konfiguration som är specifika för ditt konto kan förhindra pop servrar cachelagring av en tillgång. Till exempel regelmotor kan förhindra att en tillgång cachelagras genom att aktivera funktionen kringgå Cache för kvalificerade begäranden.<br /> -Cache svarshuvuden: Den begärda tillgången Cache-Control och Expires-huvuden kan förhindra att POP-servrar från cachelagringen.
OKÄND | Anger att servrarna inte gick att utvärdera om den begärda tillgången har Cacheable ställs. Den här statusen uppstår vanligen när begäran nekas på grund av token-baserad autentisering.

### <a name="sample-response-header"></a>Exempel svarshuvud

Rubriken följande exempel anger om det begärda innehållet kan cachelagrade:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key-svarshuvud
Den `X-EC-Debug: x-ec-cache-key` svarshuvud anger fysiska cache-nyckeln som associeras med det begärda innehållet. En fysisk cachenyckel består av en sökväg som identifierar en tillgång för cachelagring. Servrarna kommer med andra ord, söka efter en cachelagrad version av en tillgång enligt dess sökväg som definierats av dess cachenyckel.

Den här fysiska cachenyckel som börjar med ett dubbelt snedstreck (/ /) följt av det protokoll som används för att begära innehåll (HTTP eller HTTPS). Det här protokollet följs av den relativa sökvägen till den begärda tillgången som börjar med innehålls åtkomstpunkt (till exempel _/000001/_).

Som standard http-plattformar som är konfigurerade för att använda *standard-cache*, vilket innebär att frågesträngar ignoreras av cachelagringen. Den här typen av konfiguration förhindrar i Cachenyckeln inklusive frågesträngdata.

Om en frågesträng registreras i cache-nyckel, den har konverteras till motsvarande hash och sedan infogas mellan namnet på den begärda tillgången och dess filnamnstillägget (till exempel tillgången&lt;hashvärde&gt;.html).

### <a name="response-header-format"></a>Svaret huvudformat

Den `X-EC-Debug` svarshuvud rapporterar fysiska cachenyckel informationen i följande format:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Exempel svarshuvud

Rubriken följande exempel visar den fysiska cache-nyckeln för det begärda innehållet:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Cache tillstånd svarshuvud
Den `X-EC-Debug: x-ec-cache-state` svarshuvud anger cache-tillståndet för det begärda innehållet när den begärdes.

### <a name="response-header-format"></a>Svaret huvudformat

Den `X-EC-Debug` svarshuvud rapporterar cache statusinformation i följande format:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Termer som används i syntaxen ovan svar huvud definieras enligt följande:

- MASeconds: Anger en maximal ålder (i sekunder) som definierats av Cache-Control-huvuden för det begärda innehållet.

- MATimePeriod: Konverterar värdet för maximal ålder (det vill säga MASeconds) till den ungefärliga motsvarigheten till en större enhet (till exempel, dagar). 

- UnixTime: Anger cache tidsstämpel för det begärda innehållet i Unix-tid (kallas även POSIX tids- eller Unix epok). Cache-tidsstämpel anger början datum/tid som en tillgång TTL beräknas. 

    Om den ursprungliga servern inte att använda en tredje parts HTTP cachelagring server eller om servern inte returnerar svarshuvud ålder, blir datumet och tiden när tillgången hämta eller verifiera alltid i cache-tidsstämpel. Annars POP-servrar ska använda fältet ålder för att beräkna tillgångens TTL-värde på följande sätt: hämtning/RevalidateDateTime - ålder.

- ddd dd MMM åååå: mm: ss GMT: Anger cache tidsstämpel för det begärda innehållet. Mer information finns i UnixTime termen ovan.

- CASeconds: Anger antalet sekunder som har förflutit sedan cache-tidsstämpel.

- RTSeconds: Anger antalet sekunder kvar som cachelagrat innehåll betraktas som ny. Det här värdet beräknas enligt följande: RTSeconds = maximal ålder - cachelagra ålder.

- RTTimePeriod: Konverterar återstående TTL-värdet (det vill säga RTSeconds) till den ungefärliga motsvarigheten till en större enhet (till exempel, dagar).

- ExpiresSeconds: Anger hur många sekunder kvar innan tidsvärdet anges i den `Expires` Svarsrubrik. Om den `Expires` svarshuvud ingår inte i svaret, så är värdet för den här termen *ingen*.

### <a name="sample-response-header"></a>Exempel svarshuvud

Rubriken följande exempel visar cache-status för det begärda innehållet vid den tidpunkt då den begärdes:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

