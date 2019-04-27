---
title: X-EG-Debug HTTP-huvuden för Azure CDN regelmotor | Microsoft Docs
description: Rubriken X-EG-Debug debug cache-begäran innehåller ytterligare information om den princip som tillämpas på den begärda tillgången. Dessa huvuden är specifika för Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736980"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EG-Debug HTTP-huvuden för Azure CDN regelmotor
Begärandehuvudet för debug cache `X-EC-Debug`, tillhandahåller ytterligare information om den princip som tillämpas på den begärda tillgången. Dessa huvuden är specifika för **Azure CDN Premium från Verizon** produkter.

## <a name="usage"></a>Användning
Svaret skickas från POP-servrar till en användare innehåller den `X-EC-Debug` rubrik endast när följande villkor är uppfyllda:

- Den [felsöka Cache svarshuvuden funktionen](cdn-rules-engine-reference-features.md#debug-cache-response-headers) har aktiverats på regelmotor för den angivna begäran.
- Den angivna begäran definierar de svarshuvuden för debug cache som kommer att inkluderas i svaret.

## <a name="requesting-debug-cache-information"></a>Begär felsökningsinformation om cachelagring
Använd följande direktiv i den angivna begäran för att definiera cache felsökningsinformation som kommer att inkluderas i svaret:

Begärandehuvud | Beskrivning |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Cache-statuskod](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Cache-statuskod](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Komma](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Cache-key](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Cache-tillstånd](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Felsöka cachesvar huvuden kan begäras genom att inkludera följande huvud och de angivna direktiv i begäran:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Exemplet X-EG-Debug-huvud

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Cache statusinformation för kod
Rubriken X-EG-Debug kan identifiera en server och hur den hanterade svar via följande direktiv:

Huvud | Beskrivning
-------|------------
X-EC-Debug: x-ec-cache | Den här rubriken rapporteras varje gång innehållet dirigeras via CDN. Den identifierar den POP-server som uppfyllt begäran.
X-EC-Debug: x-ec-cache-remote | Den här rubriken rapporteras endast när det begärda innehållet har cachelagrats på en shield ursprungsserver eller en ADN gateway-server.

### <a name="response-header-format"></a>Svar-huvudformat

Rubriken X-EG-Debug rapporterar cache kod statusinformation i följande format:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

De termer som används i ovanstående svar rubrik syntax definieras enligt följande:
- StatusCode: Anger hur det begärda innehållet har hanterats av CDN, som visas via en cache-statuskod.
    
    Statuskoden TCP_DENIED kan rapporteras i stället för att ingen om en obehörig begäran nekas på grund av tokenbaserad autentisering. Däremot fortsätter NONE statuskoden som ska användas vid visning av Cachestatus rapporter eller raw loggdata.

- Plattform: Anger plattformen där innehållet begärdes. Följande koder är giltiga för det här fältet:

    Kod  | Plattform
    ------| --------
    ECAcc | HTTP-stor
    ECS   | HTTP-liten
    ECD   | Application Delivery Network (ADN)

- POP: Anger den [POP](cdn-pop-abbreviations.md) som hanteras begäran. 

### <a name="sample-response-headers"></a>Exemplet svarshuvuden

Följande exempel huvuden innehåller cache kod statusinformation för en begäran:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Komma-svarshuvud
Den `X-EC-Debug: x-ec-check-cacheable` svarshuvud anger om det begärda innehållet kan cachelagrade.

Den här svarshuvud anger inte om cachelagring ägde rum. I stället anger om begäran var berättigad för cachelagring.

### <a name="response-header-format"></a>Svar-huvudformat

Den `X-EC-Debug` svarshuvud rapportering om en begäran kan cachelagrade finns i följande format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Begreppet i ovanstående svar rubrik syntax definieras enligt följande:

Värde  | Beskrivning
-------| --------
JA    | Anger att det begärda innehållet berättigade för cachelagring.
NO     | Anger att det begärda innehållet har inte berättigade för cachelagring. Den här statusen kan vara något av följande orsaker: <br /> -Kundspecifika konfiguration: En konfiguration som är specifika för ditt konto kan hindra pop-servrar från cachelagring av en tillgång. Regelmotor kan till exempel förhindra att en tillgång att cachelagras genom att aktivera funktionen kringgå Cache för kvalificerade begäranden.<br /> -Cachelagra svarshuvuden: Den begärda tillgången Cache-Control och Expires-huvuden kan förhindra att POP-servrar från cachelagringen.
OKÄNT | Anger att servrarna inte gick att utvärdera om den begärda tillgången har komma. Den här statusen visas normalt när begäran nekas på grund av tokenbaserad autentisering.

### <a name="sample-response-header"></a>Exempel-svarshuvud

Följande exempel svarshuvudet anger om det begärda innehållet kan cachelagrade:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cachenyckel svarshuvud
Den `X-EC-Debug: x-ec-cache-key` svarshuvud anger fysiska cache-nyckel som är associerade med det begärda innehållet. En fysisk cachenyckel består av en sökväg som identifierar en tillgång för cachelagring. Med andra ord kontrollerar servrar för en cachelagrad version av en tillgång enligt dess sökväg som definieras av dess cachenyckel.

Den här fysiska cachenyckel börjar med dubbla snedstreck (/ /) följt av det protokoll som används för att begära innehåll (HTTP eller HTTPS). Det här protokollet följs av den relativa sökvägen till den begärda tillgången som börjar med den innehåll åtkomstpunkten (till exempel _/000001/_).

Som standard HTTP plattformar är konfigurerade för att använda *standard-cache*, vilket innebär att frågesträngar ignoreras av mekanismen för cachelagring. Den här typen av konfiguration förhindrar att cache-nyckeln, inklusive fråga strängdata.

Om en frågesträng registreras i cache-key, har den konverteras till motsvarande hash och infogas mellan namnet på den begärda tillgången och dess filnamnstillägget (till exempel tillgången&lt;hashvärde&gt;.html).

### <a name="response-header-format"></a>Svar-huvudformat

Den `X-EC-Debug` svarshuvud rapporterar fysiska cachenyckel information i följande format:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Exempel-svarshuvud

Följande exempel svarshuvudet anger den fysiska cache-nyckeln för det begärda innehållet:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Svarshuvud för cache-tillstånd
Den `X-EC-Debug: x-ec-cache-state` svarshuvud anger cache-tillståndet för det begärda innehållet när den begärdes.

### <a name="response-header-format"></a>Svar-huvudformat

Den `X-EC-Debug` svarshuvud rapporterar cache statusinformation i följande format:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

De termer som används i ovanstående svar rubrik syntax definieras enligt följande:

- MASeconds: Anger max-age (i sekunder) som definieras av Cache-Control-huvuden för det begärda innehållet.

- MATimePeriod: Konverterar max-age-värde (det vill säga MASeconds) till det ungefärliga motsvarande en större enhet (till exempel, dagar). 

- UnixTime: Anger cache-tidsstämpel för det begärda innehållet i Unix-tiden (alias) POSIX tid eller Unix epoch). Cache-tidsstämpeln visar från datum/tid som en tillgång TTL ska beräknas. 

    Om ursprungsservern inte att använda en tredje parts HTTP cachelagring server eller om servern inte returnerar svarshuvudet ålder, blir datum och tid när tillgången har hämtats eller verifiera om alltid i cache-tidsstämpel. I annat fall använder POP-servrar fältet ålder för att beräkna tillgångens TTL på följande sätt: Hämtning av filer/RevalidateDateTime - ålder.

- ddd dd mm-åååå: mm: ss GMT: Anger cache-tidsstämpel för det begärda innehållet. Mer information finns i UnixTime termen ovan.

- CASeconds: Anger antalet sekunder som har förflutit sedan cache-tidsstämpel.

- RTSeconds: Anger antalet sekunder som återstår som cachelagrat innehåll kommer kan anses vara aktuell. Det här värdet beräknas enligt följande: RTSeconds = max-age - cachelagra ålder.

- RTTimePeriod: Konverterar återstående TTL-värdet (det vill säga RTSeconds) till det ungefärliga motsvarande en större enhet (till exempel, dagar).

- ExpiresSeconds: Anger hur många sekunder kvar innan det datum/tid som anges i den `Expires` svarshuvudet. Om den `Expires` svarshuvud ingår inte i svaret och sedan värdet för den här termen är *ingen*.

### <a name="sample-response-header"></a>Exempel-svarshuvud

Följande exempel svarshuvudet anger cache-tillståndet för det begärda innehållet vid den tidpunkt då den begärdes:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

