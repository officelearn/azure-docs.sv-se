---
title: Azure API Management-principer | Microsoft Docs
description: Läs mer om principerna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7304ade9c47034b6cb76eb87126f09fb5af65c8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="api-management-policies"></a>API Management-principer
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](api-management-howto-policies.md).  
  
 Principer är en kraftfull funktion för systemet som tillåter utgivaren för att ändra funktionssättet för API: et genom konfigurationen. Principer är en samling av instruktioner som utförs i tur och ordning på begäran eller svar på en API. Populära instruktioner med Formatkonvertering från XML till JSON och anropa hastighet begränsa om du vill begränsa mängden inkommande samtal från en utvecklare. Många fler principer är tillgängliga direkt.  
  
 Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde](api-management-advanced-policies.md#choose) och [Ange variabel](api-management-advanced-policies.md#set-variable) baseras på principuttryck. Mer information finns i [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies) och [Principuttryck](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> principer  
  
-   [Principer för begränsning av åtkomst](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrollera HTTP-huvudet](api-management-access-restriction-policies.md#CheckHTTPHeader) -tillämpar existens och/eller värdet för ett HTTP-huvud.  
    -   [Gränsen anropet frekvensen av prenumerationen](api-management-access-restriction-policies.md#LimitCallRate) -förhindrar API användning ger spikar i diagrammet genom att begränsa anropet frekvensen på grundval av per prenumeration.  
    -   [Gränsen anropet frekvensen av nyckeln](api-management-access-restriction-policies.md#LimitCallRateByKey) -förhindrar API användning ger spikar i diagrammet genom att begränsa anropet frekvensen på grundval av per nyckel.  
    -   [Begränsa anroparen IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar)-anrop från särskilda IP-adresser och/eller -adressintervall.  
    -   [Kvot för uppsättningen av prenumerationen](api-management-access-restriction-policies.md#SetUsageQuota) -du kan tillämpa en förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per prenumeration.  
    -   [Kvot för uppsättningen av nyckeln](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -du kan tillämpa en förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per nyckel.  
    -   [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) -tillämpar förekomst och giltigheten för en JWT extraheras från ett angivna HTTP-sidhuvud eller en angiven frågeparameter.  
-   [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) - villkorligt gäller principen uttryck baserat på tillämpningen av booleska uttryck.  
    -   [Vidarebefordra begäran](api-management-advanced-policies.md#ForwardRequest) -vidarebefordrar begäran till backend-tjänsten.  
    -   [Loggen till Event Hub](api-management-advanced-policies.md#log-to-eventhub) -skickar meddelanden i det angivna formatet till ett meddelandemål som definieras av en loggaren entitet.  
    -   [Försök](api-management-advanced-policies.md#Retry) -återförsök körningen av de slutna principrapporter om och tills villkoret är uppfyllt. Körningen upprepas med de angivna intervall och upp till angivet antal nya försök.  
    -   [Returnera svar](api-management-advanced-policies.md#ReturnResponse) -avbryts körningen i pipeline och returnerar det angivna svaret direkt till anroparen.  
    -   [Skicka förfrågan om enkelriktade](api-management-advanced-policies.md#SendOneWayRequest) -skickar en begäran till den angivna URL: en utan att vänta på ett svar.  
    -   [Skicka förfrågan](api-management-advanced-policies.md#SendRequest) -skickar en begäran till angiven URL.  
    -   [Ange variabel](api-management-advanced-policies.md#set-variable) -bevara ett värde i en namngiven kontexten variabel för senare användning.  
    -   [Ange metoden](api-management-advanced-policies.md#SetRequestMethod) -kan du ändra HTTP-metoden för en begäran.  
    -   [Ange statuskoden](api-management-advanced-policies.md#SetStatus) -ändrar HTTP-statuskoden till det angivna värdet.  
    -   [Spåra](api-management-advanced-policies.md#Trace) -lägger till en sträng i den [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata.  
    -   [Vänta](api-management-advanced-policies.md#Wait) -väntar för omslutna [begäran om att skicka](api-management-advanced-policies.md#SendRequest), [hämta värdet från cache](api-management-caching-policies.md#GetFromCacheByKey), eller [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) principer för att slutföra innan du fortsätter.  
-   [Autentiseringsprinciper](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autentisera med grundläggande](api-management-authentication-policies.md#Basic) -autentisering med en serverdelstjänst som använder grundläggande autentisering.  
    -   [Autentisera med klientcertifikatet](api-management-authentication-policies.md#ClientCertificate) -autentisering med en serverdelstjänst som använder klientcertifikat.  
-   [Cachelagringsprinciper](api-management-caching-policies.md#CachingPolicies)  
    -   [Hämta från cache](api-management-caching-policies.md#GetFromCache) -utföra cache Leta upp och returnera ett giltigt cachelagrade svar när det är tillgängligt.  
    -   [Arkivet ska cachelagras](api-management-caching-policies.md#StoreToCache) -cachelagrar svaret enligt den angivna konfigurationen.  
    -   [Hämta ett värde från cache](api-management-caching-policies.md#GetFromCacheByKey) -hämta ett cachelagrade objekt med nyckel.  
    -   [Lagrar värdet i cache](api-management-caching-policies.md#StoreToCacheByKey) -lagra ett objekt i cacheminnet av nyckeln.  
    -   [Ta bort värdet från cache](api-management-caching-policies.md#RemoveCacheByKey) -ta bort ett objekt i cacheminnet av nyckeln.  
-   [Principer mellan domäner](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Tillåt mellan domäner kan anropa](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -gör API: et åtkomlig från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -lägger till resursdelning för korsande ursprung (CORS) stöd till en åtgärd eller en API för att tillåta domänerna anrop från webbläsarbaserade klienter.  
    -   [Hanteras JSONP](api-management-cross-domain-policies.md#JSONP) -lägger till JSON med stöd för utfyllnad (hanteras JSONP) till en åtgärd eller en API för att tillåta domänerna anrop från JavaScript-webbläsarbaserade klienter.  
-   [Omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begäran eller svar body från JSON till XML.  
    -   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begäran eller svar body från XML till JSON.  
    -   [Sök och Ersätt strängen i brödtexten](api-management-transformation-policies.md#Findandreplacestringinbody) - söker efter en begäran eller ett svar delsträng och ersätter den med en annan understräng.  
    -   [Maskera URL: er i innehåll](api-management-transformation-policies.md#MaskURLSContent) -skriver (masker) länkar i svaret body så att de pekar på motsvarande länk via gatewayen.  
    -   [Ange serverdelstjänst](api-management-transformation-policies.md#SetBackendService) -ändras serverdelstjänst för en inkommande begäran.  
    -   [Konfigurera brödtext](api-management-transformation-policies.md#SetBody) -anger meddelandetexten för inkommande och utgående förfrågningar.  
    -   [Ange HTTP-huvudet](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till en befintlig svar och/eller huvudet i begäran eller lägger till nya svar och/eller begäran-huvud.  
    -   [Ange frågesträngparametern](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet eller tar bort frågesträngparametern för begäran.  
    -   [Skriv om URL: en](api-management-transformation-policies.md#RewriteURL) -konverterar en begärd URL från dess offentliga form i formuläret som förväntades av webbtjänsten.  
    -   [Transformera XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) -tillämpar en XSL-transformation på XML i begäran eller svar.  



## <a name="next-steps"></a>Nästa steg
Arbeta med principer, Läs mer:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
