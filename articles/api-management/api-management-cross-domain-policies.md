---
title: Korsdomänprinciper Azure API Management | Microsoft Docs
description: Läs mer om principerna för över domäner som är tillgängliga för användning i Azure API Management.
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
ms.openlocfilehash: 8b43ef81385f1d185fdd63d2a33453c75684fd21
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969932"
---
# <a name="api-management-cross-domain-policies"></a>Korsdomänprinciper för API Management
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a> Principer mellan domäner  
  
-   [Tillåt anrop mellan domäner](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -gör API: et åtkomlig från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
-   [CORS](api-management-cross-domain-policies.md#CORS) -lägger till cross-origin resource sharing (CORS) support till en åtgärd eller ett API för att tillåta anrop mellan domäner från webbläsarbaserade klienter.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) -lägger till JSON med stöd för utfyllnad (JSONP) till en åtgärd eller ett API för att tillåta anrop mellan domäner från JavaScript-webbläsarbaserade klienter.  
  
##  <a name="AllowCrossDomainCalls"></a> Tillåt anrop mellan domäner  
 Använd den `cross-domain` princip att göra API tillgängligt från Adobe Flash och Microsoft Silverlight webbläsarbaserade klienter.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
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
|mellan domäner|Rotelement. Underordnade element måste motsvara den [Adobe domänerna filen principspecifikationen](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
-   **Princip-scope:** globala  
  
##  <a name="CORS"></a> CORS  
 Den `cors` Grupprincip lägger till cross-origin resource sharing (CORS) support till en åtgärd eller ett API för att tillåta anrop mellan domäner från webbläsarbaserade klienter.  
  
 CORS kan en webbläsare och en server för att interagera och avgöra om att tillåta specifika cross-origin-begäranden (d.v.s. XMLHttpRequests anrop som görs från JavaScript på en webbsida till andra domäner) eller inte. Detta möjliggör mer flexibilitet än att bara tillåta begäranden om samma ursprung, men det är säkrare än så att alla cross-origin-begäranden.  
  
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
 Det här exemplet visar hur du stöd för före flygning begäranden, till exempel de med anpassade huvuden eller andra metoder än GET och POST. För att stödja anpassade sidhuvuden och ytterligare HTTP-verb, Använd den `allowed-methods` och `allowed-headers` avsnitten som visas i följande exempel.  
  
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
|cors|Rotelement.|Ja|Gäller inte|  
|tillåtna ursprung|Innehåller `origin` element som beskriver de tillåtna ursprung för begäranden mellan domäner. `allowed-origins` kan innehålla antingen ett enskilt `origin` element som anger `*` så att alla ursprung, eller en eller flera `origin` element som innehåller en URI.|Ja|Gäller inte|  
|ursprung|Värdet kan vara antingen `*` så att alla ursprung eller en URI som anger ett enda ursprung. URI: N måste innehålla ett schema, värd och port.|Ja|Om porten utelämnas i en URI, används port 80 för HTTP och port 443 används för HTTPS.|  
|tillåtna metoder|Det här elementet krävs om metoder än får eller publicera tillåts. Innehåller `method` element som anger HTTP-verb som stöds.|Nej|Om det här avsnittet inte är tillgänglig, stöds GET och POST.|  
|metod|Anger ett HTTP-verb.|Minst en `method` elementet krävs om de `allowed-methods` avsnittet finns.|Gäller inte|  
|tillåtna huvuden|Det här elementet innehåller `header` element att ange namnen på de rubriker som kan tas med i begäran.|Nej|Gäller inte|  
|Exponerar rubriker|Det här elementet innehåller `header` element att ange namnen på de rubriker som ska vara tillgängliga för klienten.|Nej|Gäller inte|  
|sidhuvud|Anger en rubrik.|Minst en `header` elementet krävs i `allowed-headers` eller `expose-headers` om finns i avsnittet.|Gäller inte|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|Tillåt autentiseringsuppgifter|Den `Access-Control-Allow-Credentials` rubrik preflight-svar anges till värdet för det här attributet och påverka klientens möjlighet att skicka autentiseringsuppgifter i domänerna begäranden.|Nej|false|  
|preflight-resultatet-max-age|Den `Access-Control-Max-Age` rubrik preflight-svar anges till värdet för det här attributet och påverka användaragenten möjlighet att cache före flygning svar.|Nej|0|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
-   **Princip-scope:** API, igen  
  
##  <a name="JSONP"></a> JSONP  
 Den `jsonp` Grupprincip lägger till JSON med stöd för utfyllnad (JSONP) till en åtgärd eller ett API för att tillåta anrop mellan domäner från JavaScript-webbläsarbaserade klienter. JSONP är en metod som används i JavaScript-program att begärandedata från en server i en annan domän. JSONP kringgår den begränsning som tillämpas av de flesta webbläsare där åtkomst till webbsidor måste vara i samma domän.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Om du anropar metoden utan parametern återanrop? cb = XXX returneras oformaterad JSON (utan en Omslutning för anrop av funktionen).  
  
 Om du lägger till parametern återanrop `?cb=XXX` returneras ett JSONP-resultat, wrapping ursprungliga JSON resultat runt Återanropsfunktionen som `XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|jsonp|Rotelement.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|motringning parameternamn|Domänerna JavaScript funktionsanropet föregås av det fullständigt kvalificerade domännamnet där funktionen finns.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** utgående  
-   **Princip-scope:** globala, produkt, API, igen  
  
## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   