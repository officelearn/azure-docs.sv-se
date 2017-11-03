---
title: Azure API Management avancerade principer | Microsoft Docs
description: "Läs mer om de avancerade principerna som är tillgängligt för användning i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8a13348b-7856-428f-8e35-9e4273d94323
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: e5a658e0d20d42911870f2522f6c1bab7529ea11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-advanced-policies"></a>API Management avancerade principer
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AdvancedPolicies"></a>Avancerade principer  
  
-   [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) - villkorligt gäller principrapporter baserat på resultatet av utvärderingen av boolesk [uttryck](api-management-policy-expressions.md).  
  
-   [Vidarebefordra begäran](#ForwardRequest) -vidarebefordrar begäran till backend-tjänsten.

-   [Begränsa samtidighet](#LimitConcurrency) -förhindrar omslutna principer från att köras med mer än det angivna antalet begäranden i taget.
  
-   [Loggen till Event Hub](#log-to-eventhub) -skickar meddelanden i det angivna formatet till en Händelsehubb som definieras av en loggaren entitet. 

-   [Mock svar](#mock-response) -avbryts körningen i pipeline och returnerar svaret mocked direkt till anroparen.
  
-   [Försök](#Retry) -återförsök körningen av de slutna principrapporter om och tills villkoret är uppfyllt. Körningen upprepas med de angivna intervall och upp till angivet antal nya försök.  
  
-   [Returnera svar](#ReturnResponse) -avbryts körningen i pipeline och returnerar det angivna svaret direkt till anroparen. 
  
-   [Skicka förfrågan om enkelriktade](#SendOneWayRequest) -skickar en begäran till den angivna URL: en utan att vänta på ett svar.  
  
-   [Skicka förfrågan](#SendRequest) -skickar en begäran till angiven URL.  

-   [Ange HTTP-proxy](#SetHttpProxy) -låter dig vägen vidarebefordrade begäranden via en HTTP-proxy.  

-   [Ange metoden](#SetRequestMethod) -kan du ändra HTTP-metoden för en begäran.  
  
-   [Ange statuskoden](#SetStatus) -ändrar HTTP-statuskoden till det angivna värdet.  
  
-   [Ange variabel](api-management-advanced-policies.md#set-variable) -kvarstår ett värde i en namngiven [kontexten](api-management-policy-expressions.md#ContextVariables) variabel för senare användning.  

-   [Spåra](#Trace) -lägger till en sträng i den [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) utdata.  
  
-   [Vänta](#Wait) -väntar för omslutna [begäran om att skicka](api-management-advanced-policies.md#SendRequest), [hämta värdet från cache](api-management-caching-policies.md#GetFromCacheByKey), eller [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) principer för att slutföra innan du fortsätter.  
  
##  <a name="choose"></a>Kontrollflöde  
 Den `choose` principen gäller omslutna princip uttryck baserat på resultatet av utvärderingen av booleska uttryck som liknar en if-then-else eller växel konstruera i ett programmeringsspråk.  
  
###  <a name="ChoosePolicyStatement"></a>Principframställning  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 Princip för åtkomstkontroll flödet måste innehålla minst ett `<when/>` element. Den `<otherwise/>` element är valfria. Villkoren i `<when/>` element utvärderas i ordning efter deras utseende i principen. Principen instruktion(er) omgiven första `<when/>` element med villkoret attribut är lika med `true` tillämpas. Principer omgiven av `<otherwise/>` element, i förekommande fall, kommer att tillämpas om alla av den `<when/>` elementattribut för villkoret är `false`.  
  
### <a name="examples"></a>Exempel  
  
####  <a name="ChooseExample"></a>Exempel  
 I följande exempel visas en [ange variabel](api-management-advanced-policies.md#set-variable) principen och två principer för åtkomstkontroll flödet.  
  
 Ange variabeln princip finns i avsnittet inkommande och skapar en `isMobile` booleskt [kontexten](api-management-policy-expressions.md#ContextVariables) variabel som har angetts till true om den `User-Agent` begäran huvudet innehåller texten `iPad` eller `iPhone`.  
  
 Den första kontroll flödet principen finns i avsnittet inkommande och villkorligt gäller en av två [ange frågesträngparametern](api-management-transformation-policies.md#SetQueryStringParameter) principer beroende på värdet för den `isMobile` kontexten variabeln.  
  
 Den andra kontrollen flödet i avsnittet utgående och villkorligt gäller den [konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) principen när `isMobile` är inställd på `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>Exempel  
 Det här exemplet illustrerar hur du utför innehållsfiltrering genom att ta bort dataelement från svar togs emot från serverdelstjänsten när du använder den `Starter` produkten. En demonstration av hur du konfigurerar och använder den här principen finns [moln omfattar avsnitt 177: mer API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 34:30. Börja med 31:50 att se en översikt över [mörkt Sky prognos API: N](https://developer.forecast.io/) används för den här demon.  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Välj|Rotelementet.|Ja|  
|När|De villkor du vill använda för den `if` eller `ifelse` delar av den `choose` princip. Om den `choose` princip har flera `when` avsnitt, de utvärderas i turordning. En gång i `condition` av ett när element beräknas till `true`, ingen ytterligare `when` villkor utvärderas.|Ja|  
|Annars|Innehåller princip fragment som ska användas om ingen av de `when` villkor utvärderas till `true`.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|  
|---------------|-----------------|--------------|  
|villkor = ”booleskt uttryck &#124; Booleskt konstant ”|Booleska uttryck eller en konstant som utvärderas när den innehållande `when` Principframställning utvärderas.|Ja|  
  
###  <a name="ChooseUsage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="ForwardRequest"></a>Vidarebefordra begäran  
 Den `forward-request` princip vidarebefordrar inkommande begäran till backend-tjänst som anges i begäran [kontexten](api-management-policy-expressions.md#ContextVariables). URL: en för backend-tjänsten har angetts i API [inställningar](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) och kan ändras med hjälp av den [ange serverdelstjänst](api-management-transformation-policies.md) princip.  
  
> [!NOTE]
>  Tar bort den här grupprincipresultat i begäran inte vidarebefordras till backend-tjänsten och principer i avsnittet utgående utvärderas omedelbart vid slutförande av principer i avsnittet inkommande.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>Exempel  
  
#### <a name="example"></a>Exempel  
 Följande princip för API-nivå vidarebefordras alla begäranden till backend-tjänsten med en timeout på 60 sekunder.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exempel  
 Använder den här åtgärden säkerhetsnivå för den `base` element ska ärva backend-principen från överordnat nivån API-scope.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exempel  
 Den här åtgärden säkerhetsnivå för uttryckligen vidarebefordrar alla begäranden till backend-tjänsten med en tidsgräns på 120 och ärver inte överordnat nivån backend API-princip.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Exempel  
 Den här åtgärden säkerhetsnivå för vidarebefordrar inte begäranden till backend-tjänsten.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|vidarebefordra begäran|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|timeout = ”heltal”|Det går inte att timeoutintervall i sekunder innan anropet till serverdelstjänsten.|Nej|Ingen tidsgräns|  
|Följ omdirigeringar = ”true &#124; FALSE ”|Anger huruvida omdirigeringar från serverdelstjänsten följt av gateway eller returneras till anroparen.|Nej|FALSKT|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** backend  
  
-   **Princip för scope:** alla scope  
  
##  <a name="LimitConcurrency"></a>Gränsen för samtidighet  
 Den `limit-concurrency` princip förhindrar att omslutna principer körning av fler än det angivna antalet förfrågningar vid en given tidpunkt. På överskrider tröskeln läggs nya begäranden till en kö tills maximala kölängden uppnås. När kön uttömning misslyckas nya begäranden omedelbart.
  
###  <a name="LimitConcurrencyStatement"></a>Principframställning  
  
```xml  
<limit-concurrency key="expression" max-count="number" timeout="in seconds" max-queue-length="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>Exempel  
  
####  <a name="ChooseExample"></a>Exempel  
 Exemplet nedan visar hur du begränsar antalet begäranden som vidarebefordras till en serverdel baserat på värdet för en variabel i kontexten.
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3" timeout="60">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|    
|gränsen för samtidighet|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|--------------|  
|key|En sträng. Uttryck tillåts. Anger samtidighet scope. Kan delas av flera principer.|Ja|Saknas|  
|Max antal|Ett heltal. Anger maximalt antal begäranden som tillåts att ange principen.|Ja|Saknas|  
|timeout|Ett heltal. Uttryck tillåts. Anger antalet sekunder som en begäran ska vänta med att ange ett scope innan åtgärden misslyckas med ”429 för många begäranden”|Nej|Infinity|  
|Max Kölängd|Ett heltal. Uttryck tillåts. Anger den maximala längden. Inkommande begäranden försök att ange den här principen kommer att avslutas med ”429 för många begäranden” omedelbart när kön är slut.|Nej|Infinity|  
  
###  <a name="ChooseUsage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  

##  <a name="log-to-eventhub"></a>Loggen till Händelsehubb  
 Den `log-to-eventhub` princip skickar meddelanden i det angivna formatet till en Händelsehubb som definieras av en loggaren entitet. Som namnet antyder används principen för att spara valda begäran eller svar omständighetsinformation för analys online eller offline.  
  
> [!NOTE]
>  Stegvisa instruktioner om hur du konfigurerar en händelsehubb och loggning av händelser, se [så logghändelser API-hantering med Händelsehubbar](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>Exempel  
 Valfri sträng kan användas som värde som ska loggas i Händelsehubbar. I det här exemplet datum och tid, tjänstnamn för distribution, förfrågnings-id, ip-adress och åtgärdsnamn för alla inkommande samtal loggas till händelsehubben loggaren registrerats med den `contoso-logger` id.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|loggen till eventhub|Rotelementet. Värdet för det här elementet är sträng att logga in till din event hub.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|  
|---------------|-----------------|--------------|  
|loggaren-id|Id för loggaren registrerats API Management-tjänsten.|Ja|  
|partitions-id|Anger index för partitionen som meddelanden skickas.|Valfri. Det här attributet kan inte användas om `partition-key` används.|  
|Partitionsnyckeln|Anger det värde som används för tilldelning av partitionen när meddelanden skickas.|Valfri. Det här attributet kan inte användas om `partition-id` används.|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  

##  <a name="mock-response"></a>Fingerad svar  
Den `mock-response`, som namn innebär, används för att mock API: er och åtgärder. Den normala pipelinekörningen avbryts och returnerar ett mocked svar till anroparen. Principen försöker alltid returnera svar för högsta återgivning. Den föredrar svar innehåll exempel, när det finns tillgängligt. Den genererar exempel svar från scheman, när scheman har angetts och exempel finns inte. Om varken exempel eller scheman hittas returneras svar med inget innehåll.
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>Exempel  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|mock-svar|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|--------------|  
|statuskod|Anger Svarets statuskod och används för att välja motsvarande exempel eller schema.|Nej|200|  
|innehållstyp|Anger `Content-Type` svar huvudets värde och används för att välja motsvarande exempel eller schema.|Nej|Ingen|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående, vid fel  
  
-   **Princip för scope:** alla scope

##  <a name="Retry"></a>Försök igen  
 Den `retry` princip kör dess underordnade principer en gång och sedan försöker körningen tills för och försök igen `condition` blir `false` eller försök `count` är slut.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>Exempel  
 I följande exempelbegäran försöks forewarding upp till tio gånger med exponentiell försök algoritm. Eftersom `first-fast-retry` har angetts till false, alla nya försök regleras av algoritmen exponsntial försök igen.  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|retry|Rotelementet. Kan innehålla andra principer som dess underordnade element.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|Villkor|En boolesk literal eller [uttryck](api-management-policy-expressions.md) anger om återförsök ska stoppas (`false`) eller fortsatte (`true`).|Ja|Saknas|  
|Antal|Ett positivt tal som anger det maximala antalet försök att försöka.|Ja|Saknas|  
|interval|Ett positivt tal i sekunder som anger vänta intervall mellan det nya försöket försöker.|Ja|Saknas|  
|Max-intervall|Ett positivt tal i sekunder som anger maximalt vänta mellan nya försök. Den används för att implementera en algoritm exponentiell försök igen.|Nej|Saknas|  
|delta|Ett positivt tal i sekunder som anger att vänta intervall ökning. Används för att implementera linjär och exponentiella retry-algoritmer.|Nej|Saknas|  
|första-fast-återförsök|Om värdet `true` , första nytt försök utförs omedelbart.|Nej|`false`|  
  
> [!NOTE]
>  När bara den `interval` anges **fast** intervall för nya försök utförs.  
>  När bara den `interval` och `delta` har angetts en **linjär** intervall försök algoritmen används, där väntetiden mellan försök beräknas enligt följande formel - `interval + (count - 1)*delta`.  
>  När den `interval`, `max-interval` och `delta` anges, **exponentiell** intervall försök algoritmen används där väntetiden mellan försöken ökar exponentiellt från värdet för `interval` till värdet `max-interval` enligt följande forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Observera att ärvs underordnade principbegränsningar för användning av den här principen.  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="ReturnResponse"></a>Returnera svar  
 Den `return-response` principen avbryter pipelinekörningen och returnerar ett standardvärde eller anpassade svar till anroparen. Standard-svaret är `200 OK` med ingen brödtext. Anpassade svar kan anges via en kontext variabel eller princip-instruktioner. När både tillhandahålls ändras svaret finns i kontexten variabeln av principen instruktionerna innan de returneras till anroparen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|returnera svar|Rotelementet.|Ja|  
|set-huvud|En [set-huvudet](api-management-transformation-policies.md#SetHTTPheader) princip-satsen.|Nej|  
|Ange brödtext|En [set brödtext](api-management-transformation-policies.md#SetBody) princip-satsen.|Nej|  
|Ange status|En [Ange status](api-management-advanced-policies.md#SetStatus) princip-satsen.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|  
|---------------|-----------------|--------------|  
|svaret variabelnamn|Namnet på variabeln kontexten refereras från, till exempel en uppströms [-begäran om att skicka](api-management-advanced-policies.md#SendRequest) principen och som innehåller en `Response` objekt|Valfri.|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="SendOneWayRequest"></a>Skicka förfrågan om ett sätt  
 Den `send-one-way-request` princip angivna begäran skickas till den angivna URL: en utan att vänta på ett svar.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>Exempel  
 Exempel principen visar ett exempel på hur du använder den `send-one-way-request` princip för att skicka ett meddelande till en Slack chatt-rummet om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns [med externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|en-sätt-begäran om att skicka|Rotelementet.|Ja|  
|URL: en|URL för begäran.|Inga om läge = kopian. Annars Ja.|  
|Metoden|HTTP-metod för begäran.|Inga om läge = kopian. Annars Ja.|  
|sidhuvud|Huvudet i begäran. Använda flera huvud-element för flera huvuden för begäran.|Nej|  
|Brödtext|Begärandetexten.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|mode = ”sträng”|Anger om detta är en ny begäran eller en kopia av den aktuella begäranden. I utgående läge, läge = kopiera initieras inte begärandetexten.|Nej|Ny|  
|namn|Anger namnet på rubriken anges.|Ja|Saknas|  
|Det finns åtgärd|Anger vilken åtgärd som ska vidtas när huvudet har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintliga-huvud.<br />-skip - ersätter inte det befintliga huvudvärdet.<br />-Tillägg - lägger till värdet på det befintliga huvudvärdet.<br />-delete - tar bort huvudet i begäran.<br /><br /> Om värdet är `override` ta med flera poster med samma namn resulterar i sidhuvudet har angetts enligt alla poster (som visas flera gånger); endast listade värden anges i resultatet.|Nej|åsidosätt|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="SendRequest"></a>Skicka förfrågan  
 Den `send-request` princip angivna begäran skickas till angiven URL, väntar på längre än ange timeout-värdet.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>Exempel  
 Det här exemplet illustrerar ett sätt att kontrollera en referens token med en server för auktorisering. Mer information om det här exemplet finns [med externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<inbound>  
  <!-- Extract Token from Authorization header parameter -->  
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />  
  
  <!-- Send request to Token Server to validate token (see RFC 7662) -->  
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">  
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>  
    <set-method>POST</set-method>  
    <set-header name="Authorization" exists-action="override">  
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>  
    </set-header>  
    <set-header name="Content-Type" exists-action="override">  
      <value>application/x-www-form-urlencoded</value>  
    </set-header>  
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>  
  </send-request>  
  
  <choose>  
        <!-- Check active property in response -->  
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
            <!-- Return 401 Unauthorized with http-problem payload -->  
            <return-response response-variable-name="existing response variable">  
                <set-status code="401" reason="Unauthorized" />  
                <set-header name="WWW-Authenticate" exists-action="override">  
                    <value>Bearer error="invalid_token"</value>  
                </set-header>  
            </return-response>  
        </when>  
    </choose>  
  <base />  
</inbound>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Skicka begäran|Rotelementet.|Ja|  
|URL: en|URL för begäran.|Inga om läge = kopian. Annars Ja.|  
|Metoden|HTTP-metod för begäran.|Inga om läge = kopian. Annars Ja.|  
|sidhuvud|Huvudet i begäran. Använda flera huvud-element för flera huvuden för begäran.|Nej|  
|Brödtext|Begärandetexten.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|mode = ”sträng”|Anger om detta är en ny begäran eller en kopia av den aktuella begäranden. I utgående läge, läge = kopiera initieras inte begärandetexten.|Nej|Ny|  
|svaret variabelnamn = ”sträng”|Om den inte finns `context.Response` används.|Nej|Saknas|  
|timeout = ”heltal”|Det går inte att timeout-intervall i sekunder innan anropet till URL: en.|Nej|60|  
|Ignorera fel|Om true, och begäran resulterar i ett fel:<br /><br /> – Om svaret variabelnamn angavs innehåller ett null-värde.<br />– Om svaret variabelnamn inte har angetts, kontext. Begäran kommer inte att uppdateras.|Nej|FALSKT|  
|namn|Anger namnet på rubriken anges.|Ja|Saknas|  
|Det finns åtgärd|Anger vilken åtgärd som ska vidtas när huvudet har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintliga-huvud.<br />-skip - ersätter inte det befintliga huvudvärdet.<br />-Tillägg - lägger till värdet på det befintliga huvudvärdet.<br />-delete - tar bort huvudet i begäran.<br /><br /> Om värdet är `override` ta med flera poster med samma namn resulterar i sidhuvudet har angetts enligt alla poster (som visas flera gånger); endast listade värden anges i resultatet.|Nej|åsidosätt|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="SetHttpProxy"></a>Ange HTTP-proxy  
 Den `proxy` principen kan du på väg begäranden vidarebefordras till serverdelar via en HTTP-proxy. Endast HTTP (HTTPS inte) stöds mellan gatewayen och proxyn. Grundläggande och NTLM-autentisering.
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>Exempel  
Observera användningen av [egenskaper](api-management-howto-properties.md) som värden för användarnamn och lösenord för att undvika att lagra känslig information i dokumentets princip.  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Proxy|Rotelementet|Ja|  

### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|URL = ”sträng”|Proxy-URL i form av http://host:port.|Ja|Saknas|  
|UserName = ”sträng”|Användarnamnet som ska användas för autentisering med proxyservern.|Nej|Saknas|  
|lösenord = ”sträng”|Lösenordet som ska användas för autentisering med proxyservern.|Nej|Saknas|  

### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
  
-   **Princip för scope:** alla scope  

##  <a name="SetRequestMethod"></a>Set-metod för begäran  
 Den `set-method` principen kan du ändra metoden HTTP-begäran för en begäran.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>Exempel  
 Det här exemplet princip som använder den `set-method` principen visas ett exempel på ett meddelande skickades till en Slack chatt-rum om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns [med externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|set-metod|Rotelementet. Värdet för elementet anger HTTP-metoden.|Ja|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, vid fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="SetStatus"></a>Ange statuskoden  
 Den `set-status` principen anger HTTP-statuskoden med det angivna värdet.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>Exempel  
 Det här exemplet illustrerar hur du skickar tillbaka ett 401 svar om autentiseringstoken är ogiltig. Mer information finns i [med externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)  
  
```xml  
<choose>  
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
    <return-response response-variable-name="existing response variable">  
      <set-status code="401" reason="Unauthorized" />  
      <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
      </set-header>  
    </return-response>  
  </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Ange status|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|kod = ”heltal”|HTTP-statuskoden ska returneras.|Ja|Saknas|  
|Orsak = ”sträng”|En beskrivning av orsaken för att returnera statuskoden.|Ja|Saknas|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** utgående, backend fel  
  
-   **Princip för scope:** alla scope  

##  <a name="set-variable"></a>Ange variabel  
 Den `set-variable` princip deklarerar en [kontexten](api-management-policy-expressions.md#ContextVariables) variabeln och tilldelar den ett värde som angetts via en [uttryck](api-management-policy-expressions.md) eller en teckensträng. Om uttrycket innehåller ett litteralvärde kommer att konverteras till en sträng och värdet ska vara `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a>Principframställning  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a>Exempel  
 I följande exempel visas en uppsättning variabeln princip i avsnittet inkommande. Ange variabeln principen skapar en `isMobile` booleskt [kontexten](api-management-policy-expressions.md#ContextVariables) variabel som har angetts till true om den `User-Agent` begäran huvudet innehåller texten `iPad` eller `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Ange variabel|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|  
|---------------|-----------------|--------------|  
|namn|Namnet på variabeln.|Ja|  
|värde|Värdet för variabeln. Detta kan vara ett uttryck eller ett litteralvärde.|Ja|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
###  <a name="set-variableAllowedTypes"></a>Tillåtna typer  
 Uttryck som används i den `set-variable` principen måste returnera ett av följande grundläggande typer.  
  
-   System.Boolean  
  
-   System.SByte  
  
-   System.Byte  
  
-   System.UInt16  
  
-   System.UInt32  
  
-   System.UInt64  
  
-   System.Int16  
  
-   System.Int32  
  
-   System.Int64  
  
-   System.Decimal  
  
-   System.Single  
  
-   System.Double  
  
-   System.Guid  
  
-   System.String  
  
-   System.Char  
  
-   System.DateTime  
  
-   System.TimeSpan  
  
-   System.Byte?  
  
-   System.UInt16?  
  
-   System.UInt32?  
  
-   System.UInt64?  
  
-   System.Int16?  
  
-   System.Int32?  
  
-   System.Int64?  
  
-   System.Decimal?  
  
-   System.Single?  
  
-   System.Double?  
  
-   System.Guid?  
  
-   System.String?  
  
-   System.Char?  
  
-   System.DateTime?  

##  <a name="Trace"></a>Spårning  
 Den `trace` princip lägger till en sträng i den [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) utdata. Principen körs endast när spårning utlöses, d.v.s. `Ocp-Apim-Trace` huvud är närvarande och ange att `true` och `Ocp-Apim-Subscription-Key` begärandehuvudet finns och innehåller en giltig nyckel som är associerade med administratörskontot.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Spårning|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|Källa|Stränglitteral meningsfulla för visningsprogram och ange källan för meddelandet.|Ja|Saknas|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **Avsnitt i princip:** inkommande, utgående backend fel  
  
-   **Princip för scope:** alla scope  
  
##  <a name="Wait"></a>Vänta  
 Den `wait` principen Kör sina direkt underordnade principer parallellt och väntar på att alla eller en av dess omedelbara underordnade principer ska slutföras innan den slutförs. Vänta principen kan ha sina direkt underordnade principer [begäran om att skicka](api-management-advanced-policies.md#SendRequest), [hämta värdet från cache](api-management-caching-policies.md#GetFromCacheByKey), och [Åtkomstkontrollflödet](api-management-advanced-policies.md#choose) principer.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>Exempel  
 I följande exempel finns två `choose` principer som direkt underordnade principer för den `wait` princip. Var och en av dessa `choose` principer körs parallellt. Varje `choose` princip försöker hämta ett cachelagrade värde. Om det finns en cache-miss, kallas en backend-tjänst för att ange värdet. I det här exemplet i `wait` principen inte slutföras förrän alla dess omedelbara underordnade principer slutföras eftersom den `for` -attributet är inställt på `all`.   I det här exemplet variablerna kontext (`execute-branch-one`, `value-one`, `execute-branch-two`, och `value-two`) deklareras utanför omfånget för den här principen exempel.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|Vänta|Rotelementet. Kan innehålla som underordnade element endast `send-request`, `cache-lookup-value`, och `choose` principer.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Attribut|Beskrivning|Krävs|Standard|  
|---------------|-----------------|--------------|-------------|  
|för|Anger om den `wait` principen väntar på att alla direkt underordnade principer ska slutföras eller bara en. Tillåtna värden är:<br /><br /> -   `all`-Vänta tills alla direkt underordnade principer ska slutföras<br />-alla - vänta tills alla direkt underordnade principen att slutföra. När den första omedelbara underordnade principen är klar, den `wait` principen har slutförts och körningen av alla andra principer för omedelbart underordnade avslutas.|Nej|Alla|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående backend  
  
-   **Princip för scope:**alla scope  
  
## <a name="next-steps"></a>Nästa steg
Arbeta med principer, Läs mer:
-   [Principer för i API-hantering](api-management-howto-policies.md) 
-   [Principuttryck](api-management-policy-expressions.md)
