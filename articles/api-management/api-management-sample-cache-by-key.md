---
title: Anpassade cachelagring i Azure API Management
description: "Lär dig att cachelagra objekt som nyckel i Azure API Management"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 4a41e4e0be44e855ead253ad76fe5a3af52070ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="custom-caching-in-azure-api-management"></a>Anpassade cachelagring i Azure API Management
Azure API Management-tjänsten har inbyggt stöd för [HTTP-svar cachelagring](api-management-howto-cache.md) med resurs-URL som nyckel. Nyckeln kan ändras av huvuden för begäran med hjälp av den `vary-by` egenskaper. Detta är användbart för cachelagring av hela HTTP-svar (aka garantier), men ibland är det praktiskt att cache bara en del av en representation. Den nya [cache-sökning-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) och [cache-lagra-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) principer ger möjlighet att lagra och hämta godtyckliga delar av data från i principdefinitioner. Den här möjligheten också tillför värde till den tidigare introducerades [-begäran om att skicka](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) principen eftersom du kan nu cachelagra svar från externa tjänster.

## <a name="architecture"></a>Arkitektur
API Management-tjänsten använder en delad per klient cache så att när du skalar upp till flera enheter som du kommer fortfarande att få åtkomst till samma cachelagrade data. Men när du arbetar med en distribution i flera regioner finns oberoende cacheminnen inom vart och ett av regionerna. På grund av detta är det viktigt att inte behandla cacheminnet som ett datalager, där det är den enda källan för vissa information. Om du har och senare har valt att dra nytta av flera regioner distributionen, kan sedan kunder med användare som reser förlora åtkomsten till den cachelagrade data.

## <a name="fragment-caching"></a>Cachelagring av fragment
Det finns vissa fall där svar som returneras innehåller en del av data som är dyrt att avgöra och ännu är den senaste för en rimlig tid. Exempelvis bör du en tjänst som skapats av ett flygbolag som innehåller information som rör svarta reservationer, svarta status osv. Om användaren är medlem i flygbolagen punkter programmet, skulle de också ha information om deras aktuella status och hittills utfört ackumulerade. Den här användaren-relaterad information lagras i ett annat system, men kan det vara önskvärt att inkludera den i svar om svarta status och -reservationer som returneras. Detta kan göras med hjälp av en process som kallas cachelagring av fragment. Den primära representationen kan returneras från den ursprungliga servern med hjälp av någon typ av token som indikerar om användarrelaterade-information som ska infogas. 

Överväg följande JSON-svar från en serverdel API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

