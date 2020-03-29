---
title: Felhantering i Azure API Management-principer | Microsoft-dokument
description: Lär dig hur du svarar på feltillstånd som kan uppstå under bearbetningen av begäranden i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902447"
---
# <a name="error-handling-in-api-management-policies"></a>Felhantering i API Management-principer

Genom att `ProxyError` tillhandahålla ett objekt tillåter Azure API Management utgivare att svara på feltillstånd, som kan uppstå under bearbetning av begäranden. Objektet `ProxyError` nås via [kontexten. Egenskapen LastError](api-management-policy-expressions.md#ContextVariables) och kan användas `on-error` av principer i principavsnittet. Den här artikeln innehåller en referens för felhanteringsfunktionerna i Azure API Management.

## <a name="error-handling-in-api-management"></a>Felhantering i API Management

Principer i Azure API Management `inbound` `backend`är `outbound`indelade i , , och `on-error` avsnitt som visas i följande exempel.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Under bearbetningen av en begäran utförs inbyggda steg tillsammans med alla principer som är i omfånget för begäran. Om ett fel uppstår hoppar bearbetningen omedelbart till principavsnittet. `on-error`
Principavsnittet `on-error` kan användas i alla scope. API-utgivare kan konfigurera anpassat beteende som att logga felet till händelsehubbar eller skapa ett nytt svar för att återgå till anroparen.

> [!NOTE]
> Avsnittet `on-error` finns inte i principer som standard. Om du `on-error` vill lägga till avsnittet i en princip bläddrar du till önskad princip i principredigeraren och lägger till det. Mer information om hur du konfigurerar principer finns [i Principer i API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).
>
> Om det `on-error` inte finns något avsnitt får de som ringer 400 eller 500 HTTP-svar om ett feltillstånd inträffar.

### <a name="policies-allowed-in-on-error"></a>Principer som tillåts vid fel

Följande principer kan användas `on-error` i principavsnittet.

-   [Välja](api-management-advanced-policies.md#choose)
-   [set-variabel](api-management-advanced-policies.md#set-variable)
-   [hitta och ersätta](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [retur-svar](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-metod](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [skicka-begäran](api-management-advanced-policies.md#SendRequest)
-   [skicka envägsbegäran](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-till-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-till-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-till-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

När ett fel inträffar och `on-error` kontrollen hoppar till principavsnittet lagras felet i [kontext. Egenskapen LastError,](api-management-policy-expressions.md#ContextVariables) som kan nås `on-error` av principer i avsnittet . LastError har följande egenskaper.

| Namn       | Typ   | Beskrivning                                                                                               | Krävs |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | sträng | Namnger det element där felet uppstod. Kan vara antingen princip eller ett inbyggt pipeline-stegnamn.      | Ja      |
| `Reason`   | sträng | Maskinvänlig felkod, som kan användas vid felhantering.                                       | Inga       |
| `Message`  | sträng | Beskrivning av läsbart på människa.                                                                         | Ja      |
| `Scope`    | sträng | Namn på det scope där felet uppstod och kan vara en av "global", "produkt", "api" eller "operation" | Inga       |
| `Section`  | sträng | Avsnittsnamn där felet uppstod. Möjliga värden: "inkommande", "backend", "utgående" eller "on-error".      | Inga       |
| `Path`     | sträng | Anger kapslad princip, till exempel "välj[3]/när[2]".                                                 | Inga       |
| `PolicyId` | sträng | Värdet för `id` attributet, om det anges av kunden, på principen där felet uppstod             | Inga       |

> [!TIP]
> Du kan komma åt statuskoden via kontexten. Response.StatusCode.

> [!NOTE]
> Alla principer har `id` ett valfritt attribut som kan läggas till rotelementet i principen. Om det här attributet finns i en princip när ett feltillstånd inträffar, kan värdet för attributet hämtas med hjälp av egenskapen. `context.LastError.PolicyId`

## <a name="predefined-errors-for-built-in-steps"></a>Fördefinierade fel för inbyggda steg

Följande fel är fördefinierade för feltillstånd som kan uppstå under utvärderingen av inbyggda bearbetningssteg.

| Källa        | Villkor                                 | Orsak                  | Meddelande                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguration | Uri matchar inte med något API eller någon åtgärd | OperationIntensning       | Det gick inte att matcha inkommande begäran med en åtgärd.                                                                      |
| auktorisering | Prenumerationsnyckeln har inte angetts             | PrenumerationKeyNotFound | Åtkomst nekad på grund av att prenumerationsnyckeln saknas. Se till att inkludera prenumerationsnyckeln när du gör begäranden till det här API:et. |
| auktorisering | Värdet för prenumerationsnyckeln är ogiltigt         | SubscriptionKeyInvalid  | Åtkomst nekad på grund av ogiltig prenumerationsnyckel. Se till att ange en giltig nyckel för en aktiv prenumeration.            |
| multipel | Nedströmsanslutning (från en klient till en API Management gateway) avbröts av klienten medan begäran väntade | KlientanslutningFailure | multipel |
| multipel | Uppströmsanslutning (från en API Management-gateway till en serverd-tjänst) har inte upprättats eller avbröts av serverda | BackendConnectionFailure | multipel |
| multipel | Körningsundantag hade inträffat under utvärderingen av ett visst uttryck | UttryckVärdeVärdesättningFailure | multipel |

## <a name="predefined-errors-for-policies"></a>Fördefinierade fel för principer

Följande fel är fördefinierade för feltillstånd som kan uppstå under principutvärdering.

| Källa       | Villkor                                                       | Orsak                    | Meddelande                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| hastighetsgräns   | Hastighetsgränsen har överskridits                                             | RateLimitExceed         | Hastighetsgränsen överskrids                                                                                                               |
| kvot        | Kvoten har överskridits                                                  | KvotFördlöt             | Slut på kvot för samtalsvolym. Kvoten fylls på i xx:xx:xx. -eller- Av bandbreddskvot. Kvoten fylls på i xx:xx:xx. |
| Jsonp        | Parametervärdet för motringning är ogiltigt (innehåller fel tecken) | MotringningParameterInvalid  | Värdet för motringningsparametern {motringningsparameter-namn} är inte en giltig JavaScript-identifierare.                                          |
| ip-filter    | Det gick inte att tolka anropar-IP från begäran                          | FailedToParseCallerIP     | Det gick inte att upprätta IP-adressen för anroparen. Åtkomst nekad.                                                                        |
| ip-filter    | Anropar-IP finns inte i listan över tillåtna                                | AnropareIpNotAllowed        | Anropar-IP-adress {ip-address} är inte tillåten. Åtkomst nekad.                                                                        |
| ip-filter    | Anroparens IP finns i en blockerad lista                                    | AnroparBlockerad           | Anroparens IP-adress är blockerad. Åtkomst nekad.                                                                                         |
| check-header | Obligatoriskt huvud som inte visas eller värdet saknas               | HeaderNotFound (Olikartade)            | Det gick inte att hitta namnet {header-name} i begäran. Åtkomst nekad.                                                                    |
| check-header | Obligatoriskt huvud som inte visas eller värdet saknas               | HeaderValueNotAllowed     | Huvudet {header-name} värdet {header-value} är inte tillåtet. Åtkomst nekad.                                                          |
| validera-jwt | Jwt token saknas i begäran                                 | TokenNotFound (TokenNotFound)             | JWT hittades inte i begäran. Åtkomst nekad.                                                                                         |
| validera-jwt | Signaturvalidering misslyckades                                     | TokenSignatureInvalid     | <meddelande från JVM-biblioteket\>. Åtkomst nekad.                                                                                          |
| validera-jwt | Ogiltig målgrupp                                                | TokenAudienceNotAllowed   | <meddelande från JVM-biblioteket\>. Åtkomst nekad.                                                                                          |
| validera-jwt | Ogiltig utfärdare                                                  | TokenIssuerNotAllowed     | <meddelande från JVM-biblioteket\>. Åtkomst nekad.                                                                                          |
| validera-jwt | Token har upphört att gälla                                                   | TokenExpired              | <meddelande från JVM-biblioteket\>. Åtkomst nekad.                                                                                          |
| validera-jwt | Signaturnyckeln löstes inte av ID                            | TokenSignatureKeyNotFound | <meddelande från JVM-biblioteket\>. Åtkomst nekad.                                                                                          |
| validera-jwt | Obligatoriska anspråk saknas från token                          | TokenClaimNotFound        | JWT token saknar följande påståenden: <c1\>, <\>c2 , ... Åtkomst nekad.                                                            |
| validera-jwt | Anspråksvärden som inte stämmer överens                                           | TokenClaimValueNotAllowed | Anspråk {anspråksnamn} värdet {anspråksvärde} är inte tillåtet. Åtkomst nekad.                                                             |
| validera-jwt | Andra valideringsfel                                       | JwtInvalid (på)                | <meddelande från JVM-biblioteket\>                                                                                                          |
| vidarebefordran eller skicka begäran | HTTP-svarsstatuskod och -huvuden togs inte emot från servergången i den konfigurerade tidsgränsen | Timeout | multipel |

## <a name="example"></a>Exempel

Ställa in en API-princip till:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

och skicka en obehörig begäran kommer att resultera i följande svar:

![Obehörigt felsvar](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API-hantering](api-management-howto-policies.md)
-   [Omvandla API:er](transform-api.md)
-   [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
-   [Policyexempel](policy-samples.md)
