---
title: Azure API Management kors domän principer | Microsoft Docs
description: Lär dig mer om de kors domän principer som är tillgängliga för användning i Azure API Management.
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
ms.date: 07/14/2020
ms.author: apimpm
ms.openlocfilehash: 99784e43130b70554c05ff79a10993f2b6eebbde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499621"
---
# <a name="api-management-cross-domain-policies"></a>Korsdomänprinciper för API Management
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Kors domän principer

- [Tillåt kors domän anrop](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – gör API: et tillgängligt från webbläsarbaserade Adobe Flash-och Microsoft Silverlight-baserade klienter.
- [CORS](api-management-cross-domain-policies.md#CORS) – lägger till CORS-stöd (Cross-Origin Resource Sharing) till en åtgärd eller ett API för att tillåta kors domän anrop från webbläsarbaserade klienter.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – lägger till JSON med JSONP-stöd (JSON with utfyllnad) till en åtgärd eller ett API för att tillåta kors domän anrop från Java Script browser-baserade klienter.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Tillåt anrop mellan domäner
Använd `cross-domain` principen för att göra API: et tillgängligt från webbläsarbaserade klienter i Adobe Flash och Microsoft Silverlight.

### <a name="policy-statement"></a>Princip kommentar

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
|mellan domäner|Rot element. Underordnade element måste följa [specifikationen för Adobe Cross-domain policy File](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Ja|

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande
- **Princip omfattningar:** alla omfattningar

## <a name="cors"></a><a name="CORS"></a> CORS
`cors`Principen lägger till CORS-stöd (Cross-Origin Resource Sharing) till en åtgärd eller ett API för att tillåta kors domän anrop från webbläsarbaserade klienter.

CORS gör det möjligt för en webbläsare och en server att samverka och avgöra om det ska gå att tillåta vissa cross-origin-begäranden (d.v.s. XMLHttpRequests-anrop som görs från Java Script på en webb sida till andra domäner). Detta ger större flexibilitet än att bara tillåta begär Anden om samma ursprung, men det är säkrare än att tillåta alla cross-origin-begäranden.

Du måste använda CORS-principen för att aktivera den interaktiva konsolen i Developer-portalen. Mer information finns i [dokumentationen för Developer Portal](./api-management-howto-developer-portal.md#cors) .

### <a name="policy-statement"></a>Princip kommentar

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
I det här exemplet visas hur du stöder för-flygnings begär Anden, t. ex. de som har anpassade sidhuvuden eller andra metoder än GET och POST. Om du vill ha stöd för anpassade rubriker och ytterligare HTTP-verb använder du `allowed-methods` `allowed-headers` avsnitten och som visas i följande exempel.

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
|CORS|Rot element.|Ja|E.t.|
|tillåtna-ursprung|Innehåller `origin` element som beskriver tillåtna ursprung för frågor mellan domäner. `allowed-origins` kan innehålla antingen ett enda `origin` element som anger `*` att alla ursprung eller ett eller flera `origin` element som innehåller en URI ska tillåtas.|Ja|E.t.|
|ursprung|Värdet kan antingen vara `*` att tillåta alla ursprung eller en URI som anger ett enda ursprung. URI: n måste innehålla ett schema, en värd och en port.|Ja|Om porten utelämnas i en URI används port 80 för HTTP och port 443 för HTTPS.|
|tillåtna metoder|Det här elementet krävs om andra metoder än GET eller POST tillåts. Innehåller `method` element som anger de HTTP-verb som stöds. Värdet `*` anger alla metoder.|Inga|Om det här avsnittet inte finns stöds GET och POST.|
|metod|Anger ett HTTP-verb.|Minst ett- `method` element krävs om `allowed-methods` avsnittet finns.|E.t.|
|tillåtna – rubriker|Det här elementet innehåller `header` element som anger namn på de huvuden som kan tas med i begäran.|Inga|E.t.|
|exponera – rubriker|Det här elementet innehåller `header` element som anger namn på de rubriker som ska vara tillgängliga för klienten.|Inga|Saknas|
|sidhuvud|Anger ett rubrik namn.|Minst ett `header` element krävs i `allowed-headers` eller `expose-headers` om avsnittet är tillgängligt.|E.t.|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|Tillåt-autentiseringsuppgifter|`Access-Control-Allow-Credentials`Rubriken i preflight-svaret anges till värdet för det här attributet och påverkar klientens möjlighet att skicka autentiseringsuppgifter i kors domän begär Anden.|Inga|falskt|
|preflight-resultat-max-ålder|`Access-Control-Max-Age`Rubriken i preflight-svaret ställs in på värdet för det här attributet och påverkar användar agentens möjlighet att cachelagra svar före flygning.|Inga|0|

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** inkommande
- **Princip omfattningar:** alla omfattningar

## <a name="jsonp"></a><a name="JSONP"></a> JSONP
`jsonp`Principen lägger till JSON med stöd för JSONP-stöd (JSON with utfyllnad) i en åtgärd eller ett API för att tillåta kors domän anrop från Java Script browser-baserade klienter. JSONP är en metod som används i JavaScript-program för att begära data från en server i en annan domän. JSONP kringgår begränsningen som tillämpas av de flesta webbläsare där åtkomst till webb sidor måste finnas i samma domän.

### <a name="policy-statement"></a>Princip kommentar

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Exempel

```xml
<jsonp callback-parameter-name="cb" />
```

Om du anropar-metoden utan callback-parametern? CB = XXX kommer den att returnera vanlig JSON (utan ett funktions anrop).

Om du lägger till en callback `?cb=XXX` -parameter returnerar den ett JSONP-resultat och omsluter de ursprungliga JSON-resultaten runt motringningsfunktionen som `XYZ('<json result goes here>');`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|JSONP|Rot element.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|motanrop-parameter-Name|JavaScript-funktionen för Cross-Domain anropas med det fullständigt kvalificerade domän namnet där funktionen finns.|Ja|E.t.|

### <a name="usage"></a>Användning
Den här principen kan användas i följande princip [avsnitt](./api-management-howto-policies.md#sections) och [områden](./api-management-howto-policies.md#scopes).

- **Princip avsnitt:** utgående
- **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Princip exempel](policy-samples.md)
