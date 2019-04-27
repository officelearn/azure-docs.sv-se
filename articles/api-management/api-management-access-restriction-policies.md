---
title: Azure API Management principer för begränsning av åtkomst | Microsoft Docs
description: Läs mer om åtkomst begränsning av principer som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: acc9f83923c8fdaae98cc55bc6baf62f56f2116b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798599"
---
# <a name="api-management-access-restriction-policies"></a>API Management-principer för begränsning av åtkomst

Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a> Principer för begränsning av åtkomst

-   [Kontrollera HTTP-huvud](api-management-access-restriction-policies.md#CheckHTTPHeader) -framtvingar förekomsten och/eller värdet för ett HTTP-huvud.
-   [Begränsa anropsfrekvensen per prenumeration](api-management-access-restriction-policies.md#LimitCallRate) – förhindrar att API-användning vid tillfälliga toppar genom att begränsa anropsfrekvensen per prenumeration procentuellt utifrån.
-   [Begränsa anropsfrekvensen av nyckeln](#LimitCallRateByKey) – förhindrar att API-användning vid tillfälliga toppar genom att begränsa anropsfrekvens per nyckel regelbundet.
-   [Begränsa anroparen IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar)-anrop från specifika IP-adresser och/eller -adressintervall.
-   [Ange användningskvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – du kan tillämpa en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per prenumeration.
-   [Ange användningskvot av nyckeln](#SetUsageQuotaByKey) – du kan tillämpa en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per nyckel.
-   [Verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) -tillämpar förekomst och giltigheten hos en JWT som extraheras från ett angivna HTTP-sidhuvud eller en angiven frågeparameter.

## <a name="CheckHTTPHeader"></a> Kontrollera HTTP-huvud

Använd den `check-header` princip för att genomdriva att en begäran har ett angivna HTTP-huvud. Du kan också kontrollera om rubriken har ett specifikt värde eller Sök efter en tillåtna värdeintervallet. Om den inte principen avslutar behandling av begäranden och returnerar det HTTP-kod och fel statusmeddelande som angetts av principen.

### <a name="policy-statement"></a>Principframställning

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

| Namn         | Beskrivning                                                                                                                                   | Obligatoriskt |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Kontrollera rubrik | Rotelement.                                                                                                                                 | Ja      |
| value        | Tillåtna värde för HTTP-huvud. När flera värde anges är, betraktas kontrollen lyckas om något av värdena finns en matchning. | Nej       |

### <a name="attributes"></a>Attribut

| Namn                       | Beskrivning                                                                                                                                                            | Obligatoriskt | Standard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | Felmeddelande att returnera i HTTP-svarstext om huvudet finns inte eller har ett ogiltigt värde. Det här meddelandet måste ha några specialtecken som korrekt undantaget. | Ja      | Gäller inte     |
| Det gick inte-kontroll-httpcode      | HTTP-statuskod ska returneras om huvudet finns inte eller har ett ogiltigt värde.                                                                                        | Ja      | Gäller inte     |
| header-name                | Namnet på det HTTP-huvud för att kontrollera.                                                                                                                                  | Ja      | Gäller inte     |
| Ignorera skiftläge                | Kan anges till True eller False. Om inställd på Sant fall ignoreras när huvudets värde jämförs mot uppsättningen av godkända värden.                                    | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående

-   **Princip-scope:** globala, produkt, API, igen

## <a name="LimitCallRate"></a> Begränsa anropsfrekvensen per prenumeration

Den `rate-limit` princip förhindrar API-användningstoppar på basis av per prenumeration genom att begränsa anropsfrekvensen till ett angivet antal per angiven tidsperiod. När den här principen aktiveras anroparen får en `429 Too Many Requests` Svarets statuskod.

> [!IMPORTANT]
> Den här principen kan användas endast en gång per dokument.
>
> [Principuttryck](api-management-policy-expressions.md) kan inte användas i något av principen attribut för den här principen.

### <a name="policy-statement"></a>Principframställning

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

| Namn      | Beskrivning                                                                                                                                                                                                                                                                                              | Obligatoriskt |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Ställ in gräns | Rotelement.                                                                                                                                                                                                                                                                                            | Ja      |
| api       | Lägg till en eller flera av dessa element att införa en anropsgränsen på API: er inom produkten. Produkt- och API: et anropsfrekvens begränsningar tillämpas oberoende av varandra. API: et kan vara refereras via `name` eller `id`. Om båda attributen har angetts, `id` ska användas och `name` kommer att ignoreras.                    | Nej       |
| åtgärd | Lägg till en eller flera av dessa element att införa en anropsgränsen på åtgärder i ett API. Produkt, API och åtgärd anropsfrekvens begränsningar tillämpas oberoende av varandra. Åtgärden kan vara refereras via `name` eller `id`. Om båda attributen har angetts, `id` ska användas och `name` kommer att ignoreras. | Nej       |

### <a name="attributes"></a>Attribut

| Namn           | Beskrivning                                                                                           | Obligatoriskt | Standard |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| namn           | Namnet på API: et som du vill tillämpa gräns för överföringshastigheten i.                                                | Ja      | Gäller inte     |
| anrop          | Det maximala totalt antalet anrop tillåts under en tidsperiod som anges i den `renewal-period`. | Ja      | Gäller inte     |
| förnyelseperioden | Hur lång tid i sekunder efter vilken kvoten återställs.                                              | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande

-   **Princip-scope:** produkt

## <a name="LimitCallRateByKey"></a> Begränsa anropsfrekvensen av nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den **förbrukning** nivå för API Management.

Den `rate-limit-by-key` princip förhindrar API-användningstoppar på basis av per nyckel genom att begränsa anropsfrekvensen till ett angivet antal per angiven tidsperiod. Nyckeln kan ha en godtycklig sträng-värde och anges vanligtvis med en principuttryck. Valfritt steg villkor kan läggas till ange vilka begäranden som ska räknas mot gränsen. När den här principen aktiveras anroparen får en `429 Too Many Requests` Svarets statuskod.

Mer information och exempel på den här principen kan du se [avancerad begränsning av förfrågningar med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Principframställning

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Exempel

I exemplet nedan ställs in gräns för överföringshastigheten i med anropares IP-adress.

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

| Namn      | Beskrivning   | Obligatoriskt |
| --------- | ------------- | -------- |
| Ställ in gräns | Rotelement. | Ja      |

### <a name="attributes"></a>Attribut

| Namn                | Beskrivning                                                                                           | Obligatoriskt | Standard |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| anrop               | Det maximala totalt antalet anrop tillåts under en tidsperiod som anges i den `renewal-period`. | Ja      | Gäller inte     |
| avdelningar nyckel         | Nyckeln som ska användas för frekvensbegränsningsprincipen.                                                             | Ja      | Gäller inte     |
| increment-condition | Den booleska uttryck som anger om begäran ska räknas mot kvoten (`true`).        | Nej       | Gäller inte     |
| förnyelseperioden      | Hur lång tid i sekunder efter vilken kvoten återställs.                                              | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande

-   **Princip-scope:** globala, produkt, API, igen

## <a name="RestrictCallerIPs"></a> Begränsa anroparen IP-adresser

Den `ip-filter` princip filtrerar (tillåter/nekar)-anrop från specifika IP-adresser och/eller -adressintervall.

### <a name="policy-statement"></a>Principframställning

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Exempel

I följande exempel tillåter principen endast begäranden som kommer från IP-adress eller IP-adressintervall som angetts

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Element

| Namn                                      | Beskrivning                                         | Obligatoriskt                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Rotelement.                                       | Ja                                                            |
| adress                                   | Anger en IP-adress som ska filtreras.   | Minst en `address` eller `address-range` elementet krävs. |
| address-range from="address" to="address" | Anger ett intervall med IP-adress som ska filtreras. | Minst en `address` eller `address-range` elementet krävs. |

### <a name="attributes"></a>Attribut

| Namn                                      | Beskrivning                                                                                 | Obligatoriskt                                           | Standard |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | Ett intervall med IP-adresser för att tillåta eller neka åtkomst för.                                        | Krävs när den `address-range` elementet används. | Gäller inte     |
| ip-filter action="allow &#124; forbid"    | Anger om anrop ska tillåtas eller inte för den angivna IP-adresser och intervall. | Ja                                                | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande
-   **Princip-scope:** globala, produkt, API, igen

## <a name="SetUsageQuota"></a> Ange användningskvot per prenumeration

Den `quota` principen tvingar fram en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per prenumeration.

> [!IMPORTANT]
> Den här principen kan användas endast en gång per dokument.
>
> [Principuttryck](api-management-policy-expressions.md) kan inte användas i något av principen attribut för den här principen.

### <a name="policy-statement"></a>Principframställning

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

| Namn      | Beskrivning                                                                                                                                                                                                                                                                                  | Obligatoriskt |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvot     | Rotelement.                                                                                                                                                                                                                                                                                | Ja      |
| api       | Lägg till en eller flera av dessa element att införa anrop kvot på API: er i produkten. Produkt- och API-anrop kvoter tillämpas oberoende av varandra. API: et kan vara refereras via `name` eller `id`. Om båda attributen har angetts, `id` ska användas och `name` kommer att ignoreras.                    | Nej       |
| åtgärd | Lägg till en eller flera av dessa element att införa anrop kvot på åtgärder i ett API. Produkt, API och åtgärd anrop kvoter tillämpas oberoende av varandra. Åtgärden kan vara refereras via `name` eller `id`. Om båda attributen har angetts, `id` ska användas och `name` kommer att ignoreras. | Nej       |

### <a name="attributes"></a>Attribut

| Namn           | Beskrivning                                                                                               | Obligatoriskt                                                         | Standard |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| namn           | Namnet på API: et eller åtgärden som kvoten gäller.                                             | Ja                                                              | Gäller inte     |
| bandbredd      | Det högsta totala antalet kilobyte tillåts under en tidsperiod som anges i den `renewal-period`. | Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges. | Gäller inte     |
| anrop          | Det maximala totalt antalet anrop tillåts under en tidsperiod som anges i den `renewal-period`.     | Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges. | Gäller inte     |
| förnyelseperioden | Hur lång tid i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande
-   **Princip-scope:** produkt

## <a name="SetUsageQuotaByKey"></a> Ange användningskvot av nyckel

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den **förbrukning** nivå för API Management.

Den `quota-by-key` principen tvingar fram en förnyas eller livslängd anrop volym och/eller bandbredd kvoten på basis av per nyckel. Nyckeln kan ha en godtycklig sträng-värde och anges vanligtvis med en principuttryck. Valfritt steg villkor kan läggas till ange vilka begäranden som ska räknas mot kvoten. Om flera principer skulle öka samma nyckelvärde, ökas den bara en gång per begäran. När anropet gränsen har nåtts anroparen får en `403 Forbidden` Svarets statuskod.

Mer information och exempel på den här principen kan du se [avancerad begränsning av förfrågningar med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [Principuttryck](api-management-policy-expressions.md) kan inte användas i något av principen attribut för den här principen.

### <a name="policy-statement"></a>Principframställning

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Exempel

I exemplet nedan ställs in kvoten med anropares IP-adress.

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

| Namn  | Beskrivning   | Obligatoriskt |
| ----- | ------------- | -------- |
| kvot | Rotelement. | Ja      |

### <a name="attributes"></a>Attribut

| Namn                | Beskrivning                                                                                               | Obligatoriskt                                                         | Standard |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandbredd           | Det högsta totala antalet kilobyte tillåts under en tidsperiod som anges i den `renewal-period`. | Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges. | Gäller inte     |
| anrop               | Det maximala totalt antalet anrop tillåts under en tidsperiod som anges i den `renewal-period`.     | Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges. | Gäller inte     |
| avdelningar nyckel         | Nyckeln som ska användas för kvot-principen.                                                                      | Ja                                                              | Gäller inte     |
| increment-condition | Den booleska uttryck som anger om begäran ska räknas mot kvoten (`true`)             | Nej                                                               | Gäller inte     |
| förnyelseperioden      | Hur lång tid i sekunder efter vilken kvoten återställs.                                                  | Ja                                                              | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande
-   **Princip-scope:** globala, produkt, API, igen

## <a name="ValidateJWT"></a> Verifiera JWT

Den `validate-jwt` princip tillämpar förekomst och giltigheten hos en JWT extraheras från antingen angivna HTTP-huvud eller angivna frågeparameter.

> [!IMPORTANT]
> Den `validate-jwt` principen kräver att den `exp` registrerade anspråk som ingår i JWT-token, såvida inte `require-expiration-time` attributet anges och tilldelas `false`.
> Den `validate-jwt` principen har stöd för HS256 och RS256 Signeringsalgoritmer. För HS256 tillhandahåller nyckeln infogade i princip i base64-kodat format. Nyckeln måste vara tillhandahåller via en slutpunkt för konfiguration av öppna ID för RS256.
> Den `validate-jwt` principen har stöd för token som krypterats med symmetriska nycklar med hjälp av följande krypteringsalgoritmer A128CBC HS256 A192CBC HS384 A256CBC HS512.

### <a name="policy-statement"></a>Principframställning

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
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
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

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory-tokenvalidering

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C-tokenvalidering

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Bevilja åtkomst till åtgärder baserat på tokenanspråken

Det här exemplet visar hur du använder den [verifiera JWT](api-management-access-restriction-policies.md#ValidateJWT) principen för att auktorisera åtkomst till åtgärder baserat på tokenanspråken värde.

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

#### <a name="azure-mobile-services-token-validation"></a>Azure Mobile Services-tokenvalidering

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Element

| Element             | Beskrivning                                                                                                                                                                                                                                                                                                                                           | Obligatoriskt |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | Rotelement.                                                                                                                                                                                                                                                                                                                                         | Ja      |
| målgrupper           | Innehåller en lista över godkända målgrupp anspråk som kan finnas i token. Om det finns flera målgruppsvärden så testas varje värde förrän antingen alla tömts (i så fall verifieringen misslyckas) eller tills ett lyckas. Du måste ange minst en målgrupp.                                                                     | Nej       |
| issuer-signing-keys | En lista över Base64-kodad säkerhetsnycklar som används för att verifiera signerade token. Om flera säkerhetsnycklar finns kommer varje nyckel testas förrän antingen alla tömts (i så fall verifieringen misslyckas) eller tills ett lyckas (användbart förnya token). Viktiga element har en valfri `id` attributet som används för att matcha mot `kid` anspråk.               | Nej       |
| dekryptering-nycklar     | En lista över Base64-kodade nycklar som används för att dekryptera token. Om det finns flera säkerhetsnycklar, är varje nyckel prövas tills antingen alla nycklar är slut (i så fall verifieringen misslyckas) eller en nyckel lyckas. Viktiga element har en valfri `id` attributet som används för att matcha mot `kid` anspråk.                                                 | Nej       |
| certifikatutfärdare             | En lista över godkända säkerhetsobjekt som utfärdade token. Om det finns flera utfärdarvärden så testas varje värde förrän antingen alla tömts (i så fall verifieringen misslyckas) eller tills ett lyckas.                                                                                                                                         | Nej       |
| openid-config       | Det element som används för att ange en kompatibel öppna ID configuration slutpunkt som registrerar nycklar och utfärdaren kan hämtas.                                                                                                                                                                                                                        | Nej       |
| krävs anspråk     | Innehåller en lista över anspråk som förväntas finnas i token för att den ska vara giltigt. När den `match` är attributet `all` varje anspråksvärde i principen måste finnas i en token för verifiering ska lyckas. När den `match` är attributet `any` måste finnas minst ett anspråk i token för verifiering ska lyckas. | Nej       |
| zumo-master-key     | Huvudnyckel för token som utfärdas av Azure mobiltjänster                                                                                                                                                                                                                                                                                                 | Nej       |

### <a name="attributes"></a>Attribut

| Namn                            | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                            | Obligatoriskt                                                                         | Standard                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | TimeSpan. Använd för att ange högsta förväntade tidsskillnaden mellan tokenutfärdaren systemklockor och API Management-instans.                                                                                                                                                                                                                                                                                                               | Nej                                                                               | 0 sekunder                                                                         |
| failed-validation-error-message | Felmeddelande att returnera i HTTP-svarstext om JWT inte klarar valideringen. Det här meddelandet måste ha några specialtecken som korrekt undantaget.                                                                                                                                                                                                                                                                                                 | Nej                                                                               | Standardfelmeddelande beror på verifieringsproblem, till exempel ”JWT finns inte”. |
| failed-validation-httpcode      | HTTP-statuskod ska returneras om JWT inte valideras.                                                                                                                                                                                                                                                                                                                                                                                         | Nej                                                                               | 401                                                                               |
| header-name                     | Namnet på det HTTP-huvud som denna token.                                                                                                                                                                                                                                                                                                                                                                                                         | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| query-parameter-name            | Namnet på Frågeparametern denna token.                                                                                                                                                                                                                                                                                                                                                                                                     | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| token-värde                     | Uttryck som returnerar en sträng som innehåller JWT-token                                                                                                                                                                                                                                                                                                                                                                                                     | En av `header-name`, `query-parameter-name` eller `token-value` måste anges. | Gäller inte                                                                               |
| id                              | Den `id` attribut på den `key` element kan du ange den sträng som matchas mot `kid` anspråk i token (om sådan finns) att ta reda på lämplig nyckeln som ska användas för att verifiera signaturen.                                                                                                                                                                                                                                           | Nej                                                                               | Gäller inte                                                                               |
| matchning                           | Den `match` attribut på den `claim` elementet anger om varje anspråksvärde i principen måste finnas i en token för verifiering ska lyckas. Möjliga värden:<br /><br /> - `all` -varje anspråksvärde i principen måste finnas i en token för verifiering ska lyckas.<br /><br /> - `any` -minst en anspråksvärdet måste finnas i en token för verifiering ska lyckas.                                                       | Nej                                                                               | all                                                                               |
| require-expiration-time         | Booleskt värde. Anger om ett anspråk för förfallodatum krävs i token.                                                                                                                                                                                                                                                                                                                                                                               | Nej                                                                               | true                                                                              |
| require-scheme                  | Namnet på token system, t.ex. ”Ägar”. När det här attributet anges garanterar principen som att det angivna schemat finns i värdet för auktoriseringsrubriken.                                                                                                                                                                                                                                                                                    | Nej                                                                               | Gäller inte                                                                               |
| Kräv-signerade-token           | Booleskt värde. Anger om det krävs en token signeras.                                                                                                                                                                                                                                                                                                                                                                                           | Nej                                                                               | true                                                                              |
| avgränsare                       | sträng. Anger en avgränsare (t.ex. ””,) som ska användas för att extrahera en uppsättning värden från ett med flera värden anspråk.                                                                                                                                                                                                                                                                                                                                          | Nej                                                                               | Gäller inte                                                                               |
| url                             | Öppna ID configuration slutpunkts-URL från där öppna ID konfigurationsmetadata kan hämtas. Svaret ska vara enligt specifikationer som definierats på URL:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Använd följande URL för Azure Active Directory: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` Ersätt ditt klientnamn katalog, t.ex. `contoso.onmicrosoft.com`. | Ja                                                                              | Gäller inte                                                                               |
output-token-variable-name|sträng. Namnet på sammanhangsvariabeln som ska ta emot token-värde som ett objekt av typen [ `Jwt` ](api-management-policy-expressions.md) vid lyckad token-validering|Nej|Gäller inte

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande
-   **Princip-scope:** globala, produkt, API, igen

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

-   [Principer i API Management](api-management-howto-policies.md)
-   [Transformera API: er](transform-api.md)
-   [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
-   [Princip-exempel](policy-samples.md)
