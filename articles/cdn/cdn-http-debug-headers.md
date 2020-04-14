---
title: X-EC-Debug HTTP-huvuden för Azure CDN-regler motor | Microsoft-dokument
description: X-EC-Debug-felsökningslagringsfachebegäranden innehåller ytterligare information om cacheprincipen som tillämpas på den begärda tillgången. Dessa rubriker är specifika för Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 52aae3bdd2fe82eea6cbd500723192c88c293a1e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260504"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC-Debug HTTP-huvuden för Azure CDN-regelmotor
Felsökningscachebegäranshuvudet `X-EC-Debug`ger ytterligare information om cacheprincipen som tillämpas på den begärda tillgången. Dessa rubriker är specifika för **Azure CDN Premium från** Verizon-produkter.

## <a name="usage"></a>Användning
Svaret som skickas från POP-servrarna `X-EC-Debug` till en användare innehåller huvudet endast när följande villkor är uppfyllda:

- Funktionen [Felsökningscachen svarshuvuden](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) har aktiverats på regelmotorn för den angivna begäran.
- Den angivna begäran definierar den uppsättning felsökningscachesvarshuvuden som ska inkluderas i svaret.

## <a name="requesting-debug-cache-information"></a>Begära felsökningscacheinformation
Använd följande direktiv i den angivna begäran för att definiera felsökningscachen som ska ingå i svaret:

