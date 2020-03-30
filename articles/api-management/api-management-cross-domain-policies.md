---
title: Principer för mellandomäner för Azure API Management | Microsoft-dokument
description: Lär dig mer om de principer för flera domäner som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265992"
---
# <a name="api-management-cross-domain-policies"></a>Korsdomänprinciper för API Management
Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Principer för flera domäner

- [Tillåt samtal mellan domäner](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – Gör API:et tillgängligt från webbläsarbaserade Adobe Flash- och Microsoft Silverlight-klienter.
- [CORS](api-management-cross-domain-policies.md#CORS) - Lägger till stöd för resursdelning mellan ursprung (CORS) i en åtgärd eller ett API för att tillåta samtal mellan domäner från webbläsarbaserade klienter.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - Lägger till JSON med utfyllnad (JSONP) stöd till en åtgärd eller ett API för att tillåta cross-domain samtal från JavaScript webbläsarbaserade klienter.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Tillåt samtal mellan domäner
Använd `cross-domain` principen för att göra API:et tillgängligt från webbläsarbaserade Adobe Flash- och Microsoft Silverlight-klienter.

### <a name="policy-statement"></a>Policyuttalande

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Exempel

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|mellan domäner|Rotelementet. Underordnade element måste överensstämma med [specifikationen för Adobe-principen mellan domäner](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Policyavsnitt:** inkommande
- **Principomfattningar:** alla scope

## <a name="cors"></a><a name="CORS"></a>CORS
Principen `cors` lägger till stöd för resursdelning mellan ursprung (CORS) i en åtgärd eller ett API för att tillåta samtal mellan domäner från webbläsarbaserade klienter.

MED CORS kan en webbläsare och en server interagera och avgöra om specifika begäranden med flera ursprung ska tillåtas (dvs. XMLHttpRequests-samtal som görs från JavaScript på en webbsida till andra domäner). Detta möjliggör mer flexibilitet än att bara tillåta begäranden med samma ursprung, men är säkrare än att tillåta alla begäranden med flera ursprung.

### <a name="policy-statement"></a>Policyuttalande

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
Det här exemplet visar hur du stöder begäranden före flygning, till exempel de med andra anpassade rubriker eller metoder än GET och POST. Om du vill stödja anpassade rubriker och `allowed-methods` `allowed-headers` ytterligare HTTP-verb använder du avsnitten och som visas i följande exempel.

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

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|cors (cors)|Rotelementet.|Ja|Ej tillämpligt|
|tillåtna ursprung|Innehåller `origin` element som beskriver det tillåtna ursprunget för begäranden mellan domäner. `allowed-origins`kan innehålla antingen `origin` ett enda `*` element som anger att `origin` tillåta ursprung eller ett eller flera element som innehåller en URI.|Ja|Ej tillämpligt|
|ursprung|Värdet kan vara `*` antingen att tillåta alla ursprung, eller en URI som anger ett enda ursprung. URI måste innehålla ett schema, en värd och en port.|Ja|Om porten utelämnas i en URI används port 80 för HTTP och port 443 används för HTTPS.|
|tillåtna metoder|Det här elementet krävs om andra metoder än GET eller POST tillåts. Innehåller `method` element som anger http-verb som stöds. Värdet `*` anger alla metoder.|Inga|Om det här avsnittet inte finns stöds GET och POST.|
|metod|Anger ett HTTP-verb.|Minst ett `method` element krävs `allowed-methods` om avsnittet finns.|Ej tillämpligt|
|tillåtna rubriker|Det här `header` elementet innehåller element som anger namnen på de rubriker som kan inkluderas i begäran.|Inga|Ej tillämpligt|
|exponera-rubriker|Det här `header` elementet innehåller element som anger namnen på de rubriker som ska vara tillgängliga för klienten.|Inga|Ej tillämpligt|
|sidhuvud|Anger ett rubriknamn.|Minst ett `header` element krävs `allowed-headers` `expose-headers` i eller om avsnittet finns.|Ej tillämpligt|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|tillåt-autentiseringsuppgifter|Huvudet `Access-Control-Allow-Credentials` i preflight-svaret ställs in på värdet för det här attributet och påverkar klientens möjlighet att skicka autentiseringsuppgifter i begäranden mellan domäner.|Inga|false|
|preflight-result-max-age|Huvudet `Access-Control-Max-Age` i preflight-svaret ställs in på värdet för det här attributet och påverkar användaragentens förmåga att cachelagra svar före flygning.|Inga|0|

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Policyavsnitt:** inkommande
- **Principomfattningar:** alla scope

## <a name="jsonp"></a><a name="JSONP"></a>Jsonp
Principen `jsonp` lägger till JSON med stöd för utfyllnad (JSONP) i en åtgärd eller ett API för att tillåta samtal mellan domäner från JavaScript-webbläsarbaserade klienter. JSONP är en metod som används i JavaScript-program för att begära data från en server i en annan domän. JSONP kringgår begränsningen som tillämpas av de flesta webbläsare där åtkomst till webbsidor måste finnas i samma domän.

### <a name="policy-statement"></a>Policyuttalande

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exempel

```xml
<jsonp callback-parameter-name="cb" />
```

Om du anropar metoden utan motringningsparametern ?cb=XXX returnerar den vanligt JSON (utan funktionsanropsomslag).

Om du lägger `?cb=XXX` till motringningsparametern returnerar den ett JSONP-resultat och omsluter de ursprungliga JSON-resultaten runt motringningsfunktionen som`XYZ('<json result goes here>');`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Jsonp|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|callback-parameter-namn|JavaScript-funktionsanropet mellan domäner föregås av det fullständigt kvalificerade domännamnet där funktionen finns.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **Principavsnitt:** utgående
- **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
