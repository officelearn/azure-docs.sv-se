---
title: Anpassad cachelagring i Azure API Management
description: Lär dig hur du cachelagrar objekt med nycklar i Azure API Management
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799192"
---
# <a name="custom-caching-in-azure-api-management"></a>Anpassad cachelagring i Azure API Management
Azure API Management-tjänsten har inbyggt stöd för [cachelagring av HTTP-svar](api-management-howto-cache.md) med resurs-URL som nyckel. Nyckeln kan ändras av begärandehuvuden med hjälp av den `vary-by` egenskaper. Detta är användbart för att cachelagra hela HTTP-svar (även kallat representationer), men ibland är det praktiskt att cachen bara en del av en representation. Den nya [cache-sökning-value](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) och [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) -principerna förser dig möjligheten att lagra och hämta godtyckliga delar av data inifrån principdefinitioner. Den här möjligheten lägger också till värde till tidigare introducerats [-begäran om att skicka](/azure/api-management/api-management-advanced-policies#SendRequest) principen eftersom du kan nu cachelagra svar från externa tjänster.

## <a name="architecture"></a>Arkitektur
API Management-tjänsten använder en delad per klient data lagrar så att skala upp till flera enheter du fortfarande komma åt samma cachelagrade data. När du arbetar med en distribution i flera regioner är det dock oberoende cacheminnen i var och en av regionerna. Det är viktigt att inte behandla cacheminnet som ett datalager, där det är den enda källan för vissa av information. Om du gjorde och senare bestämde sig för att dra nytta av distributionen i flera regioner, kan sedan kunder med användare som reser förlora åtkomsten till dessa cachelagrade data.

## <a name="fragment-caching"></a>Cachelagring av fragment
Det finns vissa fall där svar som returneras innehåller en del av data som är dyra att fastställa och ännu är den senaste för en rimlig tid. Exempelvis bör du en tjänst som skapats av flygbolag som tillhandahåller information som rör flygning reservationer, flygning status osv. Om användaren är medlem i programmet airlines punkter, de skulle också ha information om deras aktuella status och ackumulerade hittills utfört. Den här användarrelaterad information lagras i ett annat system, men det kan vara önskvärt att inkludera den i svaren som returneras om flygning status och -reservationer. Detta kan göras med hjälp av en process som kallas cachelagring av fragment. Den primära representationen kan returneras från den ursprungliga servern med hjälp av någon typ av token för att ange där användarrelaterad information som ska infogas. 

Överväg följande JSON-svar från ett serverdels-API.

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

Och sekundära resursen på `/userprofile/{userid}` som ser ut som,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Måste identifiera som användaren är för att fastställa den aktuella användarinformationen som ska ingå, API Management. Den här mekanismen är beroende av implementering. Till exempel jag använder den `Subject` anspråk för en `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management-butiker i `enduserid` värdet i en variabel i kontexten för senare användning. Nästa steg är att avgöra om en tidigare begäran redan hämtats användarinformationen och lagras i cacheminnet. För detta API Management använder den `cache-lookup-value` principen.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Om det finns ingen post i cacheminnet som motsvarar nyckelvärdet och Nej `userprofile` sammanhangsvariabel skapas. API Management kontrollerar kan användas i en sökning med hjälp av den `choose` styra flödet principen.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Om den `userprofile` sammanhangsvariabel finns inte och sedan API Management kommer att göra en HTTP-begäran att hämta den.

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

API Management används den `enduserid` att konstruera URL-Adressen till användarresurs för profilen. När API Management har svaret, hämtar brödtext från svaret och lagrar den tillbaka till en sammanhangsvariabel.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Du kan ange om du vill lagra användarens profil i cacheminnet för att undvika API Management gör den här HTTP-begäran igen, när samma användare skickar en annan begäran.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management lagrar värdet i cachen med exakt samma nyckel som API Management som ursprungligen försökte hämta den med. Så länge som API Management väljer att lagra värdet för ska baseras på hur ofta informationen ändras och hur feltoleranta användare är till inaktuell information. 

Det är viktigt att du upptäcker att hämtas från cachen är fortfarande en out-of-process nätverksbegäran och potentiellt kan fortfarande lägga till tiotals millisekunder på begäran. Fördelarna kommer när du fastställer informationen om användarprofiler tar längre tid än så på grund av att behöva databasen frågor eller samlar in information från flera serverdelar.

Det sista steget i processen är att uppdatera returnerade svaret med information om användarprofiler.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Du kan välja att inkludera citattecken som en del av token så att även om Ersätt inte inträffar det fortfarande är en giltig JSON.  

När du kombinerar de här stegen tillsammans kan är slutresultatet en princip som ser ut som följande.

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

Den här cachelagring används främst på webbplatser där HTML består på serversidan så att den kan återges som en enda sida. Det kan också vara användbart i API: er där klienter inte kan göra HTTP klientcachelagring eller är det klokt inte att placera det ansvaret på klienten.

Det här samma typ av cachelagring av fragment kan också göras på backend-webbservrar med hjälp av en Redis cachelagring server, men använder API Management-tjänsten för att utföra arbetet är användbart när cachelagrade fragment kommer från olika serverdelar än primärt svar.

## <a name="transparent-versioning"></a>Transparent versionshantering
Det är vanligt att flera olika implementering versioner av ett API för stöd åt gången. Till exempel att stödja olika miljöer (utveckling, test, produktion, osv.) eller för att stödja äldre versioner av API: et för ange tid för API-kunderna att migrera till nyare versioner. 

En metod för att hantera detta, i stället för att klienten utvecklare kan ändra URL: er från `/v1/customers` till `/v2/customers` är att spara i profilen konsumentdata vilken version av API: et de för närvarande vill använda och anropa lämplig backend-URL. Det är nödvändigt att fråga vissa konfigurationsdata för att fastställa rätt backend-URL kan ringa för en viss klient. API Management kan minimera prestandaförsämringen gör den här sökningen genom att cachelagra dessa konfigurationsdata.

Det första steget är att avgöra den identifierare som används för att konfigurera den önskade versionen. I det här exemplet jag har valt att associera versionen till produktnyckel för prenumerationen. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management och utför en cache-sökning för att se om det redan hämtats önskade klientversionen.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

API Management kontrollerar sedan om du vill se om det inte gick att hitta det i cacheminnet.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Om API Management inte gick att hitta den, hämtar den API Management.

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

Extrahera brödtext för svaret från svaret.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Store det i cacheminnet för framtida användning.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Och slutligen uppdatera backend-URL: en för att välja versionen av tjänsten som önskas genom klienten.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Hela policyn är följande:

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

Att aktivera API-kunderna att transparent styra vilken backend-version som används av klienter utan att behöva uppdatera och distribuera om klienterna är en smidig lösning som åtgärdar problem med många API-versionshantering.

## <a name="tenant-isolation"></a>Isolering av innehavare
I distributioner av större och flera innehavare skapa vissa företag separata grupper av klienter på olika distributioner av backend-maskinvara. Detta minskar antalet kunder som påverkas av maskinvaruproblem på serverdelen. Dessutom kan nya programvaruversioner att lanseras i steg. Vi är den här backend-arkitekturen transparent för API-kunderna. Detta kan ske på liknande sätt som transparent versionshantering eftersom den är baserad på samma teknik manipulera backend-URL med hjälp av konfigurationsstatus per API-nyckel.  

Istället för att returnera en önskad version av API: et för varje prenumerationsnyckel, returneras en identifierare som relaterar en klient till den tilldelade maskinvarugrupp. Den identifieraren kan användas för att konstruera lämplig backend-URL.

## <a name="summary"></a>Sammanfattning
Friheten att använda Azure API management cache för att lagra alla slags data möjliggör effektiv åtkomst till konfigurationsdata som kan påverka hur en inkommande begäran har bearbetats. Det kan också användas för att lagra datafragment som kan utöka svaren som returneras från ett serverdels-API.
