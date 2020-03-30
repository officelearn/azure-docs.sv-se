---
title: Principer för begränsning av Azure API Management-åtkomst | Microsoft-dokument
description: Lär dig mer om de principer för åtkomstbegränsning som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266122"
---
# <a name="api-management-access-restriction-policies"></a>Principer för åtkomstbegränsning i API Management

Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>Principer för åtkomstbegränsning

-   [Kontrollera HTTP-huvudet](api-management-access-restriction-policies.md#CheckHTTPHeader) - Framtvingar förekomsten och/eller värdet för ett HTTP-huvud.
-   [Begränsa samtalsfrekvensen per prenumeration](api-management-access-restriction-policies.md#LimitCallRate) - Förhindrar API-användningstoppar genom att begränsa samtalsfrekvensen, per prenumeration.
-   [Begränsa samtalsfrekvensen per tangent](#LimitCallRateByKey) - Förhindrar API-användningstoppar genom att begränsa samtalsfrekvensen, per nyckel.
-   [Begränsa anropar-IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filter (tillåter/nekar) anrop från specifika IP-adresser och/eller adressintervall.
-   [Ange användningskvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – Gör att du kan tillämpa en samtalsvolym för förnybar tid eller livstidssamtal och/eller bandbredd, per prenumeration.
-   [Ange användningskvot efter nyckel](#SetUsageQuotaByKey) - Gör att du kan tillämpa en samtalsvolym för förnybar tid eller livstidssamtal och/eller bandbredd, per nyckel.
-   [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) - Framtvingar förekomsten och giltigheten av en JWT som extraherats från antingen ett angivet HTTP-huvud eller en angiven frågeparameter.

> [!TIP]
> Du kan använda principer för åtkomstbegränsning i olika scope för olika ändamål. Du kan till exempel skydda hela API:et `validate-jwt` med AAD-autentisering genom att tillämpa principen `claims` på API-nivå eller så kan du använda det på API-åtgärdsnivå och använda för mer detaljerad kontroll.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>Kontrollera HTTP-huvud

Använd `check-header` principen för att framtvinga att en begäran har ett angivet HTTP-huvud. Du kan också kontrollera om huvudet har ett visst värde eller söka efter ett intervall med tillåtna värden. Om kontrollen misslyckas avslutar principen bearbetning av begäran och returnerar den HTTP-statuskod och det felmeddelande som anges av principen.

### <a name="policy-statement"></a>Policyuttalande

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Exempel

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Element

| Namn         | Beskrivning                                                                                                                                   | Krävs |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | Rotelementet.                                                                                                                                 | Ja      |
| värde        | Tillåtet HTTP-huvudvärde. När flera värdeelement anges anses kontrollen vara en lycka om något av värdena är en matchning. | Inga       |

### <a name="attributes"></a>Attribut

| Namn                       | Beskrivning                                                                                                                                                            | Krävs | Default |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| felmeddelandet misslyckades | Felmeddelande som ska returneras i HTTP-svarstexten om huvudet inte finns eller har ett ogiltigt värde. Det här meddelandet måste ha några specialtecken som har rymts på rätt sätt. | Ja      | Ej tillämpligt     |
| misslyckades-check-httpcode      | HTTP-statuskod returneras om huvudet inte finns eller har ett ogiltigt värde.                                                                                        | Ja      | Ej tillämpligt     |
| sidhuvudet                | Namnet på HTTP-huvudet som ska kontrolleras.                                                                                                                                  | Ja      | Ej tillämpligt     |
| ignorera-fall                | Kan ställas in på Sant eller Falskt. Om värdet True case anges ignoreras när huvudvärdet jämförs med uppsättningen godtagbara värden.                                    | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, utgående

-   **Principomfattningar:** alla scope

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>Begränsa samtalsfrekvensen per prenumeration

Principen `rate-limit` förhindrar API-användningsstegring per prenumeration genom att begränsa samtalsfrekvensen till ett angivet nummer per angiven tidsperiod. När den här principen utlöses `429 Too Many Requests` får anroparen en svarsstatuskod.

> [!IMPORTANT]
> Den här principen kan bara användas en gång per principdokument.
>
> [Principuttryck](api-management-policy-expressions.md) kan inte användas i något av principattributen för den här principen.

> [!CAUTION]
> På grund av den distribuerade karaktären av begränsning arkitektur, hastighetsbegränsning är aldrig helt korrekt. Skillnaden mellan konfigurerad och det verkliga antalet tillåtna begäranden varierar beroende på begäran volym och hastighet, serverad svarstid och andra faktorer.

### <a name="policy-statement"></a>Policyuttalande

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Namn       | Beskrivning                                                                                                                                                                                                                                                                                              | Krävs |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| hastighetsgräns | Rotelementet.                                                                                                                                                                                                                                                                                            | Ja      |
| api        | Lägg till ett eller flera av dessa element om du vill införa en anropsgräns för API:er i produkten. Gränsvärden för produkt- och API-anropshastighet tillämpas oberoende av dem. API kan refereras `name` antingen `id`via eller . Om båda attributen `id` tillhandahålls kommer `name` de att användas och ignoreras.                    | Inga       |
| Drift  | Lägg till ett eller flera av dessa element för att införa en anropsgräns för åtgärder inom ett API. Gränser för produkt-, API- och åtgärdsanropshastighet tillämpas oberoende av dem. Drift kan refereras `name` antingen `id`via eller . Om båda attributen `id` tillhandahålls kommer `name` de att användas och ignoreras. | Inga       |

### <a name="attributes"></a>Attribut

| Namn           | Beskrivning                                                                                           | Krävs | Default |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| namn           | Namnet på API:et som hastighetsgränsen ska tillämpas för.                                                | Ja      | Ej tillämpligt     |
| Samtal          | Det maximala totala antalet tillåtna samtal under `renewal-period`det tidsintervall som anges i . | Ja      | Ej tillämpligt     |
| förnyelseperiod | Tidsperioden i sekunder efter vilken kvoten återställs.                                              | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande

-   **Principomfattningar:** produkt, api, drift

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>Begränsa samtalsfrekvensen efter nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig på **förbrukningsnivån** för API Management.

Principen `rate-limit-by-key` förhindrar API-användningsstegring per nyckel genom att begränsa anropsfrekvensen till ett angivet nummer per angiven tidsperiod. Nyckeln kan ha ett godtyckligt strängvärde och tillhandahålls vanligtvis med hjälp av ett principuttryck. Valfritt inökningsvillkor kan läggas till för att ange vilka begäranden som ska räknas in mot gränsen. När den här principen utlöses `429 Too Many Requests` får anroparen en svarsstatuskod.

Mer information och exempel på den här principen finns i [Avancerad begäran begränsning med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> På grund av den distribuerade karaktären av begränsning arkitektur, hastighetsbegränsning är aldrig helt korrekt. Skillnaden mellan konfigurerad och det verkliga antalet tillåtna begäranden varierar beroende på begäran volym och hastighet, serverad svarstid och andra faktorer.

### <a name="policy-statement"></a>Policyuttalande

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exempel

I följande exempel knappas hastighetsgränsen av anroparens IP-adress.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Namn              | Beskrivning   | Krävs |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Namn                | Beskrivning                                                                                           | Krävs | Default |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Samtal               | Det maximala totala antalet tillåtna samtal under `renewal-period`det tidsintervall som anges i . | Ja      | Ej tillämpligt     |
| motnyckel         | Nyckeln som ska användas för räntegränsprincipen.                                                             | Ja      | Ej tillämpligt     |
| steg-villkor | Det booleska uttrycket som anger om begäran`true`ska räknas in i kvoten ( ).        | Inga       | Ej tillämpligt     |
| förnyelseperiod      | Tidsperioden i sekunder efter vilken kvoten återställs.                                              | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande

-   **Principomfattningar:** alla scope

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>Begränsa Ips för uppringaren

Principfiltren `ip-filter` (tillåter/nekar) anrop från specifika IP-adresser och/eller adressintervall.

### <a name="policy-statement"></a>Policyuttalande

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exempel

I följande exempel tillåter principen endast begäranden som kommer antingen från den enda IP-adressen eller det ip-adresser som anges

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Element

| Namn                                      | Beskrivning                                         | Krävs                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Rotelementet.                                       | Ja                                                            |
| adress                                   | Anger en enda IP-adress som du vill filtrera.   | Minst en `address` `address-range` eller ett element krävs. |
| adressintervall från="adress" till="adress" | Anger ett intervall med IP-adress som du vill filtrera. | Minst en `address` `address-range` eller ett element krävs. |

### <a name="attributes"></a>Attribut

| Namn                                      | Beskrivning                                                                                 | Krävs                                           | Default |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| adressintervall från="adress" till="adress" | Ett intervall med IP-adresser som du vill tillåta eller neka åtkomst för.                                        | Krävs när `address-range` elementet används. | Ej tillämpligt     |
| ip-filter action="tillåt &#124; förbjuda"    | Anger om anrop ska tillåtas eller inte för angivna IP-adresser och intervall. | Ja                                                | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande
-   **Principomfattningar:** alla scope

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>Ange användningskvot efter prenumeration

Principen `quota` tillämpar en appellvolym för förnybar eller livstidssamtal och/eller bandbreddskvot per prenumeration.

> [!IMPORTANT]
> Den här principen kan bara användas en gång per principdokument.
>
> [Principuttryck](api-management-policy-expressions.md) kan inte användas i något av principattributen för den här principen.

### <a name="policy-statement"></a>Policyuttalande

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Namn      | Beskrivning                                                                                                                                                                                                                                                                                  | Krävs |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvot     | Rotelementet.                                                                                                                                                                                                                                                                                | Ja      |
| api       | Lägg till ett eller flera av dessa element för att införa anropskvot för API:er inom produkten. Produkt- och API-anropskvoter tillämpas oberoende av dem. API kan refereras `name` antingen `id`via eller . Om båda attributen `id` tillhandahålls kommer `name` de att användas och ignoreras.                    | Inga       |
| Drift | Lägg till ett eller flera av dessa element för att införa anropskvot för åtgärder inom ett API. Produkt-, API- och åtgärdsanropskvoter tillämpas oberoende av dem. Drift kan refereras `name` antingen `id`via eller . Om båda attributen `id` tillhandahålls kommer `name` de att användas och ignoreras. | Inga       |

### <a name="attributes"></a>Attribut

| Namn           | Beskrivning                                                                                               | Krävs                                                         | Default |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| namn           | Namnet på det API eller den åtgärd som kvoten gäller för.                                             | Ja                                                              | Ej tillämpligt     |
| Bandbredd      | Det maximala totala antalet kilobyte som tillåts under `renewal-period`det tidsintervall som anges i . | Antingen `calls` `bandwidth`, eller båda tillsammans måste anges. | Ej tillämpligt     |
| Samtal          | Det maximala totala antalet tillåtna samtal under `renewal-period`det tidsintervall som anges i .     | Antingen `calls` `bandwidth`, eller båda tillsammans måste anges. | Ej tillämpligt     |
| förnyelseperiod | Tidsperioden i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande
-   **Policyomfattningar:** produkt

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>Ange användningskvot efter nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig på **förbrukningsnivån** för API Management.

Principen `quota-by-key` tillämpar en kvot för samtalsvolym för förnybar energi eller livstidssamtal och/eller bandbredd, på grundval av en viktig grund. Nyckeln kan ha ett godtyckligt strängvärde och tillhandahålls vanligtvis med hjälp av ett principuttryck. Valfritt ökningsvillkor kan läggas till för att ange vilka begäranden som ska räknas in i kvoten. Om flera principer skulle öka samma nyckelvärde ökas det bara en gång per begäran. När samtalsgränsen har nåtts får `403 Forbidden` anroparen en svarsstatuskod.

Mer information och exempel på den här principen finns i [Avancerad begäran begränsning med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Policyuttalande

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exempel

I följande exempel knappas kvoten av anroparens IP-adress.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

| Namn  | Beskrivning   | Krävs |
| ----- | ------------- | -------- |
| kvot | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Namn                | Beskrivning                                                                                               | Krävs                                                         | Default |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Bandbredd           | Det maximala totala antalet kilobyte som tillåts under `renewal-period`det tidsintervall som anges i . | Antingen `calls` `bandwidth`, eller båda tillsammans måste anges. | Ej tillämpligt     |
| Samtal               | Det maximala totala antalet tillåtna samtal under `renewal-period`det tidsintervall som anges i .     | Antingen `calls` `bandwidth`, eller båda tillsammans måste anges. | Ej tillämpligt     |
| motnyckel         | Nyckeln som ska användas för kvotprincipen.                                                                      | Ja                                                              | Ej tillämpligt     |
| steg-villkor | Det booleska uttrycket som anger om begäran`true`ska räknas in i kvoten ( )             | Inga                                                               | Ej tillämpligt     |
| förnyelseperiod      | Tidsperioden i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande
-   **Principomfattningar:** alla scope

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>Validera JWT

Principen `validate-jwt` tillämpar förekomsten och giltigheten av en JWT som extraherats från antingen ett angivet HTTP-huvud eller en angiven frågeparameter.

> [!IMPORTANT]
> Principen `validate-jwt` kräver att `exp` det registrerade anspråket inkluderas i `require-expiration-time` JWT-token, `false`såvida inte attributet anges och anges till .
> Principen `validate-jwt` stöder HS256- och RS256-signeringsalgoritmer. För HS256 måste nyckeln anges i enlighet med principen i det base64-kodade formuläret. För RS256 måste nyckeln anges via en slutpunkt för öppen ID-konfiguration.
> Principen `validate-jwt` stöder token krypterade med symmetriska nycklar med hjälp av följande krypteringsalgoritmer A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Policyuttalande

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>Exempel

#### <a name="simple-token-validation"></a>Enkel tokenvalidering

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory token validering

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C token validering

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Auktorisera åtkomst till åtgärder baserat på tokenanspråk

Det här exemplet visar hur du använder [principen Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) för att auktorisera åtkomst till åtgärder baserat på tokenanspråksvärde.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>Element

| Element             | Beskrivning                                                                                                                                                                                                                                                                                                                                           | Krävs |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validera-jwt        | Rotelementet.                                                                                                                                                                                                                                                                                                                                         | Ja      |
| Publik           | Innehåller en lista över godkända målgruppsanspråk som kan finnas på token. Om det finns flera målgruppsvärden provas varje värde tills antingen alla är uttömda (i vilket fall valideringen misslyckas) eller tills man lyckas. Minst en målgrupp måste anges.                                                                     | Inga       |
| emittent-signering-nycklar | En lista över Base64-kodade säkerhetsnycklar som används för att validera signerade token. Om det finns flera säkerhetsnycklar provas varje nyckel tills antingen alla är uttömda (i vilket fall valideringen misslyckas) eller tills en lyckas (användbart för token rollover). Nyckelelement har `id` ett valfritt `kid` attribut som används för att matcha mot anspråk.               | Inga       |
| dekrypteringsnycklar     | En lista över Base64-kodade nycklar som används för att dekryptera token. Om det finns flera säkerhetsnycklar provas varje nyckel tills antingen alla nycklar är uttömda (i vilket fall valideringen misslyckas) eller tills en nyckel lyckas. Nyckelelement har `id` ett valfritt `kid` attribut som används för att matcha mot anspråk.                                                 | Inga       |
| Emittenter             | En lista över godtagbara huvudnamn som utfärdade token. Om flera utfärdarvärden finns, provas varje värde tills antingen alla är uttömda (i vilket fall valideringen misslyckas) eller tills man lyckas.                                                                                                                                         | Inga       |
| openid-config       | Elementet som används för att ange en kompatibel slutpunkt för öppen ID-konfiguration från vilken signeringsnycklar och utfärdare kan erhållas.                                                                                                                                                                                                                        | Inga       |
| krav på krav     | Innehåller en lista över anspråk som förväntas finnas på token för att den ska anses giltig. När `match` attributet är `all` inställt på varje anspråksvärde i principen måste finnas i token för att valideringen ska lyckas. När `match` attributet är `any` inställt på minst ett anspråk måste finnas i token för att valideringen ska lyckas. | Inga       |

### <a name="attributes"></a>Attribut

| Namn                            | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                            | Krävs                                                                         | Default                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| klocksnedställning                      | Gått. Används för att ange maximal förväntad tidsskillnad mellan systemklockorna för tokenutfärdaren och API Management-instansen.                                                                                                                                                                                                                                                                                                               | Inga                                                                               | 0 sekunder                                                                         |
| misslyckat-validering-felmeddelande | Felmeddelande som ska returneras i HTTP-svarstexten om JWT inte skickar validering. Det här meddelandet måste ha några specialtecken som har rymts på rätt sätt.                                                                                                                                                                                                                                                                                                 | Inga                                                                               | Standardfelmeddelandet beror på valideringsproblem, till exempel "JWT inte närvarande". |
| misslyckades-validering-httpcode      | HTTP-statuskod för att returnera om JWT inte klarar valideringen.                                                                                                                                                                                                                                                                                                                                                                                         | Inga                                                                               | 401                                                                               |
| sidhuvudet                     | Namnet på HTTP-huvudet som innehåller token.                                                                                                                                                                                                                                                                                                                                                                                                         | En `header-name`av `query-parameter-name` `token-value` , eller måste anges. | Ej tillämpligt                                                                               |
| fråga-parameter-namn            | Namnet på frågeparametern som innehåller token.                                                                                                                                                                                                                                                                                                                                                                                                     | En `header-name`av `query-parameter-name` `token-value` , eller måste anges. | Ej tillämpligt                                                                               |
| token-värde                     | Uttryck som returnerar en sträng som innehåller JWT-token                                                                                                                                                                                                                                                                                                                                                                                                     | En `header-name`av `query-parameter-name` `token-value` , eller måste anges. | Ej tillämpligt                                                                               |
| id                              | Attributet `id` för `key` elementet kan du ange strängen `kid` som ska matchas mot anspråk i token (om sådan finns) för att ta reda på lämplig nyckel som ska användas för signaturvalidering.                                                                                                                                                                                                                                           | Inga                                                                               | Ej tillämpligt                                                                               |
| Matcha                           | Attributet `match` för `claim` elementet anger om varje anspråksvärde i principen måste finnas i token för att valideringen ska lyckas. Möjliga värden:<br /><br /> - `all`- Varje anspråksvärde i principen måste finnas i token för att valideringen ska lyckas.<br /><br /> - `any`- Minst ett anspråksvärde måste finnas i token för att valideringen ska lyckas.                                                       | Inga                                                                               | all                                                                               |
| kräver-utgångstid         | Boolean. Anger om ett anspråk för förfallodatum krävs i token.                                                                                                                                                                                                                                                                                                                                                                               | Inga                                                                               | true                                                                              |
| kräver-system                  | Namnet på tokenschemat, t.ex. När det här attributet har angetts säkerställer principen att det angivna schemat finns i värdet auktoriseringshuvud.                                                                                                                                                                                                                                                                                    | Inga                                                                               | Ej tillämpligt                                                                               |
| kräver signerade tokens           | Boolean. Anger om en token måste signeras.                                                                                                                                                                                                                                                                                                                                                                                           | Inga                                                                               | true                                                                              |
| Avgränsare                       | Sträng. Anger en avgränsare (t.ex. "") som ska användas för att extrahera en uppsättning värden från ett anspråk med flera värden.                                                                                                                                                                                                                                                                                                                                          | Inga                                                                               | Ej tillämpligt                                                                               |
| url                             | Öppna URL för ID-konfigurationsslutpunkt där metadata för öppen ID-konfiguration kan erhållas. Svaret ska vara enligt specifikationer som definieras`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`på URL: . För Azure Active Directory använder `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` du följande URL: ersätter `contoso.onmicrosoft.com`ditt katalogklientnamn, t.ex. | Ja                                                                              | Ej tillämpligt                                                                               |
| output-token-variabel-namn      | Sträng. Namn på kontextvariabel som får tokenvärde som ett objekt av typen [`Jwt`](api-management-policy-expressions.md) vid lyckad tokenvalidering                                                                                                                                                                                                                                                                                     | Inga                                                                               | Ej tillämpligt                                                                               |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande
-   **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API-hantering](api-management-howto-policies.md)
-   [Omvandla API:er](transform-api.md)
-   [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
-   [Policyexempel](policy-samples.md)