Och sekundära resursen på `/userprofile/{userid}` ser ut som att,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Vi behöver identifiera som användaren för att avgöra lämpliga användarinformationen att inkludera. Den här mekanismen är implementering beroende. Jag använder exempelvis den `Subject` anspråk för en `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Vi sparar du den `enduserid` värde i en kontext variabel för senare användning. Nästa steg är att avgöra om en tidigare begäran redan har hämtats användarinformationen och lagras i cachen. För den här vi använder den `cache-lookup-value` princip.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Om det finns ingen post i cacheminnet som motsvarar värdet för nyckeln och sedan Nej `userprofile` kontexten variabeln kommer att skapas. Vi kontrollerar att en sökning med hjälp av den `choose` styr flödet för principen.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Om den `userprofile` kontexten variabel finns inte och vi kommer att göra en HTTP-begäran för att hämta den.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

Vi använder den `enduserid` att konstruera profil användarresurs URL-adress. När vi har svaret vi hämtar brödtext utanför svaret och lagra den tillbaka till en variabel i kontexten.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

För att undvika oss att göra denna HTTP-begäran igen, när samma användare gör en annan begäran, lagrar vi användarens profil i cacheminnet.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Värdet lagras i cachen med exakt samma nyckel som vi ursprungligen gjordes ett försök att hämta den med. Den varaktighet som vi vill lagra värdet ska baseras på hur ofta uppdateras ändringar och hur feltoleranta användare till inaktuell information. 

Det är viktigt att tänka på att hämtas från cachen är fortfarande ett out-of-process, nätverksbegäran och eventuellt fortfarande lägga till flera millisekunder begäran. Fördelarna kommer när du fastställer användarens profilinformation tar betydligt längre tid än den på grund av behöva databas frågor eller samlar in information från flera-servrar.

Det sista steget i processen är att uppdatera returnerade svar med vår användarens profilinformation.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Jag har valt att inkludera citattecken som en del av token så att även om Ersätt inte inträffar svaret var fortfarande giltig JSON. Detta var främst underlättar felsökning.

När du kombinerar de här stegen tillsammans är slutresultatet en princip som ser ut som på följande sätt.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Den här cachelagring metoden används främst på webbplatser där HTML består på serversidan så att den kan återges som en enda sida. Men det kan också vara användbart i API: er där klienten kan göra sida HTTP cachelagring eller bör inte att placera det ansvaret på klienten.

Den här samma typ av cachelagring av fragment kan även utföras på backend-webbservrar som använder en Redis cache server, men använder API Management-tjänsten för att utföra arbetet är användbart när cachelagrade fragment kommer från olika-servrar än primärt svar.

## <a name="transparent-versioning"></a>Transparent versionshantering
Det är vanligt för flera olika implementering versioner av en API som stöds vid någon tidpunkt. Detta kanske är att stödja olika miljöer, t.ex. dev, testa, produktion, eller det kan vara att stödja äldre versioner av API: et för ange tid för API-konsumenter att migrera till nyare versioner. 

Ett sätt att hantera detta i stället för att klienten utvecklare kan ändra URL-adresser från `/v1/customers` till `/v2/customers` är att spara i konsumentens profildata vilken version av API: et de för närvarande vill använda och anropa lämpliga backend-URL. Det är nödvändigt att fråga några konfigurationsdata för att fastställa rätt backend-URL att anropa för en viss klient. Vi kan minimera prestandaförsämring med att göra den här sökningen cachelagrar konfigurationsinformationen.

Det första steget är att avgöra den identifierare som används för att konfigurera den önskade versionen. I det här exemplet jag har valt versionen till prenumerationen produktnyckeln. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Sedan gör vi en cache-sökning för att se om det redan har hämtat den önskade klientversionen.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Sedan kontrollerar vi du om det inte att hitta det i cacheminnet.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Om vi inte har vi gå och hämta den.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Extrahera brödtext för svar från svaret.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Lagra den tillbaka i cacheminnet för framtida användning.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Och slutligen uppdatera backend-URL: en för att välja versionen av tjänsten som önskas av klienten.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Principen helt är som följer.

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If we don’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Om du aktiverar API konsumenter att transparent styra vilken backend-version som används av klienter utan att behöva uppdatera och distribuera klienter är en smidig lösning som åtgärdar problem med många API-versioner.

## <a name="tenant-isolation"></a>Klientisolering
I distributioner av större och flera innehavare skapa vissa företag separata grupper för klienter på olika distributioner av backend-maskinvara. Detta minimerar antalet kunder som påverkas av maskinvaruproblem på serverdelen. Det gör också nya programvaruversioner återställas i etapper. Vi är den här backend-arkitekturen transparent för API-konsumenter. Detta kan ske på ett liknande sätt att transparent versionshantering eftersom den är baserad på samma teknik manipulera backend-URL med konfigurationstillstånd per API-nyckel.  

I stället för att returnera en önskade versionen av API: et för varje prenumeration nyckel, returneras en identifierare som gäller en klient för gruppen tilldelade maskinvara. Identifierare kan användas för att konstruera lämpliga backend-URL.

## <a name="summary"></a>Sammanfattning
Friheten att använda Azure API management-cache för att lagra alla slags data möjliggör effektiv åtkomst till konfigurationsdata som kan påverka hur en inkommande begäran bearbetas. Det kan också användas för att lagra datafragment som kan utöka svar som returnerades från en serverdel API.

## <a name="next-steps"></a>Nästa steg
Ge oss din feedback i Disqus-tråden för det här avsnittet om det finns andra scenarier som dessa principer har aktiverats för dig, eller om det finns scenarier du vill uppnå men inte är för närvarande går.

