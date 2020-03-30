---
title: Hur cachelagring fungerar | Microsoft-dokument
description: Cachelagring är processen att lagra data lokalt så att framtida begäranden om dessa data kan nås snabbare.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 92d93fbf9fa2f8df15acb62802d7ac53db836dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593851"
---
# <a name="how-caching-works"></a>Så här fungerar cachelagring

Den här artikeln innehåller en översikt över allmänna cachelagringsbegrepp och hur [CDN (Azure Content Delivery Network)](cdn-overview.md) använder cachelagring för att förbättra prestanda. Om du vill veta mer om hur du anpassar cachelagringsbeteendet på CDN-slutpunkten läser du [Kontrollera Azure CDN-cachelagring med cachelagringsregler](cdn-caching-rules.md) och [Kontroll azure CDN-cachelagring med frågesträngar](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introduktion till cachelagring

Cachelagring är processen att lagra data lokalt så att framtida begäranden om dessa data kan nås snabbare. I den vanligaste typen av cachelagring, cachelagring i webbläsaren, lagrar en webbläsare kopior av statiska data lokalt på en lokal hårddisk. Genom att använda cachelagring kan webbläsaren undvika att göra flera tur- och returresor till servern och i stället komma åt samma data lokalt, vilket sparar tid och resurser. Cachelagring är väl lämpad för lokalt hantera små, statiska data som statiska bilder, CSS-filer och JavaScript-filer.

På samma sätt används cachelagring av ett innehållsleveransnätverk på edge-servrar nära användaren för att undvika begäranden som reser tillbaka till ursprunget och minskar slutanvändarnas svarstid. Till skillnad från en webbläsares cache, som endast används för en enskild användare, har CDN en delad cache. I en delad CDN-cache kan en fil som begärs av en användare nås senare av andra användare, vilket minskar antalet begäranden till ursprungsservern avsevärt.

Dynamiska resurser som ändras ofta eller är unika för en enskild användare kan inte cachelagras. Dessa typer av resurser kan dock dra nytta av DSA-optimering (Dynamic Site Acceleration) i Azure Content Delivery Network för prestandaförbättringar.

Cachelagring kan ske på flera nivåer mellan ursprungsservern och slutanvändaren:

- Webbserver: Använder en delad cache (för flera användare).
- Nätverk för innehållsleverans: Använder en delad cache (för flera användare).
- Internet-leverantör (ISP): Använder en delad cache (för flera användare).
- Webbläsare: Använder en privat cache (för en användare).

Varje cache hanterar vanligtvis sin egen resurs färskhet och utför validering när en fil är inaktuell. Det här problemet definieras i HTTP-cachelagringsspecifikationen [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Resurs fräschör

Eftersom en cachelagrad resurs kan vara inaktuell eller inaktuell (jämfört med motsvarande resurs på ursprungsservern) är det viktigt att alla cachelagringsmekanismer styr när innehållet uppdateras. För att spara tid och bandbreddsförbrukning jämförs inte en cachelagrad resurs med versionen på ursprungsservern varje gång den används. I stället, så länge en cachelagrad resurs anses vara färsk, antas den vara den senaste versionen och skickas direkt till klienten. En cachelagrad resurs anses vara färsk när dess ålder är mindre än den ålder eller period som definieras av en cacheinställning. När en webbläsare till exempel läser in en webbsida igen verifierar den att varje cachelagd resurs på hårddisken är färsk och läser in den. Om resursen inte är ny (inaktuell) läses en uppdaterad kopia in från servern.

### <a name="validation"></a>Validering

Om en resurs anses vara inaktuell uppmanas ursprungsservern att validera den, det vill än om data i cacheminnet fortfarande matchar vad som finns på ursprungsservern. Om filen har ändrats på ursprungsservern uppdaterar cachen sin version av resursen. Annars, om resursen är färsk, levereras data direkt från cacheminnet utan att validera den först.

### <a name="cdn-caching"></a>CDN-cachelagring

Cachelagring är en integrerad del av hur ett CDN fungerar för att påskynda leveransen och minska ursprungsbelastningen för statiska tillgångar som bilder, teckensnitt och videor. I CDN-cachelagring lagras statiska resurser selektivt på strategiskt placerade servrar som är mer lokala för en användare och erbjuder följande fördelar:

- Eftersom de flesta webbtrafik är statisk (till exempel bilder, teckensnitt och videor), minskar CDN-cachelagring nätverksfördröjningen genom att flytta innehåll närmare användaren, vilket minskar avståndet som data färdas.

- Genom att avlasta arbetet till en CDN kan cachelagring minska nätverkstrafiken och belastningen på ursprungsservern. Detta minskar kostnaderna och resurskraven för programmet, även när det finns ett stort antal användare.

På samma sätt som cachelagring implementeras i en webbläsare kan du styra hur cachelagring utförs i ett CDN genom att skicka cachedirektivrubriker. Cachedirektivhuvuden är HTTP-huvuden som vanligtvis läggs till av ursprungsservern. Även om de flesta av dessa rubriker ursprungligen var utformade för att ta itu med cachelagring i klientwebbläsare, används de nu också av alla mellanliggande cacheminnen, till exempel CDN.Although most of these headers were originally designed to address caching in client browsers, they are now also used by all intermediate caches, such as CDNs. 

Två rubriker kan användas för att `Cache-Control` definiera `Expires`cachens färskhet: och . `Cache-Control`är mer aktuell och `Expires`har företräde framför , om båda finns. Det finns också två typer av rubriker som används `ETag` `Last-Modified`för validering (kallas validerare): och . `ETag`är mer aktuell och `Last-Modified`har företräde framför , om båda är definierade.  

## <a name="cache-directive-headers"></a>Rubriker i cachedirektiv

> [!IMPORTANT]
> Som standard ignorerar en Azure CDN-slutpunkt som är optimerad för DSA cachedirektivhuvuden och kringgår cachelagring. För **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler** kan du justera hur en Azure CDN-slutpunkt behandlar dessa huvuden med hjälp av [CDN-cachelagringsregler](cdn-caching-rules.md) för cachelagring. Endast för **Azure CDN Premium från Verizon-profiler** använder du [regelmotorn](cdn-rules-engine.md) för att aktivera cachelagring.

Azure CDN stöder följande HTTP-cachedirektivhuvuden, som definierar cachevaraktighet och cachedelning.

**Cache-kontroll:**
- Infördes i HTTP 1.1 för att ge webbutgivare mer kontroll över `Expires` sitt innehåll och för att ta itu med begränsningarna i huvudet.
- Åsidosätter `Expires` huvudet, om både `Cache-Control` det och definieras.
- När den används i en HTTP-begäran från `Cache-Control` klienten till CDN POP, ignoreras av alla Azure CDN-profiler, som standard.
- När den används i ett HTTP-svar från klienten till CDN POP:
     - **Azure CDN Standard/Premium från Verizon** och **Azure CDN Standard från Microsoft** stöder alla `Cache-Control` direktiv.
     - **Azure CDN Standard från Akamai** stöder endast följande `Cache-Control` direktiv. alla andra ignoreras:
         - `max-age`: En cache kan lagra innehållet för det antal sekunder som anges. Till exempel `Cache-Control: max-age=5`. I detta direktiv anges den längsta tid som innehållet anses vara färskt.
         - `no-cache`: Cacheminnet, men validera innehållet varje gång innan du levererar det från cachen. Motsvarar `Cache-Control: max-age=0`.
         - `no-store`: Cachelagra aldrig innehållet. Ta bort innehåll om det har lagrats tidigare.

**Upphör:**
- Äldre huvud som introducerades i HTTP 1.0. stöd för bakåtkompatibilitet.
- Använder en datumbaserad förfallotid med andra precisionen. 
- Liknar `Cache-Control: max-age`.
- Används `Cache-Control` när det inte finns.

**Pragma:**
   - Inte hedras av Azure CDN, som standard.
   - Äldre huvud som introducerades i HTTP 1.0. stöd för bakåtkompatibilitet.
   - Används som klientbegäranhuvud med följande `no-cache`direktiv: . Detta direktiv instruerar servern att leverera en ny version av resursen.
   - `Pragma: no-cache`motsvarar `Cache-Control: no-cache`.

## <a name="validators"></a>Validators

När cacheminnet är inaktuellt används HTTP-cache validators för att jämföra den cachelagrade versionen av en fil med versionen på ursprungsservern. **Azure CDN Standard/Premium** från `ETag` `Last-Modified` Verizon stöder både och validerare som standard, medan **Azure CDN Standard från Microsoft** och Azure **CDN Standard från Akamai** endast `Last-Modified` stöder som standard.

**Etag:**
- **Azure CDN Standard/Premium** `ETag` från Verizon stöder som standard, medan **Azure CDN Standard från Microsoft** och Azure **CDN Standard från Akamai** inte gör det.
- `ETag`definierar en sträng som är unik för varje fil och version av en fil. Till exempel `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introducerades i HTTP 1.1 och `Last-Modified`är mer aktuell än . Användbart när det senast ändrade datumet är svårt att avgöra.
- Stöder både stark validering och svag validering. Azure CDN stöder dock endast stark validering. För stark validering måste de två resursrepresentationerna vara identiska byte för byte. 
- En cache validerar en `ETag` fil `If-None-Match` som använder genom `ETag` att skicka ett huvud med en eller flera validerare i begäran. Till exempel `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Om serverns version matchar `ETag` en validerare i listan skickas statuskod 304 (Inte ändrad) i svaret. Om versionen är annorlunda svarar servern med statuskod 200 (OK) och den uppdaterade resursen.

**Senast ändrad:**
- Endast för **Azure CDN Standard/Premium från Verizon** `Last-Modified` används om `ETag` den inte är en del av HTTP-svaret. 
- Anger datum och tid då ursprungsservern har fastställt att resursen senast ändrades. Till exempel `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- En cache validerar `Last-Modified` en fil `If-Modified-Since` med hjälp av genom att skicka ett huvud med ett datum och en tid i begäran. Ursprungsservern jämför det `Last-Modified` datumet med huvudet på den senaste resursen. Om resursen inte har ändrats sedan den angivna tiden returnerar servern statuskod 304 (Inte ändrad) i sitt svar. Om resursen har ändrats returnerar servern statuskod 200 (OK) och den uppdaterade resursen.

## <a name="determining-which-files-can-be-cached"></a>Bestämma vilka filer som kan cachelagras

Alla resurser kan inte cachelagras. I följande tabell visas vilka resurser som kan cachelagras, baserat på typen av HTTP-svar. Resurser som levereras med HTTP-svar som inte uppfyller alla dessa villkor kan inte cachelagras. Endast för **Azure CDN Premium från Verizon** kan du använda regelmotorn för att anpassa vissa av dessa villkor.

|                   | Azure CDN från Microsoft          | Azure CDN från Verizon | Azure CDN från Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP-statuskoder | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP-metoder      | FÅ, HUVUD                         | HÄMTA                    | HÄMTA                          |
| Begränsningar för filstorlek  | 300 GB                            | 300 GB                 | - Allmän optimering av webbleverans: 1,8 GB<br />- Optimering av direktuppspelning av media: 1,8 GB<br />- Stor filoptimering: 150 GB |

För **att Azure CDN Standard från** Microsoft-cachelagring ska fungera på en resurs måste ursprungsservern stödja alla HEAD- och GET HTTP-begäranden och värdena för innehållslängd måste vara desamma för alla HEAD- och GET HTTP-svar för tillgången. För en HEAD-begäran måste ursprungsservern stödja HEAD-begäran och svara med samma rubriker som om den hade fått en GET-begäran.

## <a name="default-caching-behavior"></a>Standardåteruppförande

I följande tabell beskrivs standardcachelagringsbeteendet för Azure CDN-produkter och deras optimeringar.

|    | Microsoft: Allmän webbleverans | Verizon: Allmän webbleverans | Verizon: DSA | Akamai: Allmän webbleverans | Akamai: DSA | Akamai: Stor fil nedladdning | Akamai: allmän eller VOD media streaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Hederbeskärning**       | Ja    | Ja   | Inga   | Ja    | Inga   | Ja   | Ja    |
| **VARAKTIGHET FÖR CDN-cache** | 2 dagar |7 dagar | Inget | 7 dagar | Inget | 1 dag | 1 år |

**Honor ursprung**: Anger om att hedra de som stöds cache-direktiv rubriker om de finns i HTTP-svar från ursprungsservern.

**CDN-cachevaraktighet:** Anger hur länge en resurs cachelagras på Azure CDN. Men om **Honor ursprung** är Ja och HTTP-svar från ursprungsservern innehåller cache-direktiv huvudet `Expires` eller `Cache-Control: max-age`, Azure CDN använder varaktighetsvärdet som anges av huvudet i stället. 

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du anpassar och åsidosätter standardcachelagringsbeteendet på CDN via cachelagringsregler finns i [Kontrollera Azure CDN-cachelagringsbeteende med cachelagringsregler](cdn-caching-rules.md). 
- Mer information om hur du använder frågesträngar för att styra cachelagringsbeteende finns i [Kontrollera Azure CDN-cachelagring med frågesträngar](cdn-query-string.md).



