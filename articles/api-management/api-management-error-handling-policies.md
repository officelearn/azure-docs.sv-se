---
title: Felhantering i Azure API Management-principer | Microsoft Docs
description: Lär dig hur du svarar på feltillstånd som kan uppstå under bearbetning av begäranden i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 2bde63bb668188936b3dd3cf5ecbf3b8c604eb95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564327"
---
# <a name="error-handling-in-api-management-policies"></a>Felhantering i API Management-principer

Genom att tillhandahålla en `ProxyError` objekt kan Azure API Management kan utgivare att svara på feltillstånd som kan uppstå under behandling av begäranden. Den `ProxyError` objektet är tillgängligt via den [kontext. LastError](api-management-policy-expressions.md#ContextVariables) egenskap och kan användas av principer i den `on-error` princip. Den här artikeln innehåller en referens för felet hantering av funktioner i Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Felhantering i API Management

 Principer i Azure API Management är indelade i `inbound`, `backend`, `outbound`, och `on-error` avsnitten som visas i följande exempel.  
  
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
  
Inbyggda steg utförs under bearbetning av en begäran, tillsammans med eventuella principer som är inom omfånget för begäran. Om ett fel inträffar, bearbetning av omedelbart leder till den `on-error` princip.  
Den `on-error` princip kan användas i alla omfånget. API-utgivare kan konfigurera anpassade beteende, till exempel loggning felet till event hubs eller skapa ett nytt svar ska returneras till anroparen.  
  
> [!NOTE]
>  Den `on-error` avsnittet finns inte i principer som standard. Att lägga till den `on-error` till en princip, bläddra till önskad princip i principredigeraren och lägger till den. Mer information om hur du konfigurerar principer finns i [principer i API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Om det finns inga `on-error` avsnittet anropare får 400 eller 500 HTTP-svarsmeddelandena om ett fel inträffar.  
  
### <a name="policies-allowed-in-on-error"></a>Principer som tillåts i vid fel

 Följande principer kan användas i den `on-error` princip.  
  
-   [Välj](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-metod](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 När ett fel inträffar och kontroll som leder till den `on-error` principer för felet lagras i [kontext. LastError](api-management-policy-expressions.md#ContextVariables) egenskapen, som kan användas av principer i den `on-error` avsnittet. LastError har följande egenskaper.  
  
| Namn     | Typ   | Beskrivning                                                                                               | Krävs |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Källa   | string | Elementet där felet uppstod-namn. Kan vara antingen princip eller en inbyggd pipeline Stegnamn.     | Ja      |
| Orsak   | string | Dator-vänlig felkoden som skulle kunna användas i felhantering.                                       | Nej       |
| Meddelande  | string | Läsbart felbeskrivning.                                                                         | Ja      |
| Scope    | string | Namnet på området där felet uppstod och kan vara någon av ”global”, ”product”, ”-api” eller ”åtgärden” | Nej       |
| Section  | string | Namn på avsnittet där felet uppstod. Möjliga värden: ”inkommande”, ”serverdel”, ”utgående” eller ”på fel”.       | Nej       |
| `Path`     | string | Anger kapslade princip, till exempel ”Välj [3] / när [2]”.                                                        | Nej       |
| `PolicyId` | string | Värdet på den `id` attributet, om har angetts av kunden, för principen där felet uppstod             | Nej       |

> [!TIP]
> Du kan komma åt statuskoden via kontext. Response.StatusCode.  

> [!NOTE]
> Alla principer som har en valfri `id` attribut som kan läggas till rotelementet i principen. Om det här attributet visas i en princip när ett fel inträffar, värdet för attributet kan hämtas med hjälp av den `context.LastError.PolicyId` egenskapen.

## <a name="predefined-errors-for-built-in-steps"></a>Fördefinierade fel för inbyggda steg  
 Följande fel är fördefinierade för felvillkor som kan uppstå under utvärderingen av inbyggda bearbetningssteg.  
  
| Källa        | Tillstånd                                 | Orsak                  | Meddelande                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguration | URI: N matchar inte till API: et eller åtgärden | OperationNotFound       | Det går inte att matcha inkommande begäran till en åtgärd.                                                                      |
| Auktorisering | Prenumerationsnyckel som inte angetts             | SubscriptionKeyNotFound | Åtkomst nekad på grund av saknad prenumerationsnyckel. Se till att inkludera prenumerationsnyckel vid begäranden till den här API: et. |
| Auktorisering | Nyckelvärdet för prenumerationen är ogiltig         | SubscriptionKeyInvalid  | Åtkomst nekad på grund av ogiltigt prenumerations-nyckel. Se till att ange en giltig nyckel för en aktiv prenumeration.            |
  
## <a name="predefined-errors-for-policies"></a>Fördefinierade fel för principer  
 Följande fel är fördefinierade för feltillstånd som kan uppstå under utvärderingen.  
  
| Källa       | Tillstånd                                                       | Orsak                    | Meddelande                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| Hastighetsbegränsning   | Priset har överskridits                                             | RateLimitExceeded         | Hastighetsbegränsning överskriden                                                                                                               |
| kvot        | Kvoten har överskridits                                                  | QuotaExceeded             | Slut på kvot för samtalsvolym. Kvot fylls i xx:xx:xx. - eller - elimineras bandbredd kvot. Kvot fylls i xx:xx:xx. |
| jsonp        | Parametervärdet för återanrop är ogiltigt (innehåller felaktiga tecken) | CallbackParameterInvalid  | Värdet för motringningen parametern {Motringnings-parametern-name} är inte en giltig JavaScript-identifierare.                                          |
| ip-filter    | Det gick inte att parsa anroparen IP från begäran                          | FailedToParseCallerIP     | Det gick inte att upprätta IP-adress för anroparen. Åtkomst nekad.                                                                        |
| ip-filter    | Anroparen IP finns inte i listan över tillåtna                                | CallerIpNotAllowed        | Anropares IP-adress {ip-adress} är inte tillåtet. Åtkomst nekad.                                                                        |
| ip-filter    | Anroparen IP finns i blockeringslistan                                    | CallerIpBlocked           | Anropares IP-adress blockeras. Åtkomst nekad.                                                                                         |
| Kontrollera rubrik | Obligatorisk rubrik visas inte eller värde saknas               | HeaderNotFound            | Rubriken {huvud-name} hittades inte i begäran. Åtkomst nekad.                                                                    |
| Kontrollera rubrik | Obligatorisk rubrik visas inte eller värde saknas               | HeaderValueNotAllowed     | Huvudvärde {huvud-name} av {huvudvärde} är inte tillåtet. Åtkomst nekad.                                                          |
| validate-jwt | Jwt-token saknas i begäran                                 | TokenNotFound             | JWT inte hittades i begäran. Åtkomst nekad.                                                                                         |
| validate-jwt | Det gick inte att verifiera signaturen                                     | TokenSignatureInvalid     | < meddelande från jwt-biblioteket\>. Åtkomst nekad.                                                                                          |
| validate-jwt | Ogiltig målgrupp                                                | TokenAudienceNotAllowed   | < meddelande från jwt-biblioteket\>. Åtkomst nekad.                                                                                          |
| validate-jwt | Ogiltig utfärdare                                                  | TokenIssuerNotAllowed     | < meddelande från jwt-biblioteket\>. Åtkomst nekad.                                                                                          |
| validate-jwt | Token har upphört att gälla                                                   | TokenExpired              | < meddelande från jwt-biblioteket\>. Åtkomst nekad.                                                                                          |
| validate-jwt | Signaturnyckeln kunde inte lösas av ID                            | TokenSignatureKeyNotFound | < meddelande från jwt-biblioteket\>. Åtkomst nekad.                                                                                          |
| validate-jwt | Begärda anspråk saknas i token                          | TokenClaimNotFound        | JWT-token saknas för följande anspråk: < c1\>, < c2\>,... Åtkomst nekad.                                                            |
| validate-jwt | Matchningsfel för anspråk värden                                           | TokenClaimValueNotAllowed | Anspråksvärdet {anspråk-name} för {Anspråksvärdet} är inte tillåtet. Åtkomst nekad.                                                             |
| validate-jwt | Andra verifieringsfel                                       | JwtInvalid                | < meddelande från jwt-biblioteket\>                                                                                                          |

## <a name="example"></a>Exempel

Ställa in en API-princip:

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

och skicka en obehörig begäran resulterar i följande svar:

![Behörighetsfel svar](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   