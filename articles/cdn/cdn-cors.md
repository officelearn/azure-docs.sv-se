---
title: Med hjälp av Azure CDN med CORS | Microsoft Docs
description: Lär dig hur du använder i Azure Content innehållsleveransnätverk (CDN) till med Cross-Origin Resource Sharing (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7070397f6e69b21add75bad8220f0b8ebe36d266
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="using-azure-cdn-with-cors"></a>Med hjälp av Azure CDN med CORS
## <a name="what-is-cors"></a>Vad är CORS?
CORS (Cross Origin Resource Sharing) är en HTTP-funktion som gör att ett webbprogram som körs i en domän kan komma åt resurser i en annan domän. För att minska risken för angrepp för webbplatser scripting alla moderna webbläsare för att implementera säkerhetsbegränsning kallas [samma ursprung princip](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Detta förhindrar att en webbsida från anropa API: er i en annan domän.  CORS ger ett säkert sätt så att ett ursprung (ursprungsdomän) att anropa API: er i ett ursprung.

## <a name="how-it-works"></a>Hur det fungerar
Det finns två typer av CORS-förfrågningar, *enkla begäranden* och *komplexa begäranden.*

### <a name="for-simple-requests"></a>För enkla begäranden:

1. Webbläsaren skickar en begäran för CORS med en ytterligare **ursprung** HTTP-huvud. Värdet för det här sidhuvudet är ursprung som hanteras av den överordnade sidan som har definierats som en kombination av *protokoll,* *domän,* och *port.*  När en sida från https://www.contoso.com försöker få åtkomst till användardata i fabrikam.com ursprung, begärandehuvudet följande skulle skickas till fabrikam.com:

   `Origin: https://www.contoso.com`

2. Servern svarar med något av följande:

   * En **Access Control-Tillåt-ursprung** rubrik i sitt svar som anger vilken plats ursprung är tillåtna. Exempel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * En HTTP-felkod, till exempel 403 om servern inte tillåter cross-origin-begäran när du har kontrollerat rubriken ursprung

   * En **Access Control-Tillåt-ursprung** huvud med jokertecken som gör att alla ursprung:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>För komplexa begäranden:

En komplex begäran är en CORS-begäran där webbläsaren krävs för att skicka en *preflight-begäran* (d.v.s. en preliminär avsökning) innan den faktiska CORS-begäranden skickas. Preflight-begäran frågar behörigheten server om de ursprungliga CORS begäran kan fortsätta och är en `OPTIONS` begäran till samma URL.

> [!TIP]
> Mer information om CORS-flöden och vanliga fallgropar, visa den [Guide till CORS för REST API: er](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Jokertecken eller enskild ursprung scenarier
CORS i Azure CDN fungerar automatiskt med ingen ytterligare konfiguration när den **Access Control-Tillåt-ursprung** huvud är inställt på jokertecken (*) eller ett enda ursprung.  CDN cachelagrar första svaret och efterföljande begäranden använder samma huvud.

Om begäran redan har gjorts till CDN innan CORS har angetts på den din ursprung måste du rensa innehållet för slutpunkten innehållet att läsa in innehåll med den **Access Control-Tillåt-ursprung** huvud.

## <a name="multiple-origin-scenarios"></a>Scenarier med flera ursprung
Om du vill tillåta att en viss lista över ursprung som ska tillåtas för CORS saker lite mer komplicerad. Problemet uppstår när CDN cachelagrar den **Access Control-Tillåt-ursprung** sidhuvud för det första CORS-ursprunget.  När en annan CORS-ursprunget gör en efterföljande begäran, CDN fungerar det cachelagrade **Access Control-Tillåt-ursprung** rubriken, som inte matchar.  Det finns flera sätt att åtgärda detta.

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium från Verizon
Det bästa sättet att göra detta är att använda **Azure CDN Premium från Verizon**, vilket visar att vissa avancerade funktioner. 

Du behöver [skapa en regel](cdn-rules-engine.md) att kontrollera den **ursprung** huvud i begäran.  Om det är ett giltigt ursprung din regel kommer att ange den **Access Control-Tillåt-ursprung** huvud med ursprung i begäran.  Om ursprung har angetts i den **ursprung** huvud är inte tillåtet, regeln bör utelämna den **Access Control-Tillåt-ursprung** huvud vilket leder till webbläsaren om du vill avvisa begäran. 

Det finns två sätt att göra detta med regler-motorn.  I båda fallen den **Access Control-Tillåt-ursprung** huvudet från filservern ursprung ignoreras helt, den CDN regelmotor hanterar helt tillåtna CORS ursprung.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Ett reguljärt uttryck med alla giltiga ursprung
I det här fallet skapar du ett reguljärt uttryck som innehåller alla ursprung som du vill tillåta: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN från Verizon** använder [Perl kompatibel reguljära uttryck](http://pcre.org/) som motorn för reguljära uttryck.  Du kan använda ett verktyg som [reguljära uttryck 101](https://regex101.com/) att verifiera det reguljära uttrycket.  Observera att tecknet ”/” är giltig i reguljära uttryck och behöver inte hoppas, men undantagstecken tecknet anses vara bästa praxis och förväntas av vissa regex verifierare.
> 
> 

Om det reguljära uttrycket matchar regeln ersätter den **Access Control-Tillåt-ursprung** huvud (eventuella) från ursprunget med ursprung som skickade begäran.  Du kan också lägga till ytterligare CORS-huvuden som **-kontroll-Tillåt-åtkomstmetoder**.

![Exempel på regler med reguljära uttryck](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Begäran huvud-regel för varje ursprung.
I stället för reguljära uttryck kan du i stället skapa en separat regel för varje ursprung som du vill tillåta med hjälp av den **begära huvud med jokertecken** [matchar villkoret](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Precis som med metoden reguljärt uttryck anger enbart regler motorn CORS-huvuden. 

![Regler som exempel utan reguljärt uttryck](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> I exemplet ovan med jokertecknet * anger regelmotor att matcha både HTTP och HTTPS.
> 
> 

### <a name="azure-cdn-standard"></a>Standard Azure CDN
På Azure CDN Standard profiler endast mekanism för att tillåta flera ursprung utan att använda jokertecken ursprung är att använda [fråga cachelagring av frågesträngar](cdn-query-string.md).  Du måste aktivera inställningen för frågan anslutningssträngen för CDN-slutpunkten och sedan använda en unik frågesträng för begäranden från varje tillåtna domän. Detta resulterar i CDN cachelagring ett separat objekt för varje unik frågesträng. Den här metoden är perfekt, dock inte eftersom den resulterar i flera kopior av samma fil cachelagras på CDN.  

