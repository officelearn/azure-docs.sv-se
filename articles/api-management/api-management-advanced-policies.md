---
title: Avancerade principer för Azure API Management | Microsoft-dokument
description: Lär dig mer om de avancerade principer som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266174"
---
# <a name="api-management-advanced-policies"></a>Avancerade principer i API Management

Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a>Avancerade policyer

-   [Kontrollflöde](api-management-advanced-policies.md#choose) - Villkorligt tillämpar principsatser baserat på resultaten av utvärderingen av booleska [uttryck](api-management-policy-expressions.md).
-   [Vidarebefordran -](#ForwardRequest) Vidarebefordrar begäran till backend-tjänsten.
-   [Begränsa samtidighet](#LimitConcurrency) - Förhindrar att slutna principer körs med mer än det angivna antalet begäranden åt gången.
-   [Logga till händelsehubb](#log-to-eventhub) - Skickar meddelanden i angivet format till en händelsehubb som definieras av en Logger-entitet.
-   [Håna svar](#mock-response) - Avbryter pipeline-körningen och returnerar ett hånat svar direkt till anroparen.
-   [Försök igen](#Retry) - Återförsökskörning av de bifogade principsatserna, om och tills villkoret är uppfyllt. Körningen upprepas med de angivna tidsintervallen och upp till det angivna antalet försök.
-   [Retursvar](#ReturnResponse) - Avbryter pipelinekörningen och returnerar det angivna svaret direkt till anroparen.
-   [Skicka en way-begäran](#SendOneWayRequest) - Skickar en begäran till den angivna webbadressen utan att vänta på ett svar.
-   [Skicka begäran](#SendRequest) - Skickar en begäran till den angivna WEBBADRESSEN.
-   [Ange HTTP-proxy](#SetHttpProxy) - Gör att du kan dirigera vidarebefordrade begäranden via en HTTP-proxy.
-   [Ange begäransmetod](#SetRequestMethod) - Gör att du kan ändra HTTP-metoden för en begäran.
-   [Ange statuskod](#SetStatus) - Ändrar HTTP-statuskoden till det angivna värdet.
-   [Ange variabel](api-management-advanced-policies.md#set-variable) - Beständigar ett värde i en [namngiven kontextvariabel](api-management-policy-expressions.md#ContextVariables) för senare åtkomst.
-   [Spårning](#Trace) - Lägger till anpassade spårningar i API Inspector-utdata, application insights telemetries och diagnostikloggar. [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)
-   [Vänta](#Wait) - Väntar på bifogad [Skicka begäran](api-management-advanced-policies.md#SendRequest), Hämta värde [från cache-](api-management-caching-policies.md#GetFromCacheByKey)eller [kontrollflödesprinciper](api-management-advanced-policies.md#choose) som ska slutföras innan du fortsätter.

## <a name="control-flow"></a><a name="choose"></a>Kontrollflöde

Principen `choose` gäller slutna principuttalanden baserat på resultatet av utvärderingen av booleska uttryck, liknande en if-then-else eller en växelkonstruktion på ett programmeringsspråk.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a>Policyuttalande

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

Kontrollflödesprincipen måste innehålla minst `<when/>` ett element. Elementet `<otherwise/>` är valfritt. Villkor `<when/>` i element utvärderas i ordning efter deras utseende i principen. Principuttalanden som omges `<when/>` av det första `true` elementet med villkorsattribut är lika med tillämpas. Principer som omges av elementet, `<otherwise/>` om sådana `<when/>` finns, tillämpas `false`om alla elementvillkorsattribut är .

### <a name="examples"></a>Exempel

#### <a name="example"></a><a name="ChooseExample"></a>Exempel

I följande exempel visas en [princip med en viss variabel](api-management-advanced-policies.md#set-variable) och två styrflödesprinciper.

Den inställda variabelprincipen finns i det `isMobile` inkommande avsnittet och skapar en boolesk [kontextvariabel](api-management-policy-expressions.md#ContextVariables) som är inställd på true om `User-Agent` begäranden innehåller texten `iPad` eller `iPhone`.

Den första kontrollflödesprincipen finns också i det inkommande avsnittet och tillämpar villkorligt en av `isMobile` två [ange frågesträngparameterprinciper](api-management-transformation-policies.md#SetQueryStringParameter) beroende på värdet för kontextvariabeln.

Den andra kontrollflödesprincipen finns i det utgående avsnittet och tillämpar `isMobile` villkorligt `true`principen Konvertera XML [till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) när den är inställd på .

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

I det här exemplet visas hur du utför innehållsfiltrering genom att `Starter` ta bort dataelement från svaret från serverd-tjänsten när du använder produkten. En demonstration av att konfigurera och använda den här principen finns i [Cloud Cover Episode 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och snabbspola fram till 34:30. Börja 31:50 för att se en översikt över [The Dark Sky Forecast API](https://developer.forecast.io/) som används för den här demon.

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

| Element   | Beskrivning                                                                                                                                                                                                                                                               | Krävs |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Välja    | Rotelementet.                                                                                                                                                                                                                                                             | Ja      |
| När      | Villkoret som ska `if` `ifelse` användas för `choose` principens eller delar. Om `choose` principen har `when` flera avsnitt utvärderas de sekventiellt. När `condition` av a när elementet utvärderas till `true`utvärderas inga ytterligare `when` villkor. | Ja      |
| Annars | Innehåller det principkodavsnitt som ska användas `when` om inget `true`av villkoren utvärderas till .                                                                                                                                                                               | Inga       |

### <a name="attributes"></a>Attribut

| Attribut                                              | Beskrivning                                                                                               | Krävs |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Boolean uttryck &#124; Boolean konstant" | Det booleska uttrycket eller konstanten som ska utvärderas när den innehållande `when` principsatsen utvärderas. | Ja      |

### <a name="usage"></a><a name="ChooseUsage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="forward-request"></a><a name="ForwardRequest"></a>Begäran om vidarebefordran

Principen `forward-request` vidarebefordrar den inkommande begäran till serverdtjänsten som anges i [begärandens kontext](api-management-policy-expressions.md#ContextVariables). Serveringstjänstens URL anges i [API-inställningarna](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) och kan ändras med hjälp av principen [för tjänsten för serveringstjänst.](api-management-transformation-policies.md)

> [!NOTE]
> Om du tar bort den här principen visas begäran som inte vidarebefordras till backend-tjänsten och principerna i det utgående avsnittet utvärderas omedelbart efter det att principerna i det inkommande avsnittet har slutförts.

### <a name="policy-statement"></a>Policyuttalande

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

Följande API-nivåprincip vidarebefordrar alla API-begäranden till serverdtjänsten med ett tidsfördämningsintervall på 60 sekunder.

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

Den här åtgärdsnivåprincipen använder elementet `base` för att ärva serverdelsprincipen från det överordnade API-scopet.

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

Den här åtgärdsprincipen vidarebefordrar uttryckligen alla begäranden till serverdatjänsten med en timeout på 120 och ärver inte den överordnade principen för API-nivå. Om backend-tjänsten svarar med en felstatuskod från 400 till 599 inklusive, utlöses [avsnittet med fel.](api-management-error-handling-policies.md)

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Exempel

Den här åtgärdsnivåprincipen vidarebefordrar inte begäranden till backend-tjänsten.

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

| Element         | Beskrivning   | Krävs |
| --------------- | ------------- | -------- |
| vidarebefordran av begäran | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut                                     | Beskrivning                                                                                                                                                                                                                                                                                                    | Krävs | Default |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="heltal"                             | Den tid i sekunder som ska vänta på att HTTP-svarshuvudena ska returneras av backend-tjänsten innan ett timeout-fel utlöses. Minimivärdet är 0 sekunder. Värden som är större än 240 sekunder kanske inte respekteras eftersom den underliggande nätverksinfrastrukturen kan släppa inaktiva anslutningar efter denna tid. | Inga       | Inget    |
| follow-redirects="false &#124; true"          | Anger om omdirigeringar från backend-tjänsten följs av gatewayen eller returneras till anroparen.                                                                                                                                                                                                    | Inga       | false   |
| buffer-request-body="falskt &#124; sant"       | När den är inställd på "true" begäran buffras och kommer att återanvändas vid [återförsök](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Inga       | false   |
| misslyckas-på-fel-status-code="falskt &#124; sant" | När den är inställd på true triggers [on-error](api-management-error-handling-policies.md) avsnitt för svarskoder i intervallet från 400 till 599 inklusive.                                                                                                                                                                      | Inga       | false   |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** backend
-   **Principomfattningar:** alla scope

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>Begränsa samtidighet

Principen `limit-concurrency` förhindrar att slutna principer körs av mer än det angivna antalet begäranden när som helst. När du överskrider det antalet misslyckas nya begäranden omedelbart med 429 statuskod för för många begäranden.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a>Policyuttalande

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

I följande exempel visas hur du begränsar antalet begäranden som vidarebefordras till en backend baserat på värdet för en kontextvariabel.

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

| Element           | Beskrivning   | Krävs |
| ----------------- | ------------- | -------- |
| limit-concurrency | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                        | Krävs | Default |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Ett snöre. Uttryck tillåtet. Anger concurrency-scopet. Kan delas av flera principer. | Ja      | Ej tillämpligt     |
| max-räkna | Ett heltal. Anger ett maximalt antal begäranden som tillåts ange principen.           | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a>Logga till händelsehubben

Principen `log-to-eventhub` skickar meddelanden i angivet format till en händelsehubb som definieras av en Logger-entitet. Som namnet antyder används principen för att spara vald information om begäran eller svarskontext för online- eller offlineanalys.

> [!NOTE]
> En steg-för-steg-guide om hur du konfigurerar en händelsehubb och loggningshändelser finns i [Så här loggar du API Management-händelser med Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Policyuttalande

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exempel

Vilken sträng som helst kan användas som det värde som ska loggas i eventhubbar. I det här exemplet loggas datum och tid, distributionstjänstnamn, begärande-ID, IP-adress och operationsnamn `contoso-logger` för alla inkommande samtal till händelsehubben Logger som registrerats med ID:t

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

| Element         | Beskrivning                                                                     | Krävs |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-till-eventhub | Rotelementet. Värdet för det här elementet är strängen som ska loggas till händelsehubben. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                               | Krävs                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | Logger-ID:t som är registrerat hos din API Management-tjänst.         | Ja                                                                  |
| partition-id  | Anger indexet för den partition där meddelanden skickas.             | Valfri. Det här attributet `partition-key` kanske inte används om det används. |
| partition-nyckel | Anger det värde som används för partitionstilldelning när meddelanden skickas. | Valfri. Det här attributet `partition-id` kanske inte används om det används.  |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="mock-response"></a><a name="mock-response"></a>Håna svar

Den `mock-response`, som namnet antyder, används för att håna API:er och åtgärder. Den avbryter normal pipeline-körning och returnerar ett hånat svar till anroparen. Policyn försöker alltid att returnera svar av högsta trohet. Den föredrar exempel på svarsinnehåll, när det är tillgängligt. Det genererar exempel svar från scheman, när scheman tillhandahålls och exempel inte. Om varken exempel eller scheman hittas returneras svar utan innehåll.

### <a name="policy-statement"></a>Policyuttalande

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

| Element       | Beskrivning   | Krävs |
| ------------- | ------------- | -------- |
| mock-svar | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut    | Beskrivning                                                                                           | Krävs | Default |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-kod  | Anger svarsstatuskod och används för att välja motsvarande exempel eller schema.                 | Inga       | 200     |
| innehållstyp | Anger `Content-Type` svarshuvudvärde och används för att välja motsvarande exempel eller schema. | Inga       | Inget    |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, på fel

-   **Principomfattningar:** alla scope

## <a name="retry"></a><a name="Retry"></a>Igen

Principen `retry` kör sina underordnade principer en gång och försöker `condition` `false` sedan utföra `count` dem tills återförsöket blir eller försöker igen är uttömt.

### <a name="policy-statement"></a>Policyuttalande

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

I följande exempel görs vidarebefordran av begäran upp till tio gånger med hjälp av en exponentiell algoritm för återförsök. Sedan `first-fast-retry` är inställd på false, alla försök att försöka igen är föremål för exponentiell återförsök algoritm.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Element

| Element | Beskrivning                                                         | Krävs |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Rotelementet. Kan innehålla andra policyer som underordnade element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                                                                                                                                           | Krävs | Default |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Villkor        | Ett booleskt bokstavligt eller [uttryck](api-management-policy-expressions.md) som anger`false`om återförsök`true`ska stoppas ( ) eller fortsätta ( ).      | Ja      | Ej tillämpligt     |
| count            | Ett positivt tal som anger det maximala antalet försök att försöka.                                                                                | Ja      | Ej tillämpligt     |
| interval         | Ett positivt tal i sekunder som anger vänteintervallet mellan försöken att försöka igen.                                                                 | Ja      | Ej tillämpligt     |
| max-intervall     | Ett positivt tal i sekunder som anger det maximala vänteintervallet mellan försöken att försöka igen. Det används för att implementera en exponentiell algoritm för återförsök. | Inga       | Ej tillämpligt     |
| Delta (delta)            | Ett positivt tal i sekunder som anger vänteintervallet. Det används för att implementera de linjära och exponentiella algoritmerna för återförsök.             | Inga       | Ej tillämpligt     |
| första snabba försök | Om det `true` är inställt på utförs det första försöket om ett nytt försök omedelbart.                                                                                  | Inga       | `false` |

> [!NOTE]
> När endast `interval` det angivna görs **fasta** intervallförsök.
> När endast `interval` `delta` och anges används en **linjär** intervallalgoritm för återförsök, där väntetiden mellan återförsök beräknas enligt följande formel - `interval + (count - 1)*delta`.
> När `interval`algoritmen för `max-interval` , och `delta` anges används **exponentiell** intervallretrysalgoritm, där väntetiden mellan återförsöken växer exponentiellt från värdet `interval` till värdet `max-interval` enligt följande formel - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Observera att begränsningar för användning av underordnade principer ärvs av den här principen.

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="return-response"></a><a name="ReturnResponse"></a>Retursvar

Principen `return-response` avbryter pipeline-körningen och returnerar antingen ett standardsvar eller ett anpassat svar till anroparen. Standardsvaret `200 OK` är utan brödtext. Anpassat svar kan anges via en kontextvariabel eller principsatser. När båda tillhandahålls ändras svaret i kontextvariabeln av principuttalandena innan det returneras till anroparen.

### <a name="policy-statement"></a>Policyuttalande

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

| Element         | Beskrivning                                                                               | Krävs |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| retur-svar | Rotelementet.                                                                             | Ja      |
| set-header      | Ett [principuttalande för set-header.](api-management-transformation-policies.md#SetHTTPheader) | Inga       |
| set-body set-body set-body set-        | Ett [principuttalande för set-body.](api-management-transformation-policies.md#SetBody)         | Inga       |
| set-status      | Ett [principuttalande för set-status.](api-management-advanced-policies.md#SetStatus)           | Inga       |

### <a name="attributes"></a>Attribut

| Attribut              | Beskrivning                                                                                                                                                                          | Krävs  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| svarsvariabel-namn | Namnet på kontextvariabeln som refereras från till exempel en `Response` princip för överföring av [överföringsbegäran](api-management-advanced-policies.md#SendRequest) och som innehåller ett objekt | Valfri. |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a>Skicka envägsförfrågan

Principen `send-one-way-request` skickar den angivna begäran till den angivna WEBBADRESSEN utan att vänta på ett svar.

### <a name="policy-statement"></a>Policyuttalande

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

Den här exempelprincipen `send-one-way-request` visar ett exempel på hur du använder principen för att skicka ett meddelande till ett slackchattrum om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns i [Använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element                    | Beskrivning                                                                                                 | Krävs                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| skicka envägsbegäran       | Rotelementet.                                                                                               | Ja                             |
| url                        | Url:en för begäran.                                                                                     | Nej om mode=copy; annars ja. |
| metod                     | HTTP-metoden för begäran.                                                                            | Nej om mode=copy; annars ja. |
| sidhuvud                     | Begär rubrik. Använd flera rubrikelement för flera begäranderubriker.                                  | Inga                              |
| body                       | Begäran kroppen.                                                                                           | Inga                              |
| autentiseringscertifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Inga                              |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="sträng" | Avgör om det är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initieras inte begärandetexten i utgående läge.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Inga       | Ny      |
| namn          | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt      |
| finns-åtgärd | Anger vilken åtgärd som ska vidtas när huvudet redan har angetts. Det här attributet måste ha något av följande värden.<br /><br /> - åsidosättning - ersätter värdet för det befintliga huvudet.<br />- hoppa över - ersätter inte det befintliga huvudvärdet.<br />- lägg till - lägger till värdet i det befintliga huvudvärdet.<br />- ta bort - tar bort huvudet från begäran.<br /><br /> När den `override` är inställd på att värva flera poster med samma namn resulterar i att huvudet ställs in enligt alla poster (som kommer att visas flera gånger); endast angivna värden kommer att anges i resultatet. | Inga       | Åsidosätta |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="send-request"></a><a name="SendRequest"></a>Skicka begäran

Principen `send-request` skickar den angivna begäran till den angivna URL:en och väntar inte längre än det angivna timeout-värdet.

### <a name="policy-statement"></a>Policyuttalande

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

I det här exemplet visas ett sätt att verifiera en referenstoken med en auktoriseringsserver. Mer information om det här exemplet finns i [Använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element                    | Beskrivning                                                                                                 | Krävs                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| skicka-begäran               | Rotelementet.                                                                                               | Ja                             |
| url                        | Url:en för begäran.                                                                                     | Nej om mode=copy; annars ja. |
| metod                     | HTTP-metoden för begäran.                                                                            | Nej om mode=copy; annars ja. |
| sidhuvud                     | Begär rubrik. Använd flera rubrikelement för flera begäranderubriker.                                  | Inga                              |
| body                       | Begäran kroppen.                                                                                           | Inga                              |
| autentiseringscertifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Inga                              |

### <a name="attributes"></a>Attribut

| Attribut                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="sträng"                   | Avgör om det är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initieras inte begärandetexten i utgående läge.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Inga       | Ny      |
| svarsvariabel-name="string" | Namnet på kontextvariabeln som ska få ett svarsobjekt. Om variabeln inte finns skapas den när principen har slutförts [`context.Variable`](api-management-policy-expressions.md#ContextVariables) och blir tillgänglig via samlingen.                                                                                                                                                                                                                                                                                                                          | Ja      | Ej tillämpligt      |
| timeout="heltal"               | Tidsgränsen i sekunder innan samtalet till webbadressen misslyckas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Inga       | 60       |
| ignorera-fel                    | Om sant och begäran resulterar i ett fel:<br /><br /> - Om svarsvariabel-namn angavs kommer det att innehålla ett null-värde.<br />- Om svarsvariabel-namn inte angavs, kontext. Begäran kommer inte att uppdateras.                                                                                                                                                                                                                                                                                                                                                                                   | Inga       | false    |
| namn                            | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt      |
| finns-åtgärd                   | Anger vilken åtgärd som ska vidtas när huvudet redan har angetts. Det här attributet måste ha något av följande värden.<br /><br /> - åsidosättning - ersätter värdet för det befintliga huvudet.<br />- hoppa över - ersätter inte det befintliga huvudvärdet.<br />- lägg till - lägger till värdet i det befintliga huvudvärdet.<br />- ta bort - tar bort huvudet från begäran.<br /><br /> När den `override` är inställd på att värva flera poster med samma namn resulterar i att huvudet ställs in enligt alla poster (som kommer att visas flera gånger); endast angivna värden kommer att anges i resultatet. | Inga       | Åsidosätta |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a>Ange HTTP-proxy

Med `proxy` principen kan du dirigera begäranden som vidarebefordras till backends via en HTTP-proxy. Endast HTTP (inte HTTPS) stöds mellan gatewayen och proxyn. Endast grundläggande autentisering och NTLM-autentisering.

### <a name="policy-statement"></a>Policyuttalande

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exempel

Observera att [du](api-management-howto-properties.md) använder egenskaper som värden för användarnamnet och lösenordet för att undvika att lagra känslig information i principdokumentet.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Element

| Element | Beskrivning  | Krävs |
| ------- | ------------ | -------- |
| proxy   | Rotelementet | Ja      |

### <a name="attributes"></a>Attribut

| Attribut         | Beskrivning                                            | Krävs | Default |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="sträng"      | Proxy-URL i http://host:portform av .             | Ja      | Ej tillämpligt     |
| användarnamn="sträng" | Användarnamn som ska användas för autentisering med proxyn. | Inga       | Ej tillämpligt     |
| password="string" | Lösenord som ska användas för autentisering med proxyn. | Inga       | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande

-   **Principomfattningar:** alla scope

## <a name="set-request-method"></a><a name="SetRequestMethod"></a>Ange metod för begäran

Med `set-method` principen kan du ändra HTTP-begäransmetoden för en begäran.

### <a name="policy-statement"></a>Policyuttalande

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exempel

Den här exempelprincipen `set-method` som använder principen visar ett exempel på att skicka ett meddelande till ett slackchattrum om HTTP-svarskoden är större än eller lika med 500. Mer information om det här exemplet finns i [Använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element    | Beskrivning                                                       | Krävs |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-metod | Rotelementet. Elementets värde anger HTTP-metoden. | Ja      |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, on-error

-   **Principomfattningar:** alla scope

## <a name="set-status-code"></a><a name="SetStatus"></a>Ange statuskod

Principen `set-status` anger HTTP-statuskoden till det angivna värdet.

### <a name="policy-statement"></a>Policyuttalande

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exempel

Det här exemplet visar hur du returnerar ett 401-svar om auktoriseringstoken är ogiltig. Mer information finns i [Använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

| Element    | Beskrivning   | Krävs |
| ---------- | ------------- | -------- |
| set-status | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut       | Beskrivning                                                | Krävs | Default |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="heltal"  | DEN HTTP-statuskod som ska returneras.                            | Ja      | Ej tillämpligt     |
| reason="sträng" | En beskrivning av orsaken till att statuskoden returneras. | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** utgående, bakåtsträvande, på fel
-   **Principomfattningar:** alla scope

## <a name="set-variable"></a><a name="set-variable"></a>Ange variabel

Principen `set-variable` deklarerar en [kontextvariabel](api-management-policy-expressions.md#ContextVariables) och tilldelar den ett värde som anges via ett [uttryck](api-management-policy-expressions.md) eller en stränglitteral. Om uttrycket innehåller en litteral konverteras det till en sträng och `System.String`typen av värde blir .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a>Policyuttalande

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a>Exempel

I följande exempel visas en uppsättning variabelprincip i det inkommande avsnittet. Den här variabelprincipen `isMobile` skapar en boolesk [kontextvariabel](api-management-policy-expressions.md#ContextVariables) som är inställd på true om `User-Agent` begäranden innehåller texten `iPad` eller `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Element

| Element      | Beskrivning   | Krävs |
| ------------ | ------------- | -------- |
| set-variabel | Rotelementet. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                              | Krävs |
| --------- | ------------------------------------------------------------------------ | -------- |
| namn      | Namnet på variabeln.                                                | Ja      |
| värde     | Variabelns värde. Detta kan vara ett uttryck eller ett litteralt värde. | Ja      |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel
-   **Principomfattningar:** alla scope

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a>Tillåtna typer

Uttryck som används `set-variable` i principen måste returnera en av följande grundläggande typer.

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
-   System.Double (System.Double)
-   System.Guid (på samma sätt som)
-   System.Sträng
-   System.Char (på)System.Char )
-   System.DateTime
-   system.timespan
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

## <a name="trace"></a><a name="Trace"></a>Spåra

Principen `trace` lägger till en anpassad spårning i API Inspector-utdata, Application Insights telemetries och/eller diagnostikloggar.

-   Principen lägger till en anpassad spårning i API Inspector-utdata `Ocp-Apim-Trace` när spårning utlöses, `Ocp-Apim-Subscription-Key` d.v.s. begäranden finns och är inställt på true och begäranden finns och innehåller en giltig nyckel som gör det möjligt att spåra. [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)
-   Principen skapar en [Spårningstelemetri](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) i Application Insights, när [Application Insights-integrering](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) är aktiverad och den `severity` nivå som anges i principen är på eller högre än den `verbosity` nivå som anges i diagnostikinställningen.
-   Principen lägger till en egenskap i loggtransaktionen när [diagnostikloggar](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) är aktiverade och allvarlighetsgraden som anges i principen är på eller högre än den verbositetsnivå som anges i diagnostikinställningen.

### <a name="policy-statement"></a>Policyuttalande

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a>Exempel

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Element

| Element  | Beskrivning                                                                                                                                          | Krävs |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Spåra    | Rotelementet.                                                                                                                                        | Ja      |
| meddelande  | En sträng eller ett uttryck som ska loggas.                                                                                                                 | Ja      |
| metadata | Lägger till en anpassad egenskap i telemetrin Application Insights [Trace.](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) | Inga       |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                                               | Krävs | Default |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| källa    | Stränglitteral som är meningsfull för spårningsvisaren och ange källan till meddelandet.                                   | Ja      | Ej tillämpligt     |
| allvarlighetsgrad  | Anger spårningens allvarlighetsgrad. Tillåtna `verbose`värden `information` `error` är , (från lägsta till högsta). | Inga       | Verbose |
| namn      | Namnet på egenskapen.                                                                                                     | Ja      | Ej tillämpligt     |
| värde     | Fastighetens värde.                                                                                                    | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

## <a name="wait"></a><a name="Wait"></a>Vänta

Principen `wait` kör sina omedelbara underordnade principer parallellt och väntar på att antingen alla eller en av dess omedelbara underordnade principer ska slutföras innan den slutförs. Vänta-principen kan ha som sina omedelbara underordnade principer [Skicka begäran,](api-management-advanced-policies.md#SendRequest) [Hämta värde från cache-](api-management-caching-policies.md#GetFromCacheByKey)och [kontrollflödesprinciper.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Policyuttalande

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exempel

I följande exempel finns `choose` det två principer `wait` som omedelbar underordnade principer för principen. Var och `choose` en av dessa principer körs parallellt. Varje `choose` princip försöker hämta ett cachelagrat värde. Om det finns en cache miss, en backend tjänst anropas för att ange värdet. I det `wait` här exemplet slutförs principen inte förrän alla `for` dess underordnade `all`principer har slutförts, eftersom attributet är inställt på . I det här exemplet`execute-branch-one`deklareras kontextvariablerna ( , `value-one`, `execute-branch-two`och `value-two`) utanför omfattningen av den här exempelprincipen.

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

| Element | Beskrivning                                                                                                   | Krävs |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Vänta    | Rotelementet. Kan innehålla endast `send-request`underordnade element , `cache-lookup-value`och `choose` principer. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                            | Krävs | Default |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| för       | Avgör om `wait` principen väntar på att alla omedelbara underordnade principer ska slutföras eller bara en. Tillåtna värden är:<br /><br /> - `all`- vänta på att alla omedelbara barnpolicyer ska slutföras<br />- någon - vänta på någon omedelbar barn politik för att slutföra. När den första omedelbara underordnade principen har slutförts `wait` slutförs och genomförandet av andra omedelbara underordnade principer avslutas. | Inga       | all     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, utgående, bakåtsträvande
-   **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API-hantering](api-management-howto-policies.md)
-   [Principuttryck](api-management-policy-expressions.md)
-   [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
-   [Policyexempel](policy-samples.md)
