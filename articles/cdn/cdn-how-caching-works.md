---
title: Så här fungerar cachelagring | Microsoft Docs
description: Cachelagring är en process för att lagra data lokalt så att framtida begäranden om att data kan nås snabbt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: f82675f1e93a5471f98c1778e9394f9eaec1a07b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813049"
---
# <a name="how-caching-works"></a>Så här fungerar cachelagring

Den här artikeln innehåller en översikt över allmänna cachelagring begrepp och hur [Azure Content Delivery Network (CDN)](cdn-overview.md) använder cachelagring för att förbättra prestanda. Om du vill ha mer information om hur du anpassa funktionssättet för cachelagring på CDN-slutpunkten finns i [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md) och [Kontrollera Cachelagringsbeteendet med frågesträngar](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introduktion till cachelagring

Cachelagring är en process för att lagra data lokalt så att framtida begäranden om att data kan nås snabbt. I den vanligaste typen av cachelagring, web browser cachelagring, en web lagrar browser kopior av statiska data lokalt på en lokal hårddisk. Genom att använda cachelagring kan webbläsaren Undvik att göra flera turer till servern och i stället använder samma data lokalt, vilket sparar tid och resurser. Cachelagring är väl lämpade för att lokalt hantera små, statiska data som statiska bilder, CSS-filer och JavaScript-filer.

På samma sätt kan används cachelagring av content delivery network på edge-servrar nära användaren för att undvika begäranden reser tillbaka till ursprunget och svarstiden för slutanvändaren. Till skillnad från en web webbläsarens cache som används endast för en enskild användare, har en delad cache i CDN. I ett CDN delat cacheminne kan en fil som har begärts av en användare användas senare av andra användare, vilket avsevärt minskar antalet begäranden till den ursprungliga servern.

Dynamisk resurser som ändras ofta eller är unika för en enskild användare kan inte cachelagras. Dessa typer av resurser, kan dock dra nytta av dynamisk webbplats acceleration (DSA) optimering på Azure Content Delivery Network för att öka prestanda.

Cachelagring kan inträffa på flera nivåer mellan den ursprungliga servern och användaren:

- Webbserver: Använder en delad cache (för flera användare).
- Nätverk för innehållsleverans: Använder en delad cache (för flera användare).
- Internet-leverantör (ISP): Använder en delad cache (för flera användare).
- Webbläsare: Använder en privat cache (för en användare).

Varje cacheminne normalt hanterar en egen resource färskhet och utför verifieringen när en fil är inaktuell. Detta definieras i HTTP cachelagring specifikationen [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Resursen färskhet

Eftersom en cachelagrad resurs kan vara inaktuella eller inaktuella (jämfört med motsvarande resurs på den ursprungliga servern), är det viktigt att alla cachelagringsmekanism för att styra när innehållet uppdateras. För att spara tid och bandbreddsanvändning förbrukning, en cachelagrad resurs är inte jämfört med versionen på den ursprungliga servern varje gång den används. I stället så länge en cachelagrad resurs anses vara ny, det antas vara den mest aktuella versionen och skickas direkt till klienten. En cachelagrad resurs anses vara ny när dess ålder är mindre än det ålder eller den period som definieras av en cache-inställningen. Till exempel när en webbläsare laddar en webbsida, verifierar att varje cachelagrade resurs på hårddisken är nytt och läser in den. Om resursen inte är giltigt (inaktuella), en uppdaterad kopia har lästs in från servern.

### <a name="validation"></a>Validering

Om en resurs anses vara inaktuell, uppmanas ursprungsservern att verifiera den, det vill säga, avgör om data i cacheminnet fortfarande matchar vad som finns på den ursprungliga servern. Om filen har ändrats på den ursprungliga servern, uppdaterar cacheminnet dess versionen av resursen. Annars, om resursen är ny, data levereras direkt från cachen utan att verifiera den först.

### <a name="cdn-caching"></a>CDN-cachelagring

Cachelagring är integrerad i hur CDN fungerar för snabbare leverans och minska ursprung belastningen för statiska tillgångar som bilder, teckensnitt och videor. I CDN-cachelagring, statiska resurser lagras selektivt på strategiskt placerade servrar som är mer lokala för en användare och ger följande fördelar:

- Eftersom de flesta Internet-trafik är statiska (till exempel, bilder, teckensnitt och videor), minskar CDN-cachelagring svarstiden i nätverket genom att flytta innehållet närmare för användaren, vilket minskar avståndet som data skickas.

- Genom att avlasta arbete till ett nätverk för Innehållsleverans, kan cachelagring minska nätverkstrafiken och belastningen på den ursprungliga servern. Detta minskar kostnader och kraven för programmet, även om det finns många användare.

Liknar hur cachelagring har implementerats i en webbläsare, du kan styra hur cachelagring utförs i ett nätverk för Innehållsleverans genom att skicka cachelagringsdirektiv. Cachelagringsdirektiv är HTTP-huvuden som läggs normalt av den ursprungliga servern. Även om de flesta av dessa huvuden ursprungligen var avsedda att hantera cachelagring i klientwebbläsare, används de nu också av alla mellanliggande cacheminnen, till exempel CDN-nät. 

Två huvuden kan användas för att definiera cache färskhet: `Cache-Control` och `Expires`. `Cache-Control` är mer aktuell och har företräde framför `Expires`om båda finns. Det finns två typer av rubriker som används för verifiering (kallas systemhälsoverifierare): `ETag` och `Last-Modified`. `ETag` är mer aktuell och har företräde framför `Last-Modified`om båda har definierats.  

## <a name="cache-directive-headers"></a>Cachelagringsdirektiv

> [!IMPORTANT]
> Som standard en Azure CDN-slutpunkt som är optimerad för DSA ignorerar cachelagringsdirektiv och kringgår cachelagring. För **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler, kan du justera hur en Azure CDN-slutpunkt behandlar dessa rubriker med hjälp av [cachelagringsregler för CDN](cdn-caching-rules.md)att aktivera cachelagring. För **Azure CDN Premium från Verizon** profiler kan du använda den [regelmotor](cdn-rules-engine.md) att aktivera cachelagring.

Azure CDN stöder de följande HTTP-cachelagringsdirektiv, som definierar cachelagringens varaktighet och delning av cache.

**Cache-Control:**
- Introducerades i HTTP 1.1 att ge web utgivare mer kontroll över deras innehåll och för att begränsningarna i den `Expires` rubrik.
- Åsidosätter den `Expires` rubrik, om både den och `Cache-Control` har definierats.
- När de används i en HTTP-begäran från klienten till CDN-POP `Cache-Control` ignoreras av alla Azure CDN-profiler som standard.
- När de används i ett HTTP-svar från klienten till CDN POP:
     - **Azure CDN Standard/Premium från Verizon** och **Azure CDN Standard från Microsoft** har stöd för alla `Cache-Control` direktiv.
     - **Azure CDN Standard från Akamai** stöder endast följande `Cache-Control` direktiv; alla övriga filer ignoreras:
         - `max-age`: Ett cacheminne kan lagra innehållet för hur många sekunder som angetts. Till exempel `Cache-Control: max-age=5`. Detta direktiv anger längsta tid som innehållet anses vara uppdaterad.
         - `no-cache`: Cachelagra innehållet, men kontrollera innehållet varje gång innan levereras från cachen. Motsvarar `Cache-Control: max-age=0`.
         - `no-store`: Aldrig cachelagra innehållet. Ta bort innehåll om det tidigare sparade.

**Upphör att gälla:**
- Äldre rubrik som introducerades i HTTP 1.0; stöds för bakåtkompatibilitet.
- Använder en datumbaserade förfallotid med andra precision. 
- Liknar `Cache-Control: max-age`.
- Används när `Cache-Control` finns inte.

**Pragma:**
   - Hanteras inte av Azure CDN kan som standard.
   - Äldre rubrik som introducerades i HTTP 1.0; stöds för bakåtkompatibilitet.
   - Används som en rubrik för begäran av klienten med följande direktiv: `no-cache`. Detta direktiv instruerar servern att leverera en ny version av resursen.
   - `Pragma: no-cache` motsvarar `Cache-Control: no-cache`.

## <a name="validators"></a>Systemhälsoverifierare

När cachen är inaktuell, används HTTP cache systemhälsoverifierare att jämföra den cachelagrade versionen av en fil med versionen på den ursprungliga servern. **Azure CDN Standard/Premium från Verizon** stöder både `ETag` och `Last-Modified` systemhälsoverifierare som standard medan **Azure CDN Standard från Microsoft** och **Azure CDN Standard från Akamai** stöder endast `Last-Modified` som standard.

**ETag:**
- **Azure CDN Standard/Premium från Verizon** stöder `ETag` som standard medan **Azure CDN Standard från Microsoft** och **Azure CDN Standard från Akamai** inte.
- `ETag` definierar en sträng som är unik för varje fil och en version av en fil. Till exempel `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introducerades i HTTP 1.1 och är nyare än `Last-Modified`. Användbart när senaste ändringsdatum är svårt att avgöra.
- Har stöd för både stark verifiering och svaga validering; Azure CDN stöder dock endast stark verifiering. För stark verifiering två garantier måste vara byte för byte identiska. 
- En cache verifierar en fil som använder `ETag` genom att skicka en `If-None-Match` huvud med en eller flera `ETag` systemhälsoverifierare i begäran. Till exempel `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Om serverns versionen matchar en `ETag` verifieraren på listan skickar den statuskod 304 (har inte ändrats) i sitt svar. Om versionen inte är samma, svarar servern med statuskoden 200 (OK) och den uppdaterade resursen.

**Senaste ändring:**
- För **Azure CDN Standard/Premium från Verizon** , `Last-Modified` används om `ETag` ingår inte i HTTP-svaret. 
- Anger det datum och tid då den ursprungliga servern har fastställt resursen senast ändrades. Till exempel `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- En cache verifierar en fil med hjälp av `Last-Modified` genom att skicka en `If-Modified-Since` huvud med ett datum och tid i begäran. Ursprungsservern Jämför detta datum med den `Last-Modified` rubriken för den senaste resursen. Om resursen inte har ändrats sedan den angivna tiden, returnerar servern statuskod 304 (har inte ändrats) i sitt svar. Om resursen har ändrats, returnerar servern status code 200 (OK) och den uppdaterade resursen.

## <a name="determining-which-files-can-be-cached"></a>Avgör vilka filer som kan cachelagras

Inte alla resurser som kan cachelagras. I följande tabell visas vilka resurser kan cachelagras, beroende på vilken typ av HTTP-svar. Resurser som levereras med HTTP-svar som inte uppfyller dessa villkor kan inte cachelagras. För **Azure CDN Premium från Verizon** , du kan använda regelmotorn för att anpassa vissa av dessa villkor.

|                   | Azure CDN från Microsoft          | Azure CDN från Verizon | Azure CDN från Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP-statuskoder | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP-metoder      | GET, HEAD                         | HÄMTA                    | HÄMTA                          |
| Filstorleksbegränsning  | 300 GB                            | 300 GB                 | – Allmän leveransoptimering: 1.8 GB<br />-Media strömmande optimeringar: 1.8 GB<br />-Optimering av stora filer: 150 GB |

För **Azure CDN Standard från Microsoft** cachelagring för att fungera på en resurs, den ursprungliga servern måste ha stöd för alla HEAD och GET HTTP-begäranden och innehållslängden värdena måste vara samma för alla huvud- och GET HTTP-svar för tillgången. Den ursprungliga servern för en HEAD-begäran måste ha stöd för HEAD-begäran och måste svara med samma rubriker som om den fick en GET-begäran.

## <a name="default-caching-behavior"></a>Beteende för standardcachelagring

I följande tabell beskrivs Standardinställningarna för cachelagring beteende för Azure CDN-produkter och deras optimeringar.

|    | Microsoft: Allmän webbleverans | Verizon: Allmän webbleverans | Verizon: DSA | Akamai: Allmän webbleverans | Akamai: DSA | Akamai: Nedladdning av stora filer | Akamai: allmänna eller VOD-direktuppspelning |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Respektera ursprung**       | Ja    | Ja   | Nej   | Ja    | Nej   | Ja   | Ja    |
| **CDN cachelagringens varaktighet** | 2 dagar |7 dagar | Ingen | 7 dagar | Ingen | 1 dag | 1 år |

**Respektera ursprung**: Anger om du måste respektera stöds cachelagringsdirektiv om de finns i HTTP-svaret från den ursprungliga servern.

**CDN-cachevaraktighet**: Anger hur lång tid som en resurs cachelagras på Azure CDN. Men om **respektera ursprung** är Ja och HTTP-svaret från den ursprungliga servern innehåller rubriken cache-direktiv `Expires` eller `Cache-Control: max-age`, Azure CDN använder duration-värde som anges av rubriken i stället. 

## <a name="next-steps"></a>Nästa steg

- Läs hur du anpassar och åsidosätta standardinställningen beteende på CDN via cachelagringsregler för cachelagring i [Kontrollera Cachelagringsbeteendet med cachelagringsregler](cdn-caching-rules.md). 
- Läs hur du använder frågesträngar att styra funktionssätt för cachelagring i [Kontrollera Cachelagringsbeteendet med frågesträngar](cdn-query-string.md).



