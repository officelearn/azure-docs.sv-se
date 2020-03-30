---
title: Principer för Azure API-hantering | Microsoft-dokument
description: Läs mer om principerna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219466"
---
# <a name="api-management-policies"></a>API Management-principer
Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](api-management-howto-policies.md).  
  
 Principer är en kraftfull funktion i systemet som gör det möjligt för utgivaren att ändra beteendet för API genom konfiguration. Principer är en samling satser som körs sekventiellt på begäran eller svaret på ett API. Populära uttalanden inkluderar formatkonvertering från XML till JSON och samtalsfrekvensbegränsning för att begränsa mängden inkommande samtal från en utvecklare. Många fler principer är tillgängliga direkt.  
  
 Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde](api-management-advanced-policies.md#choose) och [Ange variabel](api-management-advanced-policies.md#set-variable) baseras på principuttryck. Mer information finns i [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies) och [Principuttryck](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Politik  
  
-   [Principer för begränsning av åtkomst](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrollera HTTP-huvudet](api-management-access-restriction-policies.md#CheckHTTPHeader) - Framtvingar förekomsten och/eller värdet för ett HTTP-huvud.  
    -   [Begränsa samtalsfrekvensen per prenumeration](api-management-access-restriction-policies.md#LimitCallRate) - Förhindrar API-användningstoppar genom att begränsa samtalsfrekvensen, per prenumeration.  
    -   [Begränsa samtalsfrekvensen per tangent](api-management-access-restriction-policies.md#LimitCallRateByKey) - Förhindrar API-användningstoppar genom att begränsa samtalsfrekvensen, per nyckel.  
    -   [Begränsa anropar-IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filter (tillåter/nekar) anrop från specifika IP-adresser och/eller adressintervall.  
    -   [Ange användningskvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – Gör att du kan tillämpa en samtalsvolym för förnybar tid eller livstidssamtal och/eller bandbredd, per prenumeration.  
    -   [Ange användningskvot efter nyckel](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - Gör att du kan tillämpa en samtalsvolym för förnybar tid eller livstidssamtal och/eller bandbredd, per nyckel.  
    -   [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) - Framtvingar förekomsten och giltigheten av en JWT som extraherats från antingen ett angivet HTTP-huvud eller en angiven frågeparameter.  
-   [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Kontrollflöde](api-management-advanced-policies.md#choose) - Tillämpar villkorligt principsatser baserat på utvärdering av boolesiska uttryck.  
    -   [Vidarebefordran -](api-management-advanced-policies.md#ForwardRequest) Vidarebefordrar begäran till backend-tjänsten.
    -   [Begränsa samtidighet](api-management-advanced-policies.md#LimitConcurrency) - Förhindrar att slutna principer körs med mer än det angivna antalet begäranden åt gången.
    -   [Logga till händelsehubb](api-management-advanced-policies.md#log-to-eventhub) - Skickar meddelanden i angivet format till ett meddelandemål som definieras av en Logger-entitet.
    -   [Håna svar](api-management-advanced-policies.md#mock-response) - Avbryter pipeline-körningen och returnerar ett hånat svar direkt till anroparen.
    -   [Försök igen](api-management-advanced-policies.md#Retry) - Återförsökskörning av de bifogade principsatserna, om och tills villkoret är uppfyllt. Körningen upprepas med de angivna tidsintervallen och upp till det angivna antalet försök.  
    -   [Retursvar](api-management-advanced-policies.md#ReturnResponse) - Avbryter pipelinekörningen och returnerar det angivna svaret direkt till anroparen.  
    -   [Skicka en way-begäran](api-management-advanced-policies.md#SendOneWayRequest) - Skickar en begäran till den angivna webbadressen utan att vänta på ett svar.  
    -   [Skicka begäran](api-management-advanced-policies.md#SendRequest) - Skickar en begäran till den angivna WEBBADRESSEN.
    -   [Ange HTTP-proxy](api-management-advanced-policies.md#SetHttpProxy) - Gör att du kan dirigera vidarebefordrade begäranden via en HTTP-proxy.
    -   [Ange variabel](api-management-advanced-policies.md#set-variable) - Beständiga ett värde i en namngiven kontextvariabel för senare åtkomst.  
    -   [Ange begäransmetod](api-management-advanced-policies.md#SetRequestMethod) - Gör att du kan ändra HTTP-metoden för en begäran.  
    -   [Ange statuskod](api-management-advanced-policies.md#SetStatus) - Ändrar HTTP-statuskoden till det angivna värdet.  
    -   [Spårning](api-management-advanced-policies.md#Trace) - Lägger till anpassade spårningar i API Inspector-utdata, application insights telemetries och diagnostikloggar. [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)  
    -   [Vänta](api-management-advanced-policies.md#Wait) - Väntar på bifogad [Skicka begäran](api-management-advanced-policies.md#SendRequest), Hämta värde [från cache-](api-management-caching-policies.md#GetFromCacheByKey)eller [kontrollflödesprinciper](api-management-advanced-policies.md#choose) som ska slutföras innan du fortsätter.  
-   [Autentiseringsprinciper](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autentisera med Basic](api-management-authentication-policies.md#Basic) - Autentisera med en serverdtjänst med grundläggande autentisering.  
    -   [Autentisera med klientcertifikat](api-management-authentication-policies.md#ClientCertificate) - Autentisera med en serverd-tjänst med klientcertifikat.  
    -   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) - Autentisera med en backend-tjänst med hjälp av en [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Cachelagringsprinciper](api-management-caching-policies.md#CachingPolicies)  
    -   [Hämta från cache](api-management-caching-policies.md#GetFromCache) - Utför cachesökning och returnera ett giltigt cachelagrat svar när det är tillgängligt.  
    -   [Lagra till cache](api-management-caching-policies.md#StoreToCache) - Cacheminnen svar enligt den angivna cachekontrollkonfigurationen.  
    -   [Hämta värde från cacheminnet](api-management-caching-policies.md#GetFromCacheByKey) - Hämta ett cachelagrat objekt efter nyckel.  
    -   [Lagra värde i cacheminnet](api-management-caching-policies.md#StoreToCacheByKey) - Lagra ett objekt i cachen efter nyckel.  
    -   [Ta bort värde från cacheminnet](api-management-caching-policies.md#RemoveCacheByKey) - Ta bort ett objekt i cachen efter nyckel.  
-   [Principer mellan domäner](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Tillåt samtal mellan domäner](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Gör API:et tillgängligt från webbläsarbaserade Adobe Flash- och Microsoft Silverlight-klienter.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - Lägger till stöd för resursdelning mellan ursprung (CORS) i en åtgärd eller ett API för att tillåta samtal mellan domäner från webbläsarbaserade klienter.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Lägger till JSON med utfyllnad (JSONP) stöd till en åtgärd eller ett API för att tillåta cross-domain samtal från JavaScript webbläsarbaserade klienter.  
-   [Omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Konverterar begäran eller svarstext från JSON till XML.  
    -   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Konverterar begäran eller svarstext från XML till JSON.  
    -   [Söka efter och ersätta sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) - Söker efter en understräng för begäran eller svar och ersätter den med en annan delsträng.  
    -   [Mask webbadresser i innehåll](api-management-transformation-policies.md#MaskURLSContent) - Omskrivningar (masker) länkar i svarstexten så att de pekar på motsvarande länk via gatewayen.  
    -   [Ange serverdservice](api-management-transformation-policies.md#SetBackendService) - Ändrar serverdatjänsten för en inkommande begäran.  
    -   [Ange brödtext](api-management-transformation-policies.md#SetBody) - Anger meddelandetexten för inkommande och utgående begäranden.  
    -   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) - Tilldelar ett värde till ett befintligt svars- och/eller begärandehuvud eller lägger till ett nytt svar och/eller begäranden.  
    -   [Ange frågesträngparameter](api-management-transformation-policies.md#SetQueryStringParameter) - Lägger till, ersätter värdet för eller tar bort frågesträngparametern för begäran.  
    -   [Skriv om URL](api-management-transformation-policies.md#RewriteURL) - Konverterar en URL för begäran från dess offentliga formulär till det formulär som förväntas av webbtjänsten.  
    -   [Omforma XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) - Tillämpar en XSL-omvandling på XML i begäran eller svarstexten.  



## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Omvandla API:er](transform-api.md)
+ [Policyexempel](policy-samples.md)   
