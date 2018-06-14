---
title: Azure API Management korsdomänprinciper | Microsoft Docs
description: Läs mer om principerna över domäner kan användas i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129042"
---
# <a name="api-management-cross-domain-policies"></a>Korsdomänprinciper för API Management
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a>Mellan domänprinciper  
  
-   [Tillåt mellan domäner kan anropa](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -gör API: et åtkomlig från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
-   [CORS](api-management-cross-domain-policies.md#CORS) -lägger till resursdelning för korsande ursprung (CORS) stöd till en åtgärd eller en API för att tillåta domänerna anrop från webbläsarbaserade klienter.  
-   [Hanteras JSONP](api-management-cross-domain-policies.md#JSONP) -lägger till JSON med stöd för utfyllnad (hanteras JSONP) till en åtgärd eller en API för att tillåta domänerna anrop från JavaScript-webbläsarbaserade klienter.  
  
##  <a name="AllowCrossDomainCalls"></a>Tillåta anrop mellan domäner  
 Använd den `cross-domain` princip för att nå API: et från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|mellan domäner|Rotelementet. Underordnade element måste motsvara den [Adobe domänerna filen principspecifikationen](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** globala  
  
##  <a name="CORS"></a>CORS  
 Den `cors` principen lägger till resursdelning för korsande ursprung (CORS) stöd till en åtgärd eller en API för att tillåta domänerna anrop från webbläsarbaserade klienter.  
  
 CORS kan en webbläsare och en server för att interagera och avgöra om att tillåta specifika cross-origin-begäranden (d.v.s. XMLHttpRequests anrop från JavaScript på en webbsida till andra domäner) eller inte. Detta ger bättre flexibilitet än att bara tillåta samma origin-begäranden, men det är säkrare än att tillåta alla cross-origin-begäranden.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Exempel  
 Det här exemplet visar hur du stöder före svarta begäranden, till exempel de som har anpassade huvuden eller andra metoder än GET och POST. För att stödja anpassade sidhuvuden och ytterligare HTTP-verb, Använd den `allowed-methods` och `allowed-headers` avsnitten som visas i följande exempel.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|cors|Rotelementet.|Ja|Saknas|  
|tillåtna ursprung|Innehåller `origin` element som beskriver de tillåtna ursprung för domäner begäranden. `allowed-origins`kan innehålla antingen en enskild `origin` element som anger `*` så att alla ursprung, eller en eller flera `origin` element som innehåller en URI.|Ja|Saknas|  
|ursprung|Värdet kan vara antingen `*` så att alla ursprung eller en URI som anger ett enda ursprung. URI måste innehålla ett schema, värd och port.|Ja|Om porten anges i en URI: N används port 80 för HTTP och port 443 används för HTTPS.|  
|tillåtna metoder|Det här elementet krävs om metoder än hämta eller POST tillåts. Innehåller `method` element som anger HTTP-verb som stöds.|Nej|Om det här avsnittet inte finns, stöds GET och POST.|  
|Metoden|Anger ett HTTP-verb.|Minst en `method` -element krävs om de `allowed-methods` avsnittet finns.|Saknas|  
|tillåtna rubriker|Det här elementet innehåller `header` element anger namn på rubriker som kan ingå i begäran.|Nej|Saknas|  
|exponera rubriker|Det här elementet innehåller `header` element anger namn på rubriker som ska vara tillgängliga för klienten.|Nej|Saknas|  
|sidhuvud|Anger ett namn för sidhuvudet.|Minst en `header` -element krävs i `allowed-headers` eller `expose-headers` om avsnittet finns.|Saknas|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|Tillåt autentiseringsuppgifter|Den `Access-Control-Allow-Credentials` sidhuvud preflight-svar anges till värdet för det här attributet och påverka klientens möjlighet att skicka autentiseringsuppgifter i domänerna begäranden.|Nej|false|  
|preflight-resultat-max-ålder|Den `Access-Control-Max-Age` sidhuvud preflight-svar anges till värdet för det här attributet och påverka användaragenten möjlighet att före svarta cachesvar.|Nej|0|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** API, åtgärden  
  
##  <a name="JSONP"></a>HANTERAS JSONP  
 Den `jsonp` principen lägger till JSON med stöd för utfyllnad (hanteras JSONP) i en åtgärd eller en API för att tillåta domänerna anrop från JavaScript-webbläsarbaserade klienter. Hanteras JSONP är en metod som används i JavaScript-program för att begäran om data från en server i en annan domän. Hanteras JSONP kringgår den begränsning som tillämpas av de flesta webbläsare där åtkomst till webbsidor måste vara i samma domän.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Om du anropar metoden utan parametern motringning? cb = XXX returneras oformaterade JSON (utan en funktionen anropet wrapper).  
  
 Om du lägger till parametern återanrop `?cb=XXX` returneras ett hanteras JSONP-resultat, wrapping ursprungliga JSON resultat runt Återanropsfunktionen som`XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|hanteras jsonp|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|motringning parameternamn|De domäner JavaScript-anrop med det fullständigt kvalificerade domännamnet där funktionen finns prefixet.|Ja|Saknas|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** utgående  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   