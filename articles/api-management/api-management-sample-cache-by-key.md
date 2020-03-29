---
title: Custom caching in Azure API Management (Anpassad cachelagring i Azure API Management)
description: Lär dig hur du cachelagrar objekt efter nyckel i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780829"
---
# <a name="custom-caching-in-azure-api-management"></a>Custom caching in Azure API Management (Anpassad cachelagring i Azure API Management)
Azure API Management-tjänsten har inbyggt stöd för [HTTP-svarscachelagring](api-management-howto-cache.md) med resurs-URL:en som nyckel. Nyckeln kan ändras med begäranden `vary-by` med hjälp av egenskaperna. Detta är användbart för cachelagring av hela HTTP-svar (aka representationer), men ibland är det användbart att bara cachelagra en del av en representation. De nya principerna [för cache-uppslagsvärde](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) och [cache-store-värde](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) ger möjlighet att lagra och hämta godtyckliga data från principdefinitioner. Den här möjligheten ger också mervärde till den tidigare introducerade [principen för skicka begäran](/azure/api-management/api-management-advanced-policies#SendRequest) eftersom du nu kan cachelagra svar från externa tjänster.

## <a name="architecture"></a>Arkitektur
API Management-tjänsten använder en delad datacache per klient så att du, när du skalar upp till flera enheter, fortfarande får åtkomst till samma cachelagrade data. Men när du arbetar med en distribution med flera regioner finns det oberoende cacheminnen inom var och en av regionerna. Det är viktigt att inte behandla cachen som ett datalager, där det är den enda källan till viss information. Om du gjorde det och senare bestämde dig för att dra nytta av distributionen med flera regioner kan kunder med användare som reser förlora åtkomsten till de cachelagrade data.

## <a name="fragment-caching"></a>Cachelagring av fragment
Det finns vissa fall där svar som returneras innehåller en del av data som är dyrt att avgöra och ändå är färskt under en rimlig tid. Som ett exempel, överväga en tjänst som byggts av ett flygbolag som tillhandahåller information om flygbokningar, flygstatus, etc. Om användaren är medlem i flygbolagen poäng programmet, skulle de också ha information om deras nuvarande status och ackumulerade körsträcka. Den här användarrelaterade informationen kan lagras i ett annat system, men det kan vara önskvärt att inkludera den i svar som returneras om flygstatus och reservationer. Detta kan göras med hjälp av en process som kallas fragmentcacheing. Den primära representationen kan returneras från ursprungsservern med någon form av token för att ange var den användarrelaterade informationen ska infogas. 

Tänk på följande JSON-svar från ett serverd-API.

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

Och sekundära `/userprofile/{userid}` resurs på som ser ut,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Api Management måste identifiera vem slutanvändaren är för att fastställa lämplig användarinformation som ska inkluderas. Denna mekanism är implementeringsberoende. Som ett exempel använder `Subject` jag anspråk `JWT` på en token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management `enduserid` lagrar värdet i en kontextvariabel för senare användning. Nästa steg är att avgöra om en tidigare begäran redan har hämtat användarinformationen och lagrat den i cacheminnet. För detta använder API `cache-lookup-value` Management principen.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Om det inte finns någon post i cacheminnet `userprofile` som motsvarar nyckelvärdet skapas ingen kontextvariabel. API Management kontrollerar lyckades med `choose` hjälp av kontrollflödesprincipen.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Om `userprofile` kontextvariabeln inte finns måste API Management göra en HTTP-begäran för att hämta den.

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

API Management `enduserid` använder url:en till användarprofilresursen. När API Management har svaret, drar den brödtexten ur svaret och lagrar den tillbaka till en kontextvariabel.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Om du vill undvika att API-hantering görs igen kan du ange att användarprofilen ska lagras i cachen när samma användare gör en annan begäran.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management lagrar värdet i cacheminnet med exakt samma nyckel som API Management ursprungligen försökte hämta det med. Hur länge API Management väljer att lagra värdet bör baseras på hur ofta informationen ändras och hur toleranta användare är för inaktuella information. 

Det är viktigt att inse att hämtning från cachen fortfarande är en out-of-process, nätverksbegäran och potentiellt fortfarande kan lägga till tiotals millisekunder till begäran. Fördelarna kommer när det tar längre tid att bestämma användarprofilinformationen på grund av att behöva göra databasfrågor eller aggregera information från flera back-ends.

Det sista steget i processen är att uppdatera det returnerade svaret med användarprofilinformationen.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Du kan välja att inkludera citattecknen som en del av token så att även när ersättningen inte sker, är svaret fortfarande ett giltigt JSON.  

När du kombinerar alla dessa steg tillsammans, är slutresultatet en princip som ser ut som följande.

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

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
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

Den här cachelagringsmetoden används främst på webbplatser där HTML är sammansatt på serversidan så att den kan återges som en enda sida. Det kan också vara användbart i API:er där klienter inte kan göra HTTP-cachelagring på klientsidan eller om det är önskvärt att inte lägga det ansvaret på klienten.

Samma typ av fragmentcachening kan också göras på backend-webbservrar med hjälp av en Redis-cachelagringsserver, men att använda API Management-tjänsten för att utföra detta arbete är användbart när de cachelagrade fragmenten kommer från olika back-ends än den primära Svaren.

## <a name="transparent-versioning"></a>Transparent versionshantering
Det är vanligt att flera olika implementeringsversioner av ett API stöds samtidigt. Om du till exempel vill stödja olika miljöer (utveckling, test, produktion osv.) eller för att stödja äldre versioner av API:et för att ge tid för API-konsumenter att migrera till nyare versioner. 

En metod för att hantera detta, i stället `/v1/customers` för `/v2/customers` att kräva att klientutvecklare att ändra webbadresserna från till är att lagra i konsumentens profildata vilken version av API:et de för närvarande vill använda och anropa lämplig servergrupps-URL. För att fastställa rätt serverdels-URL för att anropa en viss klient, är det nödvändigt att fråga vissa konfigurationsdata. Genom att cachelagra dessa konfigurationsdata kan API Management minimera prestandastraffet för att göra den här sökningen.

Det första steget är att fastställa identifieraren som används för att konfigurera önskad version. I det här exemplet valde jag att associera versionen till produktprenumerationsnyckeln. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management gör sedan en cache sökning för att se om den redan hämtat önskad klientversion.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Sedan kontrollerar API Management för att se om den inte hittade den i cacheminnet.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Om API Management inte hittade den hämtar API Management den.

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

Extrahera svarstexten från svaret.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Lagra den i cacheminnet igen för framtida bruk.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Och slutligen uppdatera backend URL för att välja den version av tjänsten som önskas av klienten.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Den fullständiga policyn är följande:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
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

Att göra det möjligt för API-konsumenter att öppet kontrollera vilken serverdelsversion som används av klienter utan att behöva uppdatera och distribuera om klienter är en elegant lösning som åtgärdar många API-versionshanteringsproblem.

## <a name="tenant-isolation"></a>Isolering av klienter
I större, multi-tenant distributioner vissa företag skapar separata grupper av klienter på olika distributioner av backend hårdvara. Detta minimerar antalet kunder som påverkas av ett maskinvaruproblem på backend. Det gör det också möjligt att lansera nya programvaruversioner i etapper. Helst bör den här serverdarkitekturen vara transparent för API-konsumenter. Detta kan uppnås på ett liknande sätt som transparent versionshantering eftersom det är baserat på samma teknik för att manipulera serverd-URL:en med konfigurationstillstånd per API-nyckel.  

I stället för att returnera en önskad version av API:et för varje prenumerationsnyckel returnerar du en identifierare som relaterar en klient till den tilldelade maskinvarugruppen. Den identifieraren kan användas för att skapa lämplig url för bakåtspårning.

## <a name="summary"></a>Sammanfattning
Friheten att använda Azure API-hanteringscachen för lagring av alla typer av data möjliggör effektiv åtkomst till konfigurationsdata som kan påverka hur en inkommande begäran bearbetas. Den kan också användas för att lagra datafragment som kan öka svaren, returneras från ett serverd-API.
