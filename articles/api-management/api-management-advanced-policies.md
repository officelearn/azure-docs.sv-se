---
title: Azure API Management avancerade principer | Microsoft Docs
description: Läs mer om de avancerade principerna som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: cc327695a91a39c4d910a4f2421b22cc3b100627
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747889"
---
# <a name="api-management-advanced-policies"></a>API Management avancerade principer
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AdvancedPolicies"></a> Avancerade principer

-   [Kontrollflöde](api-management-advanced-policies.md#choose) – villkorligt gäller principrapporter baserat på resultatet av utvärderingen av boolesk [uttryck](api-management-policy-expressions.md).
-   [Vidarebefordra begäran](#ForwardRequest) -vidarebefordrar begäran till backend-tjänsten.
-   [Begränsa samtidighet](#LimitConcurrency) -förhindrar omges principer från att köras med mer än det angivna antalet begäranden i taget.
-   [Logga till Event Hub](#log-to-eventhub) -skickar meddelanden i formatet som anges till en Händelsehubb som definierats av en Logger-enhet.
-   [Simulera svar](#mock-response) -avbryter pipeline-körning och returnerar ett simulerat svar direkt till anroparen.
-   [Försök](#Retry) -återförsök körningen av de slutna principrapporter om och tills villkoret är uppfyllt. Körningen upprepas med de angivna intervall och upp till den angivna antal nya försök.
-   [Returnera svaret](#ReturnResponse) -avbryter pipeline-körning och returnerar det angivna svaret direkt till anroparen.
-   [Skicka enkelriktade begäran](#SendOneWayRequest) -skickar en begäran till den angivna URL: en utan att vänta tills ett svar.
-   [Skicka begäran](#SendRequest) -skickar en begäran till den angivna URL: en.
-   [Ange HTTP-proxy](#SetHttpProxy) – gör att du kan dirigera vidarebefordrade begäranden via en HTTP-proxy.
-   [Ange begärandemetoden](#SetRequestMethod) – kan du ändra HTTP-metoden för en begäran.
-   [Ange statuskoden](#SetStatus) -ändrar HTTP-statuskoden till det angivna värdet.
-   [Ange variabel](api-management-advanced-policies.md#set-variable) -kvarstår ett värde i en namngiven [kontext](api-management-policy-expressions.md#ContextVariables) variabeln för senare användning.
-   [Spårningen](#Trace) -lägger till en sträng i den [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata.
-   [Vänta](#Wait) -väntar för angiven [sändningsbegäranden](api-management-advanced-policies.md#SendRequest), [hämta värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey), eller [Kontrollflöde](api-management-advanced-policies.md#choose) principer för att slutföra innan du fortsätter.

##  <a name="choose"></a> Kontrollflöde
 Den `choose` principen gäller inom klammerparenteserna princip instruktioner baserat på resultatet av utvärderingen av booleska uttryck, liknande en if-then-else eller en växel konstruera i ett programmeringsspråk.

###  <a name="ChoosePolicyStatement"></a> Principframställning

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

 Control flow principen måste innehålla minst en `<when/>` element. Den `<otherwise/>` element är valfria. Villkor i `<when/>` element utvärderas i ordning av utseendet i principen. Principen utdrag omgiven av först `<when/>` element med villkoret attribut är lika med `true` kommer att tillämpas. Principer omgiven den `<otherwise/>` element, om sådan finns, kommer att tillämpas om alla av de `<when/>` villkorsattribut för elementet är `false`.

### <a name="examples"></a>Exempel

####  <a name="ChooseExample"></a> Exempel
 I följande exempel visas en [ange variabel](api-management-advanced-policies.md#set-variable) princip och två principer för åtkomstkontroll-flöde.

 Ange variabeln princip finns i avsnittet inkommande och skapar en `isMobile` booleskt [kontext](api-management-policy-expressions.md#ContextVariables) variabel som är inställd på SANT om den `User-Agent` begäran huvudet innehåller texten `iPad` eller `iPhone`.

 Den första kontroll flow principen är också i avsnittet inkommande och villkorligt gäller någon av de två [ange parametern för frågesträngen](api-management-transformation-policies.md#SetQueryStringParameter) principer beroende på värdet för den `isMobile` sammanhangsvariabeln.

 Den andra principen för åtkomstkontroll flow finns i avsnittet utgående och villkorligt gäller den [konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) principen när `isMobile` är inställd på `true`.

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
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Exempel
 Det här exemplet visar hur du utför innehållsfiltrering genom att ta bort dataelement från svaret från serverdelstjänsten när du använder den `Starter` produkten. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 34:30. Starta vid 31:50 att se en översikt över [mörk Sky Prognostisera API](https://developer.forecast.io/) används för den här demon.

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
|Välj|Rotelement.|Ja|
|när|De villkor du vill använda för den `if` eller `ifelse` delar av den `choose` principen. Om den `choose` princip har flera `when` avsnitt, utvärderas de sekventiellt. När den `condition` av ett när element utvärderas till `true`, ingen ytterligare `when` villkoren utvärderas.|Ja|
|Annars|Innehåller i princip kodfragment som ska användas om ingen av de `when` villkor som utvärderas till `true`.|Nej|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|
|---------------|-----------------|--------------|
|villkor = ”booleskt uttryck &#124; booleskt konstant”|Booleska uttryck eller konstant som utvärderas när den som innehåller `when` Principframställning utvärderas.|Ja|

###  <a name="ChooseUsage"></a> Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="ForwardRequest"></a> Vidarebefordra begäran
 Den `forward-request` principen dirigerar den inkommande begäran till backend-tjänsten som anges i begäran [kontext](api-management-policy-expressions.md#ContextVariables). URL: en för backend-tjänsten har angetts i API: et [inställningar](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) och kan ändras med hjälp av den [ange serverdelstjänst](api-management-transformation-policies.md) princip.

> [!NOTE]
>  Ta bort den här gruppolicy-resultat i begäran inte vidarebefordras till serverdelen service och principerna i avsnittet utgående utvärderas omedelbart vid slutförande av principer i avsnittet inkommande.

### <a name="policy-statement"></a>Principframställning

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false"/>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel
 Följande princip för API-nivå vidarebefordrar alla begäranden till backend-tjänsten med ett timeout-intervall på 60 sekunder.

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
 Den här åtgärden på principen används den `base` element att ärva backend-principen från överordnat nivån omfång för API.

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
 Den här åtgärden säkerhetsnivå för specifikt vidarebefordrar alla begäranden till backend-tjänsten med en tidsgräns på 120 och ärver inte överordnat API-princip på valvnivå.

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
|vidarebefordra begäran|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|timeout = ”heltal”|Det går inte att timeout-intervall i sekunder innan anropet till serverdelstjänsten.|Nej|300 sekunder|
|Följ omdirigeringar = ”true &#124; FALSKT”|Anger om omdirigeringar från backend-tjänsten är följt av gatewayen eller returneras till anroparen.|Nej|false|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** serverdel
-   **Princip-scope:** alla omfattningar

##  <a name="LimitConcurrency"></a> Gränsen för samtidighet
 Den `limit-concurrency` princip som förhindrar att inom klammerparenteserna principer körning med mer än det angivna antalet förfrågningar vid en given tidpunkt. Vid som överstiger det numret, misslyckas nya begäranden omedelbart med 429 för många begäranden-statuskoden.

###  <a name="LimitConcurrencyStatement"></a> Principframställning

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel
 I följande exempel visar hur du begränsar antalet begäranden som vidarebefordras till en serverdel baserat på värdet för en sammanhangsvariabel.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Element

|Element|Beskrivning|Krävs|
|-------------|-----------------|--------------|
|gränsen för samtidighet|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|--------------|
|key|En sträng. Uttryck tillåts. Anger vilka samtidighet. Kan delas av flera principer.|Ja|Gäller inte|
|Maximalt antal|Ett heltal. Anger ett maximalt antal begäranden som tillåts att ange principen.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="log-to-eventhub"></a> Logga till Event Hub
 Den `log-to-eventhub` princip skickar meddelanden till en Händelsehubb som definierats av en Logger-enhet i angivet format. Som namnet antyder används principen för att spara valda begäran eller ett svar kontextinformation för analys online eller offline.

> [!NOTE]
>  Stegvisa instruktioner om hur du konfigurerar en händelsehubb och loggning av händelser, se [så logghändelser API Management med Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Principframställning

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exempel
 Valfri sträng kan användas som värde som ska loggas i Event Hubs. I det här exemplet datum och tid, tjänstnamn för distribution, begärande-id, ip-adress och åtgärdsnamn för alla inkommande samtal loggas i event hub loggaren som registrerats med den `contoso-logger` id.

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
|loggen till eventhub|Rotelement. Värdet för det här elementet är en sträng att logga in till din event hub.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|
|---------------|-----------------|--------------|
|loggaren-id|Id för loggaren registrerad med API Management-tjänsten.|Ja|
|partitions-id|Anger index för partitionen där meddelanden skickas.|Valfri. Det här attributet kan inte användas om `partition-key` används.|
|partitionsnyckel|Anger det värde som används för partitionstilldelningen när meddelanden skickas.|Valfri. Det här attributet kan inte användas om `partition-id` används.|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="mock-response"></a> Simulera svar
Den `mock-response`, som namnet antyder, används för att testa API: er och åtgärder. Den avbryter normal pipeline-åtgärd och returnerar ett simulerat svar till anroparen. Principen försöker alltid returnera svar av högsta kvalitet. Prioriteras svar innehåll exempel, när det finns tillgängligt. Den genererar exempel svar från scheman, när scheman har angetts och exempel finns inte. Om varken exempel eller scheman hittas returneras responser inget innehåll.

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
|utkast-svar|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|--------------|
|statuskod|Anger svarsstatuskod och används för att välja motsvarande exempel eller schema.|Nej|200|
|innehållstyp|Anger `Content-Type` svar huvudets värde och används för att välja motsvarande exempel eller schema.|Nej|Ingen|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="Retry"></a> Försök igen
 Den `retry` principen utförs en gång dess underordnade principer och försöker sedan sina köra förrän återförsök `condition` blir `false` eller försök `count` är slut.

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
 I följande exempel görs begäran vidarebefordran upp till tio gånger med en algoritm för exponentiellt återförsök. Eftersom `first-fast-retry` har angetts till false, alla omförsök omfattas av algoritmen exponentiellt återförsök.

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
|retry|Rotelement. Kan innehålla andra principer som dess underordnade element.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|villkor|En boolesk literal eller [uttryck](api-management-policy-expressions.md) att ange om återförsök ska stoppas (`false`) eller fortsatt (`true`).|Ja|Gäller inte|
|count|Ett positivt tal som anger det maximala antalet nya försök att försöka.|Ja|Gäller inte|
|interval|Det görs försök att ett positivt tal på några sekunder att ange väntetidsintervallet mellan återförsök.|Ja|Gäller inte|
|maximalt intervall|Ett positivt tal på några sekunder att ange maximalt vänta intervall mellan nya försök. Den används för att implementera en algoritm för exponentiellt återförsök.|Nej|Gäller inte|
|delta|Ett positivt tal på några sekunder att ange den vänta intervall ökningen. Den används för att implementera återförsök för linjär och exponentiell algoritmer.|Nej|Gäller inte|
|första-snabbt-försök|Om inställd `true` , första återförsök sker det omedelbart.|Nej|`false`|

> [!NOTE]
>  När bara den `interval` anges **fast** intervall för återförsök utförs.
> När bara den `interval` och `delta` anges, en **linjär** intervall för återförsök algoritmen används, där väntetiden mellan försöken beräknas enligt följande formel - `interval + (count - 1)*delta`.
> När den `interval`, `max-interval` och `delta` anges, **exponentiell** intervall för återförsök algoritmen används, där väntetiden mellan återförsöken ökar exponentiellt från värdet för `interval` till värdet `max-interval` enligt följande forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Observera att användning av underordnade principbegränsningar ärvs av den här principen.

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="ReturnResponse"></a> Returnera svar
 Den `return-response` principen avbryter pipeline-åtgärd och returnerar en standard- eller anpassade svar till anroparen. Standardsvar är `200 OK` med ingen brödtext. Du kan ange anpassade svar via en kontext variabeln eller princip-uttryck. När båda tillhandahålls ändras svaret finns i sammanhangsvariabeln av principrapporter innan det returneras till anroparen.

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
|returnera svar|Rotelement.|Ja|
|set-header|En [angivet sidhuvud](api-management-transformation-policies.md#SetHTTPheader) Principframställning.|Nej|
|Ställ in brödtext|En [Ställ in brödtext](api-management-transformation-policies.md#SetBody) Principframställning.|Nej|
|set-status|En [set-status](api-management-advanced-policies.md#SetStatus) Principframställning.|Nej|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|
|---------------|-----------------|--------------|
|svaret variabelnamn|Namnet på sammanhangsvariabeln refereras från, till exempel en uppströms [-begäran om att skicka](api-management-advanced-policies.md#SendRequest) principen och som innehåller en `Response` objekt|Valfri.|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="SendOneWayRequest"></a> Skickas enkelriktat
 Den `send-one-way-request` princip skickar angivna begäran till den angivna URL: en utan att vänta tills ett svar.

### <a name="policy-statement"></a>Principframställning

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Exempel
 Princip för det här exemplet visar ett exempel på hur du använder den `send-one-way-request` princip för att skicka ett meddelande till en Slack chattrum om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
|en-sätt – begäran om att skicka|Rotelement.|Ja|
|url|URL för begäran.|Inga om läge = kopia. Annars Ja.|
|metod|HTTP-metoden för begäran.|Inga om läge = kopia. Annars Ja.|
|sidhuvud|Huvudet i begäran. Använda flera huvudelement för flera begärandehuvuden.|Nej|
|brödtext|Begärantexten.|Nej|
|certifikat för serverautentisering|[Certifikat för klientautentisering](api-management-authentication-policies.md#ClientCertificate)|Nej|


### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|mode="string"|Anger om detta är en ny begäran eller en kopia av den aktuella begäran. I läget för utgående, läge = Kopiera inte initiera begärandetexten.|Nej|Ny|
|namn|Anger namnet på rubriken anges.|Ja|Gäller inte|
|Det finns åtgärder|Anger vilken åtgärd som ska vidtas när rubriken har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintlig rubrik.<br />-skip - ersätter inte befintliga huvudets värde.<br />-Tillägg - lägger till värdet till det befintliga värdet för sidhuvudet.<br />-delete - tar bort huvudet i begäran.<br /><br /> När värdet `override` ta med flera poster med samma namn resulterar i rubriken anges enligt alla poster (som visas flera gånger); endast listade värdena anges i resultatet.|Nej|åsidosättning|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="SendRequest"></a> Skicka begäran
 Den `send-request` princip skickar angivna begäran till angiven URL, att vänta längre än set timeout-värdet.

### <a name="policy-statement"></a>Principframställning

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Exempel
 Det här exemplet visar ett sätt att kontrollera en referens token med en auktoriseringsservern. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
            <return-response>
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
|Skicka begäran|Rotelement.|Ja|
|url|URL för begäran.|Inga om läge = kopia. Annars Ja.|
|metod|HTTP-metoden för begäran.|Inga om läge = kopia. Annars Ja.|
|sidhuvud|Huvudet i begäran. Använda flera huvudelement för flera begärandehuvuden.|Nej|
|brödtext|Begärantexten.|Nej|
|certifikat för serverautentisering|[Certifikat för klientautentisering](api-management-authentication-policies.md#ClientCertificate)|Nej|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|mode="string"|Anger om detta är en ny begäran eller en kopia av den aktuella begäran. I läget för utgående, läge = Kopiera inte initiera begärandetexten.|Nej|Ny|
|response-variable-name="string"|Namnet på sammanhangsvariabeln som tar emot ett svarsobjekt. Om variabeln inte finns det skapas efter lyckade körningen av principen och blir tillgänglig via [ `context.Variable` ](api-management-policy-expressions.md#ContextVariables) samling.|Ja|Gäller inte|
|timeout = ”heltal”|Det går inte att timeout-intervall i sekunder innan anropet till URL: en.|Nej|60|
|Ignorera fel|Om SANT och begäran resulterar i ett fel:<br /><br /> – Om svar-variabeln-name har angetts innehåller ett null-värde.<br />– Om variabeln svarsnamnet inte har angetts, kontext. Begäran kommer inte att uppdateras.|Nej|false|
|namn|Anger namnet på rubriken anges.|Ja|Gäller inte|
|Det finns åtgärder|Anger vilken åtgärd som ska vidtas när rubriken har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintlig rubrik.<br />-skip - ersätter inte befintliga huvudets värde.<br />-Tillägg - lägger till värdet till det befintliga värdet för sidhuvudet.<br />-delete - tar bort huvudet i begäran.<br /><br /> När värdet `override` ta med flera poster med samma namn resulterar i rubriken anges enligt alla poster (som visas flera gånger); endast listade värdena anges i resultatet.|Nej|åsidosättning|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="SetHttpProxy"></a> Ange HTTP-proxy
 Den `proxy` princip gör att du kan dirigera begäranden som vidarebefordras till serverdelar via en HTTP-proxy. Endast HTTP (inte HTTPS) stöds mellan gatewayen och proxy. Basic- och NTLM-autentisering.

### <a name="policy-statement"></a>Principframställning

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exempel
Observera användningen av [egenskaper](api-management-howto-properties.md) som värden för användarnamn och lösenord för att undvika att lagra känslig information i dokumentet.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Element

|Element|Beskrivning|Krävs|
|-------------|-----------------|--------------|
|Proxy|Rotelement|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|URL: en = ”string”|Proxy-URL i form av http://host:port.|Ja|Gäller inte|
|användarnamn = ”string”|Användarnamnet som ska användas för autentisering med proxyn.|Nej|Gäller inte|
|lösenord = ”string”|Lösenordet som ska användas för autentisering med proxyn.|Nej|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande

-   **Princip-scope:** alla omfattningar

##  <a name="SetRequestMethod"></a> Set-metod för begäran
 Den `set-method` princip kan du ändra metoden för HTTP-begäran för en begäran.

### <a name="policy-statement"></a>Principframställning

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exempel
 Det här exemplet-princip som använder den `set-method` principen visar ett exempel på Skicka ett meddelande till en Slack chattrum om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
|set-metod|Rotelement. Värdet för elementet anger HTTP-metoden.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande trafik och vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="SetStatus"></a> Set-statuskod
 Den `set-status` principen anger HTTP-statuskoden med det angivna värdet.

### <a name="policy-statement"></a>Principframställning

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exempel
 Det här exemplet visar hur du skickar tillbaka ett 401-svar om autentiseringstoken är ogiltig. Mer information finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
|set-status|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|code="integer"|HTTP-statuskoden ska returneras.|Ja|Gäller inte|
|Orsak = ”string”|En beskrivning av orsaken för att returnera statuskod.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** utgående, serverdel, vid fel
-   **Princip-scope:** alla omfattningar

##  <a name="set-variable"></a> Ange variabel
 Den `set-variable` princip deklarerar en [kontext](api-management-policy-expressions.md#ContextVariables) variabeln och tilldelar den ett värde som angetts via en [uttryck](api-management-policy-expressions.md) eller en teckensträng. Om uttrycket innehåller ett litteralvärde kommer att konverteras till en sträng och typ av värde blir `System.String`.

###  <a name="set-variablePolicyStatement"></a> Principframställning

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

###  <a name="set-variableExample"></a> Exempel
 I följande exempel visar en uppsättning variabeln princip under inkommande. Den här variabeln set-principen skapas en `isMobile` booleskt [kontext](api-management-policy-expressions.md#ContextVariables) variabel som är inställd på SANT om den `User-Agent` begäran huvudet innehåller texten `iPad` eller `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Element

|Element|Beskrivning|Krävs|
|-------------|-----------------|--------------|
|Ange variabel|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|
|---------------|-----------------|--------------|
|namn|Namnet på variabeln.|Ja|
|värde|Värdet på variabeln. Detta kan vara ett uttryck eller ett literalvärde.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel
-   **Princip-scope:** alla omfattningar

###  <a name="set-variableAllowedTypes"></a> Tillåtna typer
 Uttryck som används i den `set-variable` princip måste returnera följande grundläggande typer.

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

##  <a name="Trace"></a> Spårning
 Den `trace` principen lägger till en sträng i den [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata. Principen körs endast när spårning utlöses, d.v.s. `Ocp-Apim-Trace` huvudet i begäran är närvarande och har inställningen till `true` och `Ocp-Apim-Subscription-Key` begärandehuvudet finns och innehåller en giltig nyckel som är associerade med administratörskontot.

### <a name="policy-statement"></a>Principframställning

```xml

<trace source="arbitrary string literal"/>
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Element

|Element|Beskrivning|Krävs|
|-------------|-----------------|--------------|
|Spårning|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|källa|Exakt sträng är meningsfulla för visningsprogram och ange källan för meddelandet.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** alla omfattningar

##  <a name="Wait"></a> Vänta
 Den `wait` principen Kör sina direkt underordnade principer parallellt och väntar tills alla eller en av dess omedelbara underordnade principer ska slutföras innan den slutförs. Vänta principen kan ha som dess omedelbara underordnade principer [sändningsbegäranden](api-management-advanced-policies.md#SendRequest), [hämta värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey), och [Kontrollflöde](api-management-advanced-policies.md#choose) principer.

### <a name="policy-statement"></a>Principframställning

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exempel
 I följande exempel finns två `choose` principer som direkt underordnade principer från den `wait` principen. Var och en av dessa `choose` principer körs parallellt. Varje `choose` princip försöker hämta ett cachelagrade värde. Om det finns en cachemiss, anropas en serverdelstjänst för att ange värdet. I det här exemplet på `wait` principen inte slutföras förrän alla dess underordnade omedelbar principer slutföra eftersom den `for` är attributet `all`.   I det här exemplet sammanhangsvariablerna (`execute-branch-one`, `value-one`, `execute-branch-two`, och `value-two`) har deklarerats utanför omfånget för den här exempel-principen.

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
|Vänta|Rotelement. Kan innehålla som underordnade element endast `send-request`, `cache-lookup-value`, och `choose` principer.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|för|Avgör om den `wait` principen väntar tills alla direkt underordnade principer som slutförda eller bara en. Tillåtna värden är:<br /><br /> -   `all` -Vänta tills alla direkt underordnade principer att slutföra<br />-alla - vänta tills alla direkt underordnade principen att slutföra. När den första principen direkt underordnade har slutförts, den `wait` principen har slutförts och körning av alla andra principer för omedelbar underordnade avslutas.|Nej|all|

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdel
-   **Princip-scope:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:
+ [Principer i API Management](api-management-howto-policies.md)
+ [Principuttryck](api-management-policy-expressions.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)
