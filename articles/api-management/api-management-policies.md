---
title: Azure API Management-principer | Microsoft Docs
description: Läs mer om principerna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 711f6395959b3718ca0a094a03c07c4381892104
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762855"
---
# <a name="api-management-policies"></a>API Management-principer
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](api-management-howto-policies.md).  
  
 Principer är en kraftfull funktion i systemet som gör att utgivaren kan ändra beteendet för API: ets konfiguration. Principer är en samling med instruktioner som körs sekventiellt på begäran eller en API-svar. Populära instruktioner är Formatkonvertering från XML till JSON och begränsning för att begränsa antalet inkommande anrop från en utvecklare av anropsfrekvens. Många fler principer är tillgängliga direkt.  
  
 Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde](api-management-advanced-policies.md#choose) och [Ange variabel](api-management-advanced-policies.md#set-variable) baseras på principuttryck. Mer information finns i [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies) och [Principuttryck](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Principer  
  
-   [Principer för begränsning av åtkomst](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Kontrollera HTTP-huvud](api-management-access-restriction-policies.md#CheckHTTPHeader) -framtvingar förekomsten och/eller värdet för ett HTTP-huvud.  
    -   [Begränsa anropsfrekvensen per prenumeration](api-management-access-restriction-policies.md#LimitCallRate) – förhindrar att API-användning vid tillfälliga toppar genom att begränsa anropsfrekvensen per prenumeration procentuellt utifrån.  
    -   [Begränsa anropsfrekvensen av nyckeln](api-management-access-restriction-policies.md#LimitCallRateByKey) – förhindrar att API-användning vid tillfälliga toppar genom att begränsa anropsfrekvens per nyckel regelbundet.  
    -   [Begränsa anroparen IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar)-anrop från specifika IP-adresser och/eller -adressintervall.  
    -   [Ange användningskvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – du kan tillämpa en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per prenumeration.  
    -   [Ange användningskvot av nyckeln](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – du kan tillämpa en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per nyckel.  
    -   [Verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) -tillämpar förekomst och giltigheten hos en JWT som extraheras från ett angivna HTTP-sidhuvud eller en angiven frågeparameter.  
-   [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Kontrollflöde](api-management-advanced-policies.md#choose) – villkorligt gäller principrapporter som baseras på utvärderingen av booleska uttryck.  
    -   [Vidarebefordra begäran](api-management-advanced-policies.md#ForwardRequest) -vidarebefordrar begäran till backend-tjänsten.  
    -   [Logga till Event Hub](api-management-advanced-policies.md#log-to-eventhub) -skickar meddelanden i formatet som anges till ett meddelandemål som definieras av en Logger-enhet.  
    -   [Försök](api-management-advanced-policies.md#Retry) -återförsök körningen av de slutna principrapporter om och tills villkoret är uppfyllt. Körningen upprepas med de angivna intervall och upp till den angivna antal nya försök.  
    -   [Returnera svaret](api-management-advanced-policies.md#ReturnResponse) -avbryter pipeline-körning och returnerar det angivna svaret direkt till anroparen.  
    -   [Skicka enkelriktade begäran](api-management-advanced-policies.md#SendOneWayRequest) -skickar en begäran till den angivna URL: en utan att vänta tills ett svar.  
    -   [Skicka begäran](api-management-advanced-policies.md#SendRequest) -skickar en begäran till den angivna URL: en.  
    -   [Ange variabel](api-management-advanced-policies.md#set-variable) -bevara ett värde i en namngiven sammanhangsvariabel för senare användning.  
    -   [Ange begärandemetoden](api-management-advanced-policies.md#SetRequestMethod) – kan du ändra HTTP-metoden för en begäran.  
    -   [Ange statuskoden](api-management-advanced-policies.md#SetStatus) -ändrar HTTP-statuskoden till det angivna värdet.  
    -   [Spårningen](api-management-advanced-policies.md#Trace) -lägger till en sträng i den [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata.  
    -   [Vänta](api-management-advanced-policies.md#Wait) -väntar för angiven [sändningsbegäranden](api-management-advanced-policies.md#SendRequest), [hämta värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey), eller [Kontrollflöde](api-management-advanced-policies.md#choose) principer för att slutföra innan du fortsätter.  
-   [Autentiseringsprinciper](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en serverdelstjänst som använder grundläggande autentisering.  
    -   [Autentisera med klientcertifikatet](api-management-authentication-policies.md#ClientCertificate) -autentisera med en serverdelstjänst som använder klientcertifikat.  
    -   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) -autentisera med ett serverdel med en [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Cachelagringsprinciper](api-management-caching-policies.md#CachingPolicies)  
    -   [Hämta från cache](api-management-caching-policies.md#GetFromCache) -utföra cache Leta upp och returnera ett giltigt cachelagrade svar när det är tillgängligt.  
    -   [Store till cache](api-management-caching-policies.md#StoreToCache) -cachelagrar svaret enligt angivna cache-control-konfigurationen.  
    -   [Hämta värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey) – hämta ett cachelagrat objekt med nyckeln.  
    -   [Store värdet i cache](api-management-caching-policies.md#StoreToCacheByKey) -Store ett objekt i cacheminnet av nyckeln.  
    -   [Ta bort värdet från cache](api-management-caching-policies.md#RemoveCacheByKey) -ta bort ett objekt i cachen med nyckeln.  
-   [Principer mellan domäner](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Tillåt anrop mellan domäner](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -gör API: et åtkomlig från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -lägger till cross-origin resource sharing (CORS) support till en åtgärd eller ett API för att tillåta anrop mellan domäner från webbläsarbaserade klienter.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -lägger till JSON med stöd för utfyllnad (JSONP) till en åtgärd eller ett API för att tillåta anrop mellan domäner från JavaScript-webbläsarbaserade klienter.  
-   [Omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begära eller svaret body från JSON till XML.  
    -   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begära eller svaret body från XML till JSON.  
    -   [Sök och Ersätt sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) – söker efter en begäran eller ett svar delsträng och ersätter den med en annan understräng.  
    -   [Maskera URL: er i innehåll](api-management-transformation-policies.md#MaskURLSContent) -skriver (masker) länkar i svaret body så att de pekar på motsvarande länk via gatewayen.  
    -   [Ange serverdelstjänst](api-management-transformation-policies.md#SetBackendService) -ändras serverdelstjänst för en inkommande begäran.  
    -   [Ange brödtext](api-management-transformation-policies.md#SetBody) -anger meddelandets brödtext för inkommande och utgående förfrågningar.  
    -   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till en befintlig svar och/eller begärandehuvudet eller lägger till ett nytt svar och/eller begäran-huvud.  
    -   [Ange parametern för frågesträngen](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet för eller tar bort begäran frågesträngparametern.  
    -   [Omskrivningswebbadressen](api-management-transformation-policies.md#RewriteURL) – konverterar en begärd URL från sin offentliga form i formuläret som förväntas av webbtjänsten.  
    -   [Transformera XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) -gäller en XML-transformering för XML i brödtexten för begäran eller ett svar.  



## <a name="next-steps"></a>Nästa steg
Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
