---
title: Avancerade principer för Azure API Management | Microsoft Docs
description: Lär dig mer om de avancerade principerna som är tillgängliga för användning i Azure API Management.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266174"
---
# <a name="api-management-advanced-policies"></a>API Management avancerade principer

Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AdvancedPolicies"></a>Avancerade principer

-   [Kontroll flöde](api-management-advanced-policies.md#choose) – villkorligt tillämpar princip uttryck baserat på resultatet av utvärderingen av booleska [uttryck](api-management-policy-expressions.md).
-   [Vidarebefordra begäran](#ForwardRequest) – vidarebefordrar begäran till backend-tjänsten.
-   [Begränsning av samtidighet](#LimitConcurrency) – förhindrar att omslutna principer körs av fler än det angivna antalet begär anden i taget.
-   [Logga till Event Hub](#log-to-eventhub) – skickar meddelanden i angivet format till en Event Hub som definieras av en entitet för loggning.
-   [Skiss-svar](#mock-response) – avbryter pipeline-körningen och returnerar ett skissat svar direkt till anroparen.
-   [Försök igen](#Retry) – nya försök att köra de omslutna princip instruktionerna, om och tills villkoret är uppfyllt. Körningen upprepas vid de angivna tidsintervallen och upp till det angivna antalet försök.
-   [RETUR svar](#ReturnResponse) – avbryter pipeline-körningen och returnerar det angivna svaret direkt till anroparen.
-   [Skicka en enkelriktad förfrågan](#SendOneWayRequest) – skickar en begäran till angiven URL utan att vänta på ett svar.
-   [Skicka förfrågan](#SendRequest) – skickar en begäran till angiven URL.
-   [Ange http-proxy](#SetHttpProxy) – tillåter att du dirigerar vidarebefordrade begär Anden via en http-proxy.
-   [Ange metod för begäran](#SetRequestMethod) – gör att du kan ändra http-metoden för en begäran.
-   [Ange status kod](#SetStatus) – ändrar HTTP-statuskoden till det angivna värdet.
-   [Set Variable](api-management-advanced-policies.md#set-variable) – behåller ett värde i en namngiven [kontext](api-management-policy-expressions.md#ContextVariables) variabel för senare åtkomst.
-   [Trace](#Trace) – lägger till anpassade spårningar i [API-kontrollens](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata, Application Insights telemetrivärden och diagnostikloggar.
-   [Vänta](#Wait) – väntar på en bifogad [sändnings förfrågan](api-management-advanced-policies.md#SendRequest), [hämtar värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey)eller [styr flödes](api-management-advanced-policies.md#choose) principer som ska slutföras innan du fortsätter.

## <a name="choose"></a>Kontroll flöde

Principen för `choose` tillämpar de principer som har stängts på grund av resultatet av utvärderingen av booleska uttryck, ungefär som en if-then-Else-eller en switch-konstruktion i ett programmeringsspråk.

### <a name="ChoosePolicyStatement"></a>Princip kommentar

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

Kontroll flödes principen måste innehålla minst ett `<when/>`-element. `<otherwise/>`-elementet är valfritt. Villkor i `<when/>` element utvärderas i ordning efter deras utseende i principen. Princip satser som anges i det första `<when/>`-elementet med condition-attributet är lika med `true` tillämpas. Principer som omges av `<otherwise/>`-elementet, om det finns, kommer att tillämpas om alla condition-attribut för `<when/>` element `false`.

### <a name="examples"></a>Exempel

#### <a name="ChooseExample"></a>Exempel

I följande exempel visas en [uppsättning-variabel](api-management-advanced-policies.md#set-variable) princip och två kontroll flödes principer.

Den angivna variabel principen finns i avsnittet inkommande och skapar en `isMobile` boolesk [kontext](api-management-policy-expressions.md#ContextVariables) variabel som är inställd på sant om `User-Agent` begär ande rubriken innehåller texten `iPad` eller `iPhone`.

Den första kontroll flödes principen ingår också i avsnittet inkommande och tillämpar villkoret en av två [ange parameter](api-management-transformation-policies.md#SetQueryStringParameter) principer för frågesträng beroende på värdet för variabeln `isMobile`.

Den andra kontroll flödes principen är i avsnittet för utgående trafik och tillämpar villkoret [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) när `isMobile` är inställt på `true`.

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

Det här exemplet visar hur du utför innehålls filtrering genom att ta bort data element från svaret som tagits emot från backend-tjänsten när du använder `Starter` produkten. En demonstration av hur du konfigurerar och använder den här principen finns i avsnittet [Cloud Cover avsnitt 177: fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 34:30. Börja vid 31:50 för att se en översikt över [den mörke prognos-API: et](https://developer.forecast.io/) som används för den här demon.

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
| använder    | Rot element.                                                                                                                                                                                                                                                             | Ja      |
| När      | Villkoret som ska användas för `if` eller `ifelse` delar av `choose` principen. Om `choose`-principen har flera `when` avsnitt utvärderas de i tur och ordning. När `condition` av ett när-element utvärderas till `true`utvärderas inga ytterligare `when` villkor. | Ja      |
| föreskrivs | Innehåller det princip-kodfragment som ska användas om inget av de `when` villkoren utvärderas till `true`.                                                                                                                                                                               | Nej       |

### <a name="attributes"></a>Attribut

| Attribut                                              | Beskrivning                                                                                               | Krävs |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| villkor = "boolesk konstant &#124; av boolesk uttryck" | Det booleska uttryck eller den konstant som ska utvärderas när den innehåller `when` princip instruktionen utvärderas. | Ja      |

### <a name="ChooseUsage"></a>Användningsvyn

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="ForwardRequest"></a>Vidarebefordra begäran

Den `forward-request` principen vidarebefordrar inkommande begäran till Server dels tjänsten som anges i [kontexten](api-management-policy-expressions.md#ContextVariables)för begäran. URL: en för backend-tjänsten anges i API- [inställningarna](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) och kan ändras med hjälp av [Ange server dels tjänst](api-management-transformation-policies.md) princip.

> [!NOTE]
> Om du tar bort den här principen skickas inte begäran till backend-tjänsten och principerna i det utgående avsnittet utvärderas omedelbart efter att principerna i avsnittet inkommande har slutförts.

### <a name="policy-statement"></a>Princip kommentar

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

Följande API-princip vidarebefordrar alla API-begäranden till backend-tjänsten med ett timeout-intervall på 60 sekunder.

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

Den här åtgärds nivå principen använder `base`-elementet för att ärva backend-principen från omfattningen för överordnad API-nivå.

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

Den här åtgärds nivå principen vidarebefordrar explicit alla förfrågningar till backend-tjänsten med en tids gräns på 120 och ärver inte den överordnade API-nivåns backend-princip. Om backend-tjänsten svarar med en fel status kod från 400 till 599, utlöses avsnittet [på fel](api-management-error-handling-policies.md) .

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

Den här åtgärds nivå principen vidarebefordrar inte begär anden till backend-tjänsten.

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
| Forward-Request | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut                                     | Beskrivning                                                                                                                                                                                                                                                                                                    | Krävs | Default |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="integer"                             | Hur lång tid i sekunder som HTTP-svarshuvuden ska returneras av backend-tjänsten innan ett tids gräns fel uppstår. Minimalt värde är 0 sekunder. Värden som är större än 240 sekunder kanske inte går att utföra eftersom den underliggande nätverks infrastrukturen kan släppa inaktiva anslutningar efter den här tiden. | Nej       | Ingen    |
| följa-redirects = "falskt &#124; sant"          | Anger om omdirigeringar från backend-tjänsten följs av gatewayen eller returneras till anroparen.                                                                                                                                                                                                    | Nej       | false   |
| Buffer-Request-Body = " &#124; false true"       | Vid inställt på "true"-begäran buffras och återanvänds [återförsök](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Nej       | false   |
| misslyckande-On-Error-status-Code = "false &#124; true" | När har angetts till True triggers [On-Error-](api-management-error-handling-policies.md) avsnittet för svars koder inom intervallet 400 till 599.                                                                                                                                                                      | Nej       | false   |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** backend
-   **Princip omfattningar:** alla omfattningar

## <a name="LimitConcurrency"></a>Begränsa samtidighet

Den `limit-concurrency` principen förhindrar att principer som har stängts körs av fler än det angivna antalet begär anden när som helst. Om du överskrider det numret Miss kommer nya begär Anden omedelbart med 429 för många begär Anden status kod.

### <a name="LimitConcurrencyStatement"></a>Princip kommentar

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

Följande exempel visar hur du begränsar antalet begär Anden som vidarebefordras till en server del baserat på värdet för en Sammanhangs variabel.

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
| gräns-concurrency | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                        | Krävs | Default |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | En sträng. Uttryck tillåts. Anger samtidighets omfång. Kan delas av flera principer. | Ja      | Ej tillämpligt     |
| Max antal | Ett heltal. Anger ett maximalt antal begär Anden som tillåts att ange principen.           | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="log-to-eventhub"></a>Logga till händelsehubben

Den `log-to-eventhub` principen skickar meddelanden i angivet format till en Event Hub som definieras av en entitet för loggning. Som namnet antyder används principen för att spara den valda förfrågnings-eller svars kontext informationen för online-eller offline-analys.

> [!NOTE]
> En steg-för-steg-guide om hur du konfigurerar händelse nav och loggnings händelser finns i [logga API Management händelser med Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Princip kommentar

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exempel

Du kan använda valfri sträng som värde för att logga in Event Hubs. I det här exemplet loggas datum och tid, namn på distributions tjänst, ID för begäran, IP-adress och åtgärds namn för alla inkommande anrop till Event Hub-loggaren som registrerats med `contoso-logger`-ID

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
| Logga till eventhub | Rot element. Värdet för det här elementet är den sträng som ska loggas till händelsehubben. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                               | Krävs                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| loggning-ID     | ID: t för den loggade logg filen som är registrerad i API Management-tjänsten.         | Ja                                                                  |
| partitions-ID  | Anger index för den partition där meddelanden skickas.             | Valfri. Det här attributet får inte användas om `partition-key` används. |
| partition – nyckel | Anger det värde som används för partition tilldelning när meddelanden skickas. | Valfri. Det här attributet får inte användas om `partition-id` används.  |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="mock-response"></a>Skiss svar

`mock-response`, som namnet antyder, används för att modellera API: er och åtgärder. Den normala körningen av pipelinen avbryts och returnerar ett skissat svar till anroparen. Principen försöker alltid returnera svar med störst åter givning. Det föredrar exempel på svars innehåll när det är tillgängligt. Den genererar exempel svar från scheman, när scheman anges och exempel inte. Om varken exempel eller scheman hittas returneras svar utan innehåll.

### <a name="policy-statement"></a>Princip kommentar

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
| modeller – svar | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut    | Beskrivning                                                                                           | Krävs | Default |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status kod  | Anger svars status kod och används för att välja motsvarande exempel eller schema.                 | Nej       | 200     |
| innehålls typ | Anger `Content-Type` svarets huvud värde och används för att välja motsvarande exempel eller schema. | Nej       | Ingen    |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="Retry"></a>Försök igen

Den `retry` principen kör sina underordnade principer en gång och gör sedan om körningen tills återförsöket `condition` `false` eller försöker igen `count` är förbrukat.

### <a name="policy-statement"></a>Princip kommentar

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

I följande exempel görs en begäran om vidarebefordran till tio gånger med en algoritm för exponentiellt återförsök. Eftersom `first-fast-retry` har angetts till false, omfattas alla nya återförsök för algoritmen för exponentiellt försök.

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
| retry   | Rot element. Kan innehålla andra principer som underordnade element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                                                                                                                                           | Krävs | Default |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | En boolesk literal eller ett [uttryck](api-management-policy-expressions.md) som anger om försök ska stoppas (`false`) eller fortsatte (`true`).      | Ja      | Ej tillämpligt     |
| count            | Ett positivt tal som anger det maximala antalet återförsök som ska göras.                                                                                | Ja      | Ej tillämpligt     |
| interval         | Ett positivt tal i sekunder som anger vänte intervallet mellan Återförsöken.                                                                 | Ja      | Ej tillämpligt     |
| max intervall     | Ett positivt tal i sekunder som anger det högsta vänte intervallet mellan försöken. Den används för att implementera en algoritm för exponentiell återförsök. | Nej       | Ej tillämpligt     |
| delta            | Ett positivt tal i sekunder som anger hur många vänte intervall som ska ökas. Den används för att implementera de linjära och exponentiella nya algoritmerna.             | Nej       | Ej tillämpligt     |
| första – snabb återförsök | Om värdet är `true` utförs det första försöket omedelbart.                                                                                  | Nej       | `false` |

> [!NOTE]
> Om endast `interval` anges utförs nya försök för **fast** intervall.
> När endast `interval` och `delta` anges används en algoritm för omförsök av **linjär** intervall, där vänte tiden mellan återförsök beräknas enligt följande formel-`interval + (count - 1)*delta`.
> När `interval`, `max-interval` och `delta` anges, tillämpas algoritmen för **exponentiell** intervalls-omförsök, där vänte tiden mellan Återförsöken ökar exponentiellt från värdet för `interval` till värdet `max-interval` enligt följande formel `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Observera att användnings begränsningar för underordnade principer kommer att ärvas av den här principen.

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="ReturnResponse"></a>Retur svar

`return-response` principen avbryter pipeline-körningen och returnerar antingen ett standard-eller anpassat svar till anroparen. Standard svaret är `200 OK` utan bröd text. Anpassat svar kan anges via en Sammanhangs variabel eller princip satser. När båda anges ändras svaret som finns i Sammanhangs variabeln av princip satserna innan de returneras till anroparen.

### <a name="policy-statement"></a>Princip kommentar

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
| returnera svar | Rot element.                                                                             | Ja      |
| set-header      | En princip sats för [set-Heading](api-management-transformation-policies.md#SetHTTPheader) . | Nej       |
| Set-Body        | En princip för en [uppsättning med brödtext](api-management-transformation-policies.md#SetBody) .         | Nej       |
| set-status      | En instruktion för [set-status-](api-management-advanced-policies.md#SetStatus) princip.           | Nej       |

### <a name="attributes"></a>Attribut

| Attribut              | Beskrivning                                                                                                                                                                          | Krävs  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| svar-variabel-namn | Namnet på den Sammanhangs variabel som refereras från, till exempel en överordnad princip för [skicka begäran](api-management-advanced-policies.md#SendRequest) och innehåller ett `Response`-objekt | Valfri. |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SendOneWayRequest"></a>Skicka en enkelriktad begäran

Den `send-one-way-request` principen skickar den angivna begäran till den angivna URL: en utan att vänta på ett svar.

### <a name="policy-statement"></a>Princip kommentar

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

I den här exempel principen visas ett exempel på hur du använder `send-one-way-request`s princip för att skicka ett meddelande till ett slack-chattrum om HTTP-svars koden är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| Skicka – envägs-begäran       | Rot element.                                                                                               | Ja                             |
| url                        | URL för begäran.                                                                                     | Nej om läge = kopiera; annars Ja. |
| metod                     | HTTP-metoden för begäran.                                                                            | Nej om läge = kopiera; annars Ja. |
| sidhuvud                     | Begär ande huvud. Använd flera huvud element för flera begärandehuvuden.                                  | Nej                              |
| brödtext                       | Begär ande texten.                                                                                           | Nej                              |
| autentisering-certifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Nej                              |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Anger om det här är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initierar inte läge = kopiera begär ande texten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nej       | Ny      |
| namn          | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt      |
| exists-åtgärd | Anger vilken åtgärd som ska vidtas när rubriken redan har angetts. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet i den befintliga rubriken.<br />-Skip-ersätter inte det befintliga huvud-värdet.<br />-append – lägger till värdet i det befintliga huvud-värdet.<br />-Delete – tar bort rubriken från begäran.<br /><br /> När det är inställt på `override` att en lista över flera poster med samma namn resulterar i att rubriken anges enligt alla poster (som visas flera gånger). endast värden som visas i resultatet anges. | Nej       | åsidosättning |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SendRequest"></a>Skicka begäran

Den `send-request` principen skickar den angivna begäran till den angivna URL: en och väntar inte längre än värdet för Ange tids gräns.

### <a name="policy-statement"></a>Princip kommentar

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

Det här exemplet visar ett sätt att verifiera en reference-token med en Authorization Server. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| skicka begäran               | Rot element.                                                                                               | Ja                             |
| url                        | URL för begäran.                                                                                     | Nej om läge = kopiera; annars Ja. |
| metod                     | HTTP-metoden för begäran.                                                                            | Nej om läge = kopiera; annars Ja. |
| sidhuvud                     | Begär ande huvud. Använd flera huvud element för flera begärandehuvuden.                                  | Nej                              |
| brödtext                       | Begär ande texten.                                                                                           | Nej                              |
| autentisering-certifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Nej                              |

### <a name="attributes"></a>Attribut

| Attribut                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Krävs | Default  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Anger om det här är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initierar inte läge = kopiera begär ande texten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nej       | Ny      |
| response-variable-name="string" | Namnet på den Sammanhangs variabel som ska ta emot ett svars objekt. Om variabeln inte finns skapas den när principen har körts och kommer att bli tillgänglig via [`context.Variable`](api-management-policy-expressions.md#ContextVariables) samling.                                                                                                                                                                                                                                                                                                                          | Ja      | Ej tillämpligt      |
| timeout="integer"               | Tids gräns intervallet i sekunder innan anropet till URL: en misslyckades.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nej       | 60       |
| Ignorera-fel                    | Om värdet är true och begäran resulterar i ett fel:<br /><br /> -Om Response-Variable-Name har angetts innehåller det ett null-värde.<br />– Om Response-Variable-Name inte angavs, context. Begäran kommer inte att uppdateras.                                                                                                                                                                                                                                                                                                                                                                                   | Nej       | false    |
| namn                            | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Ej tillämpligt      |
| exists-åtgärd                   | Anger vilken åtgärd som ska vidtas när rubriken redan har angetts. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet i den befintliga rubriken.<br />-Skip-ersätter inte det befintliga huvud-värdet.<br />-append – lägger till värdet i det befintliga huvud-värdet.<br />-Delete – tar bort rubriken från begäran.<br /><br /> När det är inställt på `override` att en lista över flera poster med samma namn resulterar i att rubriken anges enligt alla poster (som visas flera gånger). endast värden som visas i resultatet anges. | Nej       | åsidosättning |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SetHttpProxy"></a>Ange HTTP-proxy

Med den `proxy` principen kan du dirigera begär Anden som vidarebefordras till Server delar via en HTTP-proxy. Endast HTTP (inte HTTPS) stöds mellan gatewayen och proxyn. Endast grundläggande och NTLM-autentisering.

### <a name="policy-statement"></a>Princip kommentar

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Exempel

Observera att [Egenskaper](api-management-howto-properties.md) används som värden för användar namn och lösen ord för att undvika att lagra känslig information i princip dokumentet.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Element

| Element | Beskrivning  | Krävs |
| ------- | ------------ | -------- |
| Proxy   | Rot element | Ja      |

### <a name="attributes"></a>Attribut

| Attribut         | Beskrivning                                            | Krävs | Default |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | Proxy-URL i form av http://host:port.             | Ja      | Ej tillämpligt     |
| username="string" | Användar namn som ska användas för autentisering med proxyn. | Nej       | Ej tillämpligt     |
| Password = "sträng" | Lösen ord som ska användas för autentisering med proxyservern. | Nej       | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

## <a name="SetRequestMethod"></a>Ange metod för begäran

Med principen för `set-method` kan du ändra HTTP-begäran för en begäran.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exempel

I den här exempel principen som använder `set-method` principen visas ett exempel på hur du skickar ett meddelande till ett slack chatt-rum om HTTP-svarskod är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
| set-metod | Rot element. Värdet för elementet anger HTTP-metoden. | Ja      |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SetStatus"></a>Ange status kod

Den `set-status` principen anger HTTP-statuskoden till det angivna värdet.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Exempel

Det här exemplet visar hur du returnerar ett 401-svar om autentiseringstoken är ogiltig. Mer information finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
| set-status | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut       | Beskrivning                                                | Krävs | Default |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | Den HTTP-statuskod som ska returneras.                            | Ja      | Ej tillämpligt     |
| Orsak = "sträng" | En beskrivning av orsaken till att status koden returneras. | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** utgående, Server del, på-fel
-   **Princip omfattningar:** alla omfattningar

## <a name="set-variable"></a>Ange variabel

`set-variable` principen deklarerar en [kontext](api-management-policy-expressions.md#ContextVariables) variabel och tilldelar den ett värde som anges via ett [uttryck](api-management-policy-expressions.md) eller en tecken sträng. om uttrycket innehåller en literal kommer det att konverteras till en sträng och typen av värde kommer att `System.String`.

### <a name="set-variablePolicyStatement"></a>Princip kommentar

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Exempel

Följande exempel visar en uppsättning variabel princip i avsnittet inkommande. Den här uppsättningen variabel principen skapar en `isMobile` boolesk [kontext](api-management-policy-expressions.md#ContextVariables) variabel som är inställd på sant om `User-Agent` begär ande rubriken innehåller texten `iPad` eller `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Element

| Element      | Beskrivning   | Krävs |
| ------------ | ------------- | -------- |
| Set-Variable | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                              | Krävs |
| --------- | ------------------------------------------------------------------------ | -------- |
| namn      | Namnet på variabeln.                                                | Ja      |
| värde     | Variabelns värde. Detta kan vara ett uttryck eller ett tecken värde. | Ja      |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel
-   **Princip omfattningar:** alla omfattningar

### <a name="set-variableAllowedTypes"></a>Tillåtna typer

Uttryck som används i `set-variable` principen måste returnera någon av följande grundläggande typer.

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
-   System. GUID
-   System. String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System. byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System. Single?
-   System.Double?
-   System. GUID?
-   System. String?
-   System. char?
-   System.DateTime?

## <a name="Trace"></a>Rita

`trace` principen lägger till en anpassad spårning i API-kontrollens utdata, Application Insights telemetrivärden och/eller diagnostikloggar.

-   Principen lägger till en anpassad spårning i [API-kontrollens](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata när spårning utlöses, d.v.s. `Ocp-Apim-Trace` begär ande huvudet finns och anges till true och `Ocp-Apim-Subscription-Key` begär ande huvudet finns och innehåller en giltig nyckel som tillåter spårning.
-   Principen skapar en [trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) -telemetri i Application Insights när [Application Insights integration](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) är aktive rad och `severity` nivån som anges i principen är eller högre än den `verbosity` nivå som anges i den diagnostiska inställningen.
-   Principen lägger till en egenskap i logg posten när [diagnostikloggar](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) är aktive rad och allvarlighets graden som anges i principen är på eller högre än den detaljerade nivån som anges i den diagnostiska inställningen.

### <a name="policy-statement"></a>Princip kommentar

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="traceExample"></a>Exempel

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Element

| Element  | Beskrivning                                                                                                                                          | Krävs |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Rita    | Rot element.                                                                                                                                        | Ja      |
| meddelande  | En sträng eller ett uttryck som ska loggas.                                                                                                                 | Ja      |
| metadata | Lägger till en anpassad egenskap i Application Insights [trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) -telemetri. | Nej       |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                                               | Krävs | Default |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| källa    | Tecken strängen är meningsfull för spårnings visaren och anger källan till meddelandet.                                   | Ja      | Ej tillämpligt     |
| allvarlighets grad  | Anger spårningens allvarlighets nivå. Tillåtna värden är `verbose``information``error` (från lägsta till högsta). | Nej       | Utförlig |
| namn      | Egenskapens namn.                                                                                                     | Ja      | Ej tillämpligt     |
| värde     | Egenskapens värde.                                                                                                    | Ja      | Ej tillämpligt     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="Wait"></a>Vänta

Den `wait` principen kör sina omedelbara underordnade principer parallellt, och väntar på att alla eller en av dess omedelbara underordnade principer ska slutföras innan den är klar. Wait-principen kan ha samma omedelbara underordnade principer som [skicka begäran](api-management-advanced-policies.md#SendRequest), [Hämta värde från cache](api-management-caching-policies.md#GetFromCacheByKey)och [kontrol lera flödes](api-management-advanced-policies.md#choose) principer.

### <a name="policy-statement"></a>Princip kommentar

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exempel

I följande exempel finns det två `choose` principer som omedelbara underordnade principer för `wait`s principen. Var och en av dessa `choose` principer körs parallellt. Varje princip för `choose` försöker hämta ett cachelagrat värde. Om det finns ett cacheminne anropas en backend-tjänst för att ange värdet. I det här exemplet slutförs inte `wait`-principen förrän alla dess omedelbara underordnade principer har slutförts, eftersom attributet `for` är inställt på `all`. I det här exemplet deklareras kontextnodens variabler (`execute-branch-one`, `value-one`, `execute-branch-two`och `value-two`) utanför omfånget för den här exempel principen.

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
| Vänta    | Rot element. Får bara innehålla underordnade element `send-request`, `cache-lookup-value`och `choose` principer. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                            | Krävs | Default |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| söker       | Bestämmer om den `wait` principen väntar på att alla omedelbara underordnade principer ska slutföras eller bara en. Tillåtna värden är:<br /><br /> - `all`-vänta tills alla omedelbara underordnade principer har slutförts<br />– vänta tills en omedelbar underordnad princip har slutförts. När den första omedelbara underordnade principen har slutförts avbryts den `wait` principen och körningen av andra omedelbara underordnade principer avbryts. | Nej       | all     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, backend
-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

-   [Principer i API Management](api-management-howto-policies.md)
-   [Principuttryck](api-management-policy-expressions.md)
-   [Princip referens](api-management-policy-reference.md) för en fullständig lista över princip satser och deras inställningar
-   [Princip exempel](policy-samples.md)