Begärandehuvud | Beskrivning |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Statuskod för cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-fjärrkontroll | [Statuskod för cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cachebara](#cacheable-response-header)
X-EC-Debug: x-ec-cache-nyckel | [Cache-nyckel](#cache-key-response-header)
X-EC-Debug: x-ec-cache-tillstånd | [Cachetillstånd](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Felsökningscachens svarshuvuden kan begäras genom att inkludera följande huvud och de angivna direktiven i begäran:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Exempel på X-EC-Felsökningshuvud

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Information om cachestatuskod
X-EC-Debug-svarshuvudet kan identifiera en server och hur den hanterade svaret via följande direktiv:

Huvud | Beskrivning
-------|------------
X-EC-Debug: x-ec-cache | Det här huvudet rapporteras när innehåll dirigeras via CDN. Den identifierar POP-servern som uppfyllde begäran.
X-EC-Debug: x-ec-cache-fjärrkontroll | Det här huvudet rapporteras endast när det begärda innehållet cachelagrades på en ursprungssköldserver eller en ADN-gatewayserver.

### <a name="response-header-format"></a>Format för svarsrubrik

X-EC-Debug-huvudet rapporterar cachestatuskodsinformation i följande format:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Termerna som används i syntaxen för svarshuvudet ovan definieras på följande sätt:
- Statuskod: Anger hur det begärda innehållet hanterades av CDN, som representeras via en cachestatuskod.
    
    Den TCP_DENIED statuskoden kan rapporteras i stället för INGEN när en obehörig begäran nekas på grund av tokenbaserad autentisering. STATUSkoden NONE fortsätter dock att användas när cachestatusrapporter eller råloggdata visas.

- Plattform: Anger den plattform där innehållet begärdes. Följande koder gäller för det här fältet:

    Kod  | Plattform
    ------| --------
    Ecacc (europeiska) | HTTP Stor
    Ecs   | HTTP Liten
    Ecd   | Application Delivery Network (ADN)

- POP: Anger [pop](cdn-pop-abbreviations.md) som hanterade begäran. 

### <a name="sample-response-headers"></a>Exempel på svarsrubriker

Följande exempelrubriker innehåller information om cachestatuskod för en begäran:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Svarshuvud för cachelagrar
Svarshuvudet `X-EC-Debug: x-ec-check-cacheable` anger om det begärda innehållet kan ha cachelagrats.

Det här svarshuvudet anger inte om cachelagring ägde rum. I stället anges om begäran var berättigad till cachelagring.

### <a name="response-header-format"></a>Format för svarsrubrik

Svarshuvudet `X-EC-Debug` som rapporterar om en begäran kunde ha cachelagrats är i följande format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termen som används i syntaxen för svarshuvudet ovan definieras på följande sätt:

Värde  | Beskrivning
-------| --------
JA    | Anger att det begärda innehållet var kvalificerat för cachelagring.
NO     | Anger att det begärda innehållet inte var berättigat för cachelagring. Denna status kan bero på något av följande: <br /> - Kundspecifik konfiguration: En konfiguration som är specifik för ditt konto kan hindra popservrarna från att cachelagring av en tillgång. Regelmotor kan till exempel förhindra att en tillgång cachelagras genom att aktivera funktionen Bypass-cache för kvalificerade begäranden.<br /> - Cache svarshuvuden: Den begärda tillgångens Cache-Control och upphör att gälla rubriker kan hindra POP-servrar från att cachelagra den.
UNKNOWN | Anger att servrarna inte kunde bedöma om den begärda tillgången kunde cachelagras. Den här statusen inträffar vanligtvis när begäran nekas på grund av tokenbaserad autentisering.

### <a name="sample-response-header"></a>Exempel på svarshuvud

Följande exempelsvarshuvud anger om det begärda innehållet kan ha cachelagrats:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Svarshuvud för cachetangent
Svarshuvudet `X-EC-Debug: x-ec-cache-key` anger den fysiska cachenyckel som är associerad med det begärda innehållet. En fysisk cachenyckel består av en sökväg som identifierar en tillgång för cachelagring. Med andra ord kommer servrarna att söka efter en cachelagd version av en tillgång enligt dess sökväg som definieras av dess cache-nyckel.

Den här fysiska cachenyckeln börjar med ett dubbelt snedstreck (//) följt av det protokoll som används för att begära innehållet (HTTP eller HTTPS). Det här protokollet följs av den relativa sökvägen till den begärda tillgången, som börjar med åtkomstpunkten för innehåll (till exempel _/000001/_).

Som standard är HTTP-plattformar konfigurerade för att använda *standardcache,* vilket innebär att frågesträngar ignoreras av cachelagringsmekanismen. Den här typen av konfiguration förhindrar att cachetangenten inkluderar frågesträngdata.

Om en frågesträng registreras i cachenyckeln konverteras den till dess hash-motsvarighet och infogas sedan mellan namnet på&lt;den begärda tillgången och filtillägget (till exempel resurshh-värdet&gt;.html).

### <a name="response-header-format"></a>Format för svarsrubrik

Svarshuvudet `X-EC-Debug` rapporterar fysisk cachenyckelinformation i följande format:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Exempel på svarshuvud

Följande exempelsvarshuvud anger den fysiska cachenyckeln för det begärda innehållet:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Svarshuvud för cachetillstånd
Svarshuvudet `X-EC-Debug: x-ec-cache-state` anger cachetillståndet för det begärda innehållet när det begärdes.

### <a name="response-header-format"></a>Format för svarsrubrik

Svarshuvudet `X-EC-Debug` rapporterar cachetillståndsinformation i följande format:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Termerna som används i syntaxen för svarshuvudet ovan definieras på följande sätt:

- MASeconds: Anger maxåldern (i sekunder) enligt definitionen i det begärda innehållets cachekontrollrubriker.

- MATimePeriod: Konverterar värdet för maxålder (det villan, MASeconds) till ungefärlig motsvarighet till en större enhet (till exempel dagar). 

- UnixTime: Anger cachetidsstämpeln för det begärda innehållet i Unix-tid (kallas även POSIX-tid eller Unix-epok). Tidsstämpeln för cache anger startdatum/tid från vilket en tillgångs TTL ska beräknas. 

    Om ursprungsservern inte använder en HTTP-cacheserver från tredje part eller om servern inte returnerar ålderssvarshuvudet, kommer tidsstämpeln för cache alltid att vara det datum/den tidpunkt då tillgången hämtades eller återställdes. Annars använder POP-servrarna fältet Ålder för att beräkna tillgångens TTL enligt följande: Hämtning/RevalidateDateTime - Ålder.

- ddd, dd MMM yyyy HH:mm:ss GMT: Anger cachetidsstämpeln för det begärda innehållet. För mer information, se UnixTime termen ovan.

- CASekunder: Anger hur många sekunder som har förflutit sedan tidsstämpeln för cachen.

- RTSekunder: Anger hur många sekunder som det cachelagrade innehållet kommer att betraktas som färskt. Detta värde beräknas på följande sätt: RTSeconds = max-age - cache ålder.

- RTTimePeriod: Konverterar det återstående TTL-värdet (det vill säga RTSeconds) till ungefärlig motsvarighet till en större enhet (till exempel dagar).

- FörfallerSekunder: Anger antalet sekunder som återstår före det datum/den `Expires` tid som anges i svarshuvudet. Om `Expires` svarshuvudet inte ingick i svaret är värdet för den här termen *inget*.

### <a name="sample-response-header"></a>Exempel på svarshuvud

Följande exempelsvarshuvud anger cachetillståndet för det begärda innehållet när det begärdes:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

