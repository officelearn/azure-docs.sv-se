---
title: Använda Azure CDN med CORS | Microsoft-dokument
description: Lär dig hur du använder CDN (Azure Content Delivery Network) till med CORS (Cross-Origin Resource Sharing).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278106"
---
# <a name="using-azure-cdn-with-cors"></a>Använda Azure CDN med CORS
## <a name="what-is-cors"></a>Vad är CORS?
CORS (Cross Origin Resource Sharing) är en HTTP-funktion som gör att ett webbprogram som körs under en domän kan komma åt resurser i en annan domän. För att minska risken för skriptattacker mellan webbplatser implementerar alla moderna webbläsare en säkerhetsbegränsning som kallas [principen med samma ursprung](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Detta förhindrar att en webbsida anropar API:er i en annan domän.  CORS är ett säkert sätt att tillåta ett ursprung (ursprungsdomänen) att anropa API:er i ett annat ursprung.

## <a name="how-it-works"></a>Hur det fungerar
Det finns två typer av CORS-begäranden, *enkla begäranden* och *komplexa begäranden.*

### <a name="for-simple-requests"></a>För enkla önskemål:

1. Webbläsaren skickar CORS-begäran med ytterligare ett **Origin** HTTP-begärandehuvud. Värdet för det här huvudet är det ursprung som tjänade den överordnade sidan, som definieras som en kombination av *protokoll,* *domän* och *port.*  När en sida\:från https //www.contoso.com försöker komma åt en användares data i fabrikam.com ursprung, skickas följande begäranden till fabrikam.com:

   `Origin: https://www.contoso.com`

2. Servern kan svara med något av följande:

   * Ett **Access-Control-Allow-Origin-huvud** i svaret som anger vilken ursprungsplats som är tillåten. Ett exempel:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * En HTTP-felkod som 403 om servern inte tillåter begäran om korsursprung efter att ha kontrollerat Origin-huvudet

   * En **Access-Control-Allow-Origin-huvud** med ett jokertecken som tillåter alla ursprung:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>För komplexa begäranden:

En komplex begäran är en CORS-begäran där webbläsaren krävs för att skicka en *preflight-begäran* (det vill ha en preliminär avsökning) innan den faktiska CORS-begäran skickas. Preflight-begäran frågar serverbehörigheten om den `OPTIONS` ursprungliga CORS-begäran kan fortsätta och är en begäran till samma URL.

> [!TIP]
> Mer information om CORS-flöden och vanliga fallgropar finns i [guiden till CORS för REST-API:er](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenarier för jokertecken eller ett enskilt ursprung
CORS på Azure CDN fungerar automatiskt utan ytterligare konfiguration när **access-control-allow-origin-huvudet** är inställt på jokertecken (*) eller ett enda ursprung.  CDN cachelagrar det första svaret och efterföljande begäranden använder samma huvud.

Om begäranden redan har gjorts till CDN innan CORS har angetts för ditt ursprung måste du rensa innehåll på slutpunktsinnehållet för att ladda om innehållet med **access-control-allow-origin-huvudet.**

## <a name="multiple-origin-scenarios"></a>Scenarier för flera ursprung
Om du behöver tillåta en specifik lista över ursprung som skall tillåtas för CORS, saker och ting blir lite mer komplicerat. Problemet uppstår när CDN cachelagrar **access-control-allow-origin-huvudet** för det första CORS-ursprunget.  När ett annat CORS-ursprung gör en efterföljande begäran kommer CDN att betjäna det cachelagrade **Access-Control-Allow-Origin-huvudet,** som inte matchar.  Det finns flera sätt att rätta till detta.

### <a name="azure-cdn-standard-profiles"></a>Standardprofiler för Azure CDN
På Azure CDN Standard från Microsoft kan du skapa en regel i [standardregelmotorn](cdn-standard-rules-engine-reference.md) för att kontrollera **Origin-huvudet** på begäran. Om det är ett giltigt ursprung anger regeln att **access-control-allow-origin-huvudet** med önskat värde anges. I det här fallet ignoreras **access-control-allow-origin-huvudet** från filens ursprungsserver och CDN:s regelmotor hanterar helt det tillåtna CORS-ursprunget.

![Regelexempel med standardregler motor](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Du kan lägga till ytterligare åtgärder i regeln för att ändra ytterligare svarshuvuden, till exempel **Åtkomstkontroll-Tillåt-metoder**.
> 

På **Azure CDN Standard från Akamai**är den enda mekanismen för att tillåta flera ursprung utan användning av jokerteckenursprunget att använda [frågesträngcachening](cdn-query-string.md). Aktivera frågestränginställningen för CDN-slutpunkten och använd sedan en unik frågesträng för begäranden från varje tillåten domän. Om du gör det blir det att CDN-cachelagringen av ett separat objekt för varje unik frågesträng. Den här metoden är dock inte idealisk, eftersom det kommer att resultera i flera kopior av samma fil som cachelagras på CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium från Verizon
Med hjälp av Verizon Premium-regelmotorn måste du [skapa en regel](cdn-rules-engine.md) för att kontrollera **Origin-huvudet** på begäran.  Om det är ett giltigt ursprung anger regeln att huvudet **Åtkomstkontroll-Tillåt-Ursprung** med ursprunget som anges i begäran.  Om ursprunget som anges i **Origin-huvudet** inte är tillåtet bör regeln utelämna **access-control-allow-origin-huvudet,** vilket gör att webbläsaren avvisar begäran. 

Det finns två sätt att göra detta med Premium regler motorn. I båda fallen ignoreras **access-control-allow-origin-huvudet** från filens ursprungsserver och CDN:s regelmotor hanterar helt det tillåtna CORS-ursprunget.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Ett reguljärt uttryck med alla giltiga ursprung
I det här fallet ska du skapa ett reguljärt uttryck som innehåller alla ursprung som du vill tillåta: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium från Verizon** använder [Perl-kompatibla reguljära uttryck](https://pcre.org/) som motor för reguljära uttryck.  Du kan använda ett verktyg som [Reguljära uttryck 101](https://regex101.com/) för att validera ditt reguljära uttryck.  Observera att tecknet "/" är giltigt i reguljära uttryck och inte behöver fly, men att fly från det tecknet anses vara en bästa praxis och förväntas av vissa regex-validerare.
> 
> 

Om det reguljära uttrycket matchar ersätter regeln namnet **Access-Control-Allow-Origin** (om sådant finns) från ursprunget med det ursprung som skickade begäran.  Du kan också lägga till ytterligare CORS-huvuden, till exempel **Åtkomstkontroll-Tillåt-metoder**.

![Exempel på regler med reguljärt uttryck](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Begär rubrikregel för varje ursprung.
I stället för reguljära uttryck kan du i stället skapa en separat regel för varje ursprung som du vill tillåta med hjälp av [matchningsvillkoret](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **Förfrågningshuvud.** Precis som med metoden för reguljära uttryck anger regelmotorn enbart CORS-huvudena. 

![Exempel på regler utan reguljärt uttryck](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> I exemplet ovan talar användningen av jokertecknet * om regelmotorn för att matcha både HTTP och HTTPS.
> 
> 



