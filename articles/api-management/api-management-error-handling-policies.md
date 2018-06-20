---
title: Hantera fel i Azure API Management-principer | Microsoft Docs
description: Lär dig att svara på felförhållanden som kan uppstå under bearbetning av begäranden i Azure API Management.
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
ms.openlocfilehash: 73609e802eceea6aa94d77cef6ca1d654264973d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265015"
---
# <a name="error-handling-in-api-management-policies"></a>Hantera fel i API Management-principer

Genom att tillhandahålla en `ProxyError` objekt Azure API Management gör utgivare att svara på felförhållanden som kan uppstå under bearbetning av begäranden. Den `ProxyError` objektet nås via de [kontext. LastError](api-management-policy-expressions.md#ContextVariables) egenskap och kan användas av principer i den `on-error` princip. Den här artikeln innehåller en referens för felet funktioner för hantering i Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Hantera fel i API Management

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
  
Inbyggda steg utförs under bearbetning av en begäran, tillsammans med eventuella principer som ingår i omfattningen för begäran. Om ett fel inträffar bearbetning omedelbart leder till den `on-error` princip.  
Den `on-error` principavdelningen kan användas på ett scope. API-utgivare kan konfigurera anpassade beteende som loggning av fel i händelsehubbar eller skapa ett nytt svar ska returneras till anroparen.  
  
> [!NOTE]
>  Den `on-error` avsnittet finns inte i principer som standard. Att lägga till den `on-error` till en princip, bläddra till önskad principen i Redigeraren för grupprinciper och lägger till den. Mer information om hur du konfigurerar principer finns [principer i API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Om det finns inga `on-error` avsnittet anropare får 400 eller 500 http-svarsmeddelanden om ett fel inträffar.  
  
### <a name="policies-allowed-in-on-error"></a>Principer som tillåts i vid fel

 Följande principer kan användas i den `on-error` princip.  
  
-   [Välj](api-management-advanced-policies.md#choose)  
-   [Ange variabel](api-management-advanced-policies.md#set-variable)  
-   [Sök och Ersätt](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [returnera svar](api-management-advanced-policies.md#ReturnResponse)  
-   [set-huvud](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-metod](api-management-advanced-policies.md#SetRequestMethod)  
-   [Ange status](api-management-advanced-policies.md#SetStatus)  
-   [Skicka begäran](api-management-advanced-policies.md#SendRequest)  
-   [en-sätt-begäran om att skicka](api-management-advanced-policies.md#SendOneWayRequest)  
-   [loggen till eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [JSON-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [XML-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 När ett fel inträffar och kontroll flyttas till den `on-error` principer för felet lagras i [kontext. LastError](api-management-policy-expressions.md#ContextVariables) -egenskap som kan användas av principer i den `on-error` avsnitt. LastError har följande egenskaper.  
  
| Namn     | Typ   | Beskrivning                                                                                               | Krävs |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Källa   | sträng | Trots att elementet där felet uppstod. Kan vara antingen princip eller en inbyggda pipeline Stegnamn.     | Ja      |
| Orsak   | sträng | Datorn eget felkod som kan användas i felhantering.                                       | Nej       |
| Meddelande  | sträng | Läsbart felbeskrivning.                                                                         | Ja      |
| Omfång    | sträng | Namnet på omfattningen där felet uppstod och kan vara någon av ”globala”, ”product”, ”api” eller ”åtgärden” | Nej       |
| Section  | sträng | Avsnittsnamnet där felet uppstod. Möjliga värden: ”inkommande”, ”serverdel”, ”utgående” eller ”på fel”.       | Nej       |
| Sökväg     | sträng | Anger kapslade princip, till exempel ”Välj [3] / när [2]”.                                                        | Nej       |
| PolicyId | sträng | Värdet för den `id` attribut om anges av kunden, för principen där felet uppstod             | Nej       |

> [!TIP]
> Du kan komma åt statuskoden via kontext. Response.StatusCode.  

> [!NOTE]
> Alla principer som har en valfri `id` attribut som kan läggas till rotelement i principen. Om det här attributet visas i en princip när ett fel inträffar, värdet för attributet kan hämtas med hjälp av den `context.LastError.PolicyId` egenskapen.

## <a name="predefined-errors-for-built-in-steps"></a>Fördefinierade fel för inbyggda steg  
 Följande fel är fördefinierade för fel som kan uppstå under utvärderingen av inbyggda bearbetning steg.  
  
| Källa        | Tillstånd                                 | Orsak                  | Meddelande                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguration | URI: N matchar inte några API eller åtgärden | OperationNotFound       | Det går inte att matcha inkommande begäran till en åtgärd.                                                                      |
| Auktorisering | Prenumerationen nyckeln har inte angetts             | SubscriptionKeyNotFound | Åtkomst nekades på grund av prenumeration nyckel saknas. Se till att inkludera prenumeration nyckel vid begäranden till denna API. |
| Auktorisering | Nyckelvärdet för prenumerationen är ogiltig         | SubscriptionKeyInvalid  | Åtkomst nekades på grund av ogiltiga prenumerationen nyckel. Se till att ange en giltig nyckel för en aktiv prenumeration.            |
  
## <a name="predefined-errors-for-policies"></a>Fördefinierade fel för principer  
 Följande fel är fördefinierade för felförhållanden som kan uppstå under utvärderingen.  
  
| Källa       | Tillstånd                                                       | Orsak                    | Meddelande                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| gräns för överföringshastigheten   | Hastigheten med vilken överskriden                                             | RateLimitExceeded         | Hastighetsbegränsning har överskridits                                                                                                               |
| kvot        | Kvoten överskreds                                                  | QuotaExceeded             | Slut på kvot för samtalsvolym. Kvoten fylls i xx:xx:xx. - eller - Out-of-bandbredd kvoten. Kvoten fylls i xx:xx:xx. |
| hanteras jsonp        | Motringning parametervärdet är ogiltigt (innehåller felaktiga tecken) | CallbackParameterInvalid  | Värdet för parametern återanrop {--återanrop} är inte en giltig JavaScript-identifierare.                                          |
| IP-filter    | Det gick inte att parsa anroparen IP från begäran                          | FailedToParseCallerIP     | Det gick inte att upprätta IP-adress för anroparen. Åtkomst nekad.                                                                        |
| IP-filter    | Anroparen IP finns inte i listan över tillåtna                                | CallerIpNotAllowed        | Anroparen IP-adress {ip-adress} är inte tillåtet. Åtkomst nekad.                                                                        |
| IP-filter    | Anroparen IP finns i blockeringslistan                                    | CallerIpBlocked           | Anroparen IP-adress blockeras. Åtkomst nekad.                                                                                         |
| Kontrollera-huvud | Obligatorisk rubrik som visas inte eller värde saknas               | HeaderNotFound            | Huvudet {huvudnamn} hittades inte i begäran. Åtkomst nekad.                                                                    |
| Kontrollera-huvud | Obligatorisk rubrik som visas inte eller värde saknas               | HeaderValueNotAllowed     | Huvudvärde {huvudnamn} för {huvudvärde} är inte tillåtet. Åtkomst nekad.                                                          |
| Validera jwt | Jwt-token saknas i begäran                                 | TokenNotFound             | JWT hittades inte i begäran. Åtkomst nekad.                                                                                         |
| Validera jwt | Signaturverifieringen misslyckades                                     | TokenSignatureInvalid     | < meddelande från jwt biblioteket\>. Åtkomst nekad.                                                                                          |
| Validera jwt | Ogiltig målgrupp                                                | TokenAudienceNotAllowed   | < meddelande från jwt biblioteket\>. Åtkomst nekad.                                                                                          |
| Validera jwt | Ogiltig utfärdare                                                  | TokenIssuerNotAllowed     | < meddelande från jwt biblioteket\>. Åtkomst nekad.                                                                                          |
| Validera jwt | Token har upphört att gälla                                                   | TokenExpired              | < meddelande från jwt biblioteket\>. Åtkomst nekad.                                                                                          |
| Validera jwt | Signaturnyckel löstes inte med ID                            | TokenSignatureKeyNotFound | < meddelande från jwt biblioteket\>. Åtkomst nekad.                                                                                          |
| Validera jwt | Det saknas nödvändiga anspråk från token                          | TokenClaimNotFound        | JWT-token saknas följande anspråk: < c1\>, < c2\>,... Åtkomst nekad.                                                            |
| Validera jwt | Felaktig matchning av anspråk värden                                           | TokenClaimValueNotAllowed | Anspråksvärdet {anspråkets namn} för {Anspråksvärdet} är inte tillåtet. Åtkomst nekad.                                                             |
| Validera jwt | Andra verifieringsfel                                       | JwtInvalid                | < meddelande från jwt-biblioteket\>                                                                                                          |

## <a name="example"></a>Exempel

Ange en API-princip till:

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

![Obehörig felsvaret](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   