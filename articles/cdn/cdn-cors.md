---
title: Använda Azure CDN med CORS | Microsoft Docs
description: Lär dig hur du använder Azure Content Delivery Network (CDN) till med Cross-Origin Resource Sharing (CORS).
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
ms.openlocfilehash: 3dbf0aea50f382a0b325bf068a200cde42098733
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679884"
---
# <a name="using-azure-cdn-with-cors"></a>Använda Azure CDN med CORS
## <a name="what-is-cors"></a>Vad är CORS?
CORS (Cross Origin Resource Sharing) är en HTTP-funktion som gör ett webbprogram som körs i en domän att komma åt resurser i en annan domän. För att minska risken för cross-site skriptattacker alla moderna webbläsare implementerar en säkerhetsbegränsning som kallas [princip om samma ursprung](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Detta förhindrar att en webbsida från anropa API: er i en annan domän.  CORS erbjuder ett säkert sätt så att ett ursprung (ursprungsdomänen) att anropa API: er i ett annat ursprung.

## <a name="how-it-works"></a>Hur det fungerar
Det finns två typer av CORS-förfrågningar, *enkla begäranden* och *komplexa begäranden.*

### <a name="for-simple-requests"></a>För enkla begäranden:

1. Webbläsaren skickar en begäran om CORS med ytterligare **ursprung** HTTP-frågehuvudet. Värdet för den här rubriken är ursprunget som användes för överordnade-sidan, vilket definieras som en kombination av *-protokollet,* *domän,* och *port.*  När en sida från https://www.contoso.com försöker komma åt en användares data i fabrikam.com ursprung, följande begärandehuvudet skulle skickas till fabrikam.com:

   `Origin: https:\//www.contoso.com`

2. Servern svarar med något av följande:

   * En **Access-Control-Allow-Origin** rubrik i sitt svar som anger vilken ursprungsplats som tillåts. Exempel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * En HTTP-felkoden, till exempel 403 om servern inte tillåter resursdelning för korsande ursprung begäran när du har kontrollerat rubriken ursprung

   * En **Access-Control-Allow-Origin** huvud med jokertecken som gör att alla ursprung:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>För komplexa begäranden:

En begäran om komplexa är en CORS-begäran där webbläsaren krävs för att skicka en *preliminära begäran* (det vill säga en preliminär avsökning) innan du skickar den faktiska CORS-förfrågan. Preliminära begäran begär serverbehörighet om de ursprungliga CORS begära kan fortsätta och är en `OPTIONS` begäran till samma URL.

> [!TIP]
> Mer information om CORS flöden och vanliga fallgropar visa den [Guide till CORS för REST API: er](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Jokertecken eller enskild ursprung scenarier
CORS i Azure CDN fungerar automatiskt utan någon ytterligare konfiguration när den **Access-Control-Allow-Origin** rubrik är inställd på jokertecken (*) eller ett enda ursprung.  CDN ska cachelagra det första svaret och efterföljande förfrågningar kommer att använda samma rubrik.

Om begäranden har redan gjorts till CDN innan CORS som anges på ditt ursprung, måste du rensa innehåll på slutpunkten innehållet läses in på nytt innehåll med den **Access-Control-Allow-Origin** rubrik.

## <a name="multiple-origin-scenarios"></a>Scenarier med flera ursprung
Om du vill tillåta att en specifik lista över ursprung som ska tillåtas för CORS få saker lite mer komplicerat. Problemet uppstår när CDN cachelagrar den **Access-Control-Allow-Origin** rubriken för det första CORS-ursprunget.  När en annan CORS-ursprunget gör en efterföljande begäran, CDN fungerar den cachelagrade **Access-Control-Allow-Origin** rubriken, som inte matchar.  Det finns flera sätt att åtgärda detta.

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium från Verizon
Det bästa sättet att aktivera det här alternativet är att använda **Azure CDN Premium från Verizon**, som visar några avancerade funktioner. 

Du måste [skapa en regel](cdn-rules-engine.md) att kontrollera den **ursprung** sidhuvud på begäran.  Om det är ett giltigt ursprung regeln anger den **Access-Control-Allow-Origin** huvud med ursprung som tillhandahölls i begäran.  Om ursprunget anges i den **ursprung** rubrik tillåts inte, regeln utelämna den **Access-Control-Allow-Origin** rubrik, vilket leder till webbläsaren om du vill avvisa begäran. 

Det finns två sätt att göra detta med regelmotorn. I båda fallen kan den **Access-Control-Allow-Origin** huvudet från filens ursprungsservern ignoreras och CDN: s regelmotor fullständig hantering av de tillåtna CORS-ursprung.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Ett reguljärt uttryck med alla giltiga ursprung
I det här fallet ska du skapa ett reguljärt uttryck som innehåller alla de ursprung som du vill tillåta: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium från Verizon** använder [Perl kompatibla reguljära uttryck](https://pcre.org/) som motorn för reguljära uttryck.  Du kan använda ett verktyg som [reguljära uttryck 101](https://regex101.com/) att verifiera det reguljära uttrycket.  Observera att tecknet ”/” är giltig i reguljära uttryck och behöver inte undantas, men undantagstecken tecknet anses vara bästa praxis och förväntas av vissa regex-systemhälsoverifierare.
> 
> 

Om det reguljära uttrycket matchar regeln ersätter den **Access-Control-Allow-Origin** huvudet (om sådan finns) från ursprunget med ursprung som skickade begäran.  Du kan också lägga till ytterligare CORS-huvuden som **Access-Control-Tillåt-metoder**.

![Exempel på regler med reguljära uttryck](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Begära huvud-regel för varje ursprung.
I stället för reguljära uttryck kan du istället skapa en separat regel för varje ursprung som du vill att med hjälp av den **begära huvud med jokertecken** [matchningsvillkor](/previous-versions/azure/mt757336(v=azure.100)#Anchor_1). Precis som med metoden reguljärt uttryck anger enbart regelmotorn CORS-huvuden. 

![Regler för exemplet utan reguljärt uttryck](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> I exemplet ovan är användningen av jokertecknet * talar om regelmotor som motsvarar både HTTP och HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure standard CDN-profiler
På standard Azure CDN-profiler (**Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai**, och **Azure CDN Standard från Verizon**), den enda metoden för att tillåter flera ursprung utan användning av jokertecken ursprunget är att använda [cachelagring av frågesträngar](cdn-query-string.md). Aktivera inställningen fråga sträng för CDN-slutpunkten och sedan använda en unik frågesträng för begäranden från varje tillåtna domän. Detta resulterar i CDN-nätverkets ett separat objekt för varje unikt frågesträng. Den här metoden är perfekt, dock inte eftersom det kommer leda till flera kopior av samma fil som cachelagrats på CDN.  

