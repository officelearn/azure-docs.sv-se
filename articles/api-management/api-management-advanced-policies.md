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
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 166ff5f8866fca955cbe99c5896eb509f52261f6
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219565"
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

`choose` Principen använder sig av policy-instruktioner som baseras på resultatet av utvärderingen av booleska uttryck, liknande en if-then-Else-eller en switch-konstruktion i ett programmeringsspråk.

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

Kontroll flödes principen måste innehålla minst ett `<when/>` element. `<otherwise/>` Elementet är valfritt. Villkor i `<when/>` element utvärderas i ordning efter deras utseende i principen. Princip satser som är inneslutna i det första `<when/>` elementet med condition-attributet `true` är lika med. Principer som anges i `<otherwise/>` elementet, om det finns, kommer att tillämpas om alla attribut `<when/>` för element villkor är `false`.

### <a name="examples"></a>Exempel

#### <a name="ChooseExample"></a>Exempel

I följande exempel visas en [uppsättning-variabel](api-management-advanced-policies.md#set-variable) princip och två kontroll flödes principer.

Den angivna variabel principen finns i avsnittet inkommande och skapar en `isMobile` boolesk [Sammanhangs](api-management-policy-expressions.md#ContextVariables) variabel som är inställd `User-Agent` på True om begär ande rubriken innehåller texten `iPad` eller `iPhone`.

Den första kontroll flödes principen ingår också i avsnittet inkommande och tillämpar villkoret en av två [ange parameter](api-management-transformation-policies.md#SetQueryStringParameter) principer för frågesträng, beroende på värdet för `isMobile` kontext variabeln.

Den andra styrnings flödes principen är i avsnittet utgående och villkoret använder till att [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) -principen `isMobile` när `true`har angetts till.

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

I det här exemplet visas hur du utför innehålls filtrering genom att ta bort data element från svaret som tagits emot från backend `Starter` -tjänsten när du använder produkten. En demonstration av hur du konfigurerar och använder den här principen [finns i avsnittet Cloud Cover-avsnitt 177: Fler API Management funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och fast-forward till 34:30. Börja vid 31:50 för att se en översikt över [den mörke prognos-API: et](https://developer.forecast.io/) som används för den här demon.

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

| Element   | Beskrivning                                                                                                                                                                                                                                                               | Obligatorisk |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| använder    | Rot element.                                                                                                                                                                                                                                                             | Ja      |
| När      | Villkoret som ska användas för `if` eller `ifelse` -delar av `choose` principen. Om principen har flera `when` avsnitt utvärderas de i tur och ordning. `choose` När ett element i taget utvärderas till `true`utvärderas inga ytterligare `when` villkor. `condition` | Ja      |
| föreskrivs | Innehåller det princip-kodfragment som ska användas om inget av `when` villkoren `true`utvärderas.                                                                                                                                                                               | Nej       |

### <a name="attributes"></a>Attribut

| Attribut                                              | Beskrivning                                                                                               | Obligatorisk |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| villkor = "boolesk konstant &#124; av boolesk uttryck" | Det booleska uttryck eller den konstant som ska utvärderas när `when` den innehåll ande princip instruktionen utvärderas. | Ja      |

### <a name="ChooseUsage"></a>Användningsvyn

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="ForwardRequest"></a>Vidarebefordra begäran

Principen vidarebefordrar inkommande begäran till Server dels tjänsten som anges i kontexten för begäran. [](api-management-policy-expressions.md#ContextVariables) `forward-request` URL: en för backend-tjänsten anges i API- [inställningarna](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) och kan ändras med hjälp av [Ange server dels tjänst](api-management-transformation-policies.md) princip.

> [!NOTE]
> Om du tar bort den här principen skickas inte begäran till backend-tjänsten och principerna i det utgående avsnittet utvärderas omedelbart efter att principerna i avsnittet inkommande har slutförts.

### <a name="policy-statement"></a>Princip kommentar

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false" buffer-request-body="true | false" />
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

Den här åtgärds nivå principen `base` använder elementet för att ärva backend-principen från omfattningen för överordnad API-nivå.

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

Den här åtgärds nivå principen vidarebefordrar explicit alla förfrågningar till backend-tjänsten med en tids gräns på 120 och ärver inte den överordnade API-nivåns backend-princip.

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

| Element         | Beskrivning   | Obligatorisk |
| --------------- | ------------- | -------- |
| Forward-Request | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut                               | Beskrivning                                                                                                      | Obligatorisk | Standard     |
| --------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| timeout="integer"                       | Hur lång tid i sekunder som HTTP-svarshuvuden ska returneras av backend-tjänsten innan ett tids gräns fel uppstår. Minimalt värde är 0 sekunder. Värden som är större än 240 sekunder kanske inte går att utföra eftersom den underliggande nätverks infrastrukturen kan släppa inaktiva anslutningar efter den här tiden. | Nej       | Inga |
| follow-redirects="true &#124; false"    | Anger om omdirigeringar från backend-tjänsten följs av gatewayen eller returneras till anroparen.      | Nej       | false       |
| Buffer-Request-Body = " &#124; True false" | Vid inställt på "true"-begäran buffras och återanvänds [återförsök](api-management-advanced-policies.md#Retry). | Nej       | false       |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** backend
-   **Princip omfattningar:** alla omfattningar

## <a name="LimitConcurrency"></a>Begränsa samtidighet

`limit-concurrency` Principen förhindrar att principer som har stängts körs av fler än det angivna antalet begär anden när som helst. Om du överskrider det numret Miss kommer nya begär Anden omedelbart med 429 för många begär Anden status kod.

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

| Element           | Beskrivning   | Obligatorisk |
| ----------------- | ------------- | -------- |
| gräns-concurrency | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                        | Obligatorisk | Standard |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | En sträng. Uttryck tillåts. Anger samtidighets omfång. Kan delas av flera principer. | Ja      | Gäller inte     |
| Max antal | Ett heltal. Anger ett maximalt antal begär Anden som tillåts att ange principen.           | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="log-to-eventhub"></a>Logga till händelsehubben

`log-to-eventhub` Principen skickar meddelanden i angivet format till en Event Hub som definieras av en entitet för loggning. Som namnet antyder används principen för att spara den valda förfrågnings-eller svars kontext informationen för online-eller offline-analys.

> [!NOTE]
> En steg-för-steg-guide om hur du konfigurerar händelse nav och loggnings händelser finns i [logga API Management händelser med Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Princip kommentar

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Exempel

Du kan använda valfri sträng som värde för att logga in Event Hubs. I det här exemplet loggas datum och tid, distributions tjänstens namn, ID för begäran, IP-adress och åtgärds namn för alla inkommande anrop till Event Hub `contoso-logger` -loggaren som registrerats med ID: t.

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

| Element         | Beskrivning                                                                     | Obligatorisk |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| Logga till eventhub | Rot element. Värdet för det här elementet är den sträng som ska loggas till händelsehubben. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                               | Obligatorisk                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| loggning-ID     | ID: t för den loggade logg filen som är registrerad i API Management-tjänsten.         | Ja                                                                  |
| partitions-ID  | Anger index för den partition där meddelanden skickas.             | Valfritt. Det här attributet får inte användas om `partition-key` används. |
| partition – nyckel | Anger det värde som används för partition tilldelning när meddelanden skickas. | Valfritt. Det här attributet får inte användas om `partition-id` används.  |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="mock-response"></a>Skiss svar

`mock-response`, Som namnet antyder, används för att modellera API: er och åtgärder. Den normala körningen av pipelinen avbryts och returnerar ett skissat svar till anroparen. Principen försöker alltid returnera svar med störst åter givning. Det föredrar exempel på svars innehåll när det är tillgängligt. Den genererar exempel svar från scheman, när scheman anges och exempel inte. Om varken exempel eller scheman hittas returneras svar utan innehåll.

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

| Element       | Beskrivning   | Obligatorisk |
| ------------- | ------------- | -------- |
| modeller – svar | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut    | Beskrivning                                                                                           | Obligatorisk | Standard |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status kod  | Anger svars status kod och används för att välja motsvarande exempel eller schema.                 | Nej       | 200     |
| Innehålls typ | Anger `Content-Type` svarets huvud värde och används för att välja motsvarande exempel eller schema. | Nej       | Inga    |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="Retry"></a>Försök igen

Principen kör dess underordnade principer en gång och försöker sedan utföra körningen igen tills återförsöket `condition` blir `count` `false` eller så är ett nytt försök förbrukat. `retry`

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

I följande exempel görs en begäran om vidarebefordran till tio gånger med en algoritm för exponentiellt återförsök. Eftersom `first-fast-retry` har angetts till false, omfattas alla nya återförsök för algoritmen för exponentiellt nytt försök.

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

| Element | Beskrivning                                                         | Obligatorisk |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Rot element. Kan innehålla andra principer som underordnade element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut        | Beskrivning                                                                                                                                           | Obligatorisk | Standard |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | En boolesk literal eller ett [uttryck](api-management-policy-expressions.md) som anger om försök ska stoppas (`false`) eller fortsätta (`true`).      | Ja      | Gäller inte     |
| count            | Ett positivt tal som anger det maximala antalet återförsök som ska göras.                                                                                | Ja      | Gäller inte     |
| intervall         | Ett positivt tal i sekunder som anger vänte intervallet mellan Återförsöken.                                                                 | Ja      | Gäller inte     |
| max intervall     | Ett positivt tal i sekunder som anger det högsta vänte intervallet mellan försöken. Den används för att implementera en algoritm för exponentiell återförsök. | Nej       | Gäller inte     |
| delta            | Ett positivt tal i sekunder som anger hur många vänte intervall som ska ökas. Den används för att implementera de linjära och exponentiella nya algoritmerna.             | Nej       | Gäller inte     |
| första – snabb återförsök | Om det är `true` inställt på, utförs det första försöket omedelbart.                                                                                  | Nej       | `false` |

> [!NOTE]
> Om endast `interval` anges görs försök att utföra **fasta** intervall.
> När endast och `interval` `delta` anges används en algoritm för omförsök av **linjär** intervall, där vänte tiden mellan återförsök beräknas enligt följande formel- `interval + (count - 1)*delta`.
> `interval` När, ochanges`delta` , tillämpas algoritmen för exponentiellt intervall återförsök, där vänte tiden mellan Återförsöken ökar exponentiellt från värdet för till värdet `interval` `max-interval` `max-interval`enligt följande formel – `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Observera att användnings begränsningar för underordnade principer kommer att ärvas av den här principen.

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="ReturnResponse"></a>Retur svar

`return-response` Principen avbryter pipeline-körningen och returnerar antingen ett standard-eller anpassat svar till anroparen. Standardsvaret `200 OK` har ingen brödtext. Anpassat svar kan anges via en Sammanhangs variabel eller princip satser. När båda anges ändras svaret som finns i Sammanhangs variabeln av princip satserna innan de returneras till anroparen.

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

| Element         | Beskrivning                                                                               | Obligatorisk |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| returnera svar | Rot element.                                                                             | Ja      |
| set-header      | En princip sats för [set-Heading](api-management-transformation-policies.md#SetHTTPheader) . | Nej       |
| Set-Body        | En princip för en [uppsättning med brödtext](api-management-transformation-policies.md#SetBody) .         | Nej       |
| set-status      | En instruktion för [set-status-](api-management-advanced-policies.md#SetStatus) princip.           | Nej       |

### <a name="attributes"></a>Attribut

| Attribut              | Beskrivning                                                                                                                                                                          | Obligatorisk  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| svar-variabel-namn | Namnet på den Sammanhangs variabel som refereras från, till exempel en överordnad princip för [skicka begäran](api-management-advanced-policies.md#SendRequest) och innehåller `Response` ett objekt | Valfritt. |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SendOneWayRequest"></a>Skicka en enkelriktad begäran

`send-one-way-request` Principen skickar den angivna begäran till angiven URL utan att vänta på något svar.

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

I den här exempel principen visas ett exempel på `send-one-way-request` hur du använder principen för att skicka ett meddelande till ett slack chatt-rum om HTTP-svarskod är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element                    | Beskrivning                                                                                                 | Obligatorisk                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Skicka – envägs-begäran       | Rot element.                                                                                               | Ja                             |
| url                        | URL för begäran.                                                                                     | Nej om läge = kopiera; annars Ja. |
| method                     | HTTP-metoden för begäran.                                                                            | Nej om läge = kopiera; annars Ja. |
| sidhuvud                     | Begär ande huvud. Använd flera huvud element för flera begärandehuvuden.                                  | Nej                              |
| Brödtext                       | Begär ande texten.                                                                                           | Nej                              |
| autentisering-certifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Nej                              |

### <a name="attributes"></a>Attribut

| Attribut     | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obligatorisk | Standard  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string" | Anger om det här är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initierar inte läge = kopiera begär ande texten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nej       | Ny      |
| name          | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Gäller inte      |
| exists-åtgärd | Anger vilken åtgärd som ska vidtas när rubriken redan har angetts. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet i den befintliga rubriken.<br />-Skip-ersätter inte det befintliga huvud-värdet.<br />-append – lägger till värdet i det befintliga huvud-värdet.<br />-Delete – tar bort rubriken från begäran.<br /><br /> När du `override` har angett flera poster med samma namn resulterar det i att rubriken anges enligt alla poster (som visas flera gånger). endast listade värden anges i resultatet. | Nej       | åsidosättningsinställning |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SendRequest"></a>Skicka begäran

`send-request` Principen skickar den angivna begäran till den angivna URL: en och väntar inte längre än värdet för Ange tids gräns.

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

| Element                    | Beskrivning                                                                                                 | Obligatorisk                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| skicka begäran               | Rot element.                                                                                               | Ja                             |
| url                        | URL för begäran.                                                                                     | Nej om läge = kopiera; annars Ja. |
| method                     | HTTP-metoden för begäran.                                                                            | Nej om läge = kopiera; annars Ja. |
| sidhuvud                     | Begär ande huvud. Använd flera huvud element för flera begärandehuvuden.                                  | Nej                              |
| Brödtext                       | Begär ande texten.                                                                                           | Nej                              |
| autentisering-certifikat | [Certifikat som ska användas för klientautentisering](api-management-authentication-policies.md#ClientCertificate) | Nej                              |

### <a name="attributes"></a>Attribut

| Attribut                       | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Obligatorisk | Standard  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="string"                   | Anger om det här är en ny begäran eller en kopia av den aktuella begäran. I utgående läge initierar inte läge = kopiera begär ande texten.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Nej       | Ny      |
| response-variable-name="string" | Namnet på den Sammanhangs variabel som ska ta emot ett svars objekt. Om variabeln inte finns skapas den när principen har körts och kommer att bli tillgänglig via [`context.Variable`](api-management-policy-expressions.md#ContextVariables) insamling.                                                                                                                                                                                                                                                                                                                          | Ja      | Gäller inte      |
| timeout="integer"               | Tids gräns intervallet i sekunder innan anropet till URL: en misslyckades.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Nej       | 60       |
| Ignorera-fel                    | Om värdet är true och begäran resulterar i ett fel:<br /><br /> -Om Response-Variable-Name har angetts innehåller det ett null-värde.<br />– Om Response-Variable-Name inte angavs, context. Begäran kommer inte att uppdateras.                                                                                                                                                                                                                                                                                                                                                                                   | Nej       | false    |
| name                            | Anger namnet på huvudet som ska ställas in.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ja      | Gäller inte      |
| exists-åtgärd                   | Anger vilken åtgärd som ska vidtas när rubriken redan har angetts. Det här attributet måste ha ett av följande värden.<br /><br /> -override-ersätter värdet i den befintliga rubriken.<br />-Skip-ersätter inte det befintliga huvud-värdet.<br />-append – lägger till värdet i det befintliga huvud-värdet.<br />-Delete – tar bort rubriken från begäran.<br /><br /> När du `override` har angett flera poster med samma namn resulterar det i att rubriken anges enligt alla poster (som visas flera gånger). endast listade värden anges i resultatet. | Nej       | åsidosättningsinställning |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SetHttpProxy"></a>Ange HTTP-proxy

Med `proxy` principen kan du dirigera begär Anden som vidarebefordras till Server delar via en http-proxy. Endast HTTP (inte HTTPS) stöds mellan gatewayen och proxyn. Endast grundläggande och NTLM-autentisering.

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

| Element | Beskrivning  | Obligatorisk |
| ------- | ------------ | -------- |
| Proxy   | Rot element | Ja      |

### <a name="attributes"></a>Attribut

| Attribut         | Beskrivning                                            | Obligatorisk | Standard |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | Proxy-URL i form av http://host:port.             | Ja      | Gäller inte     |
| username="string" | Användar namn som ska användas för autentisering med proxyn. | Nej       | Gäller inte     |
| Password = "sträng" | Lösen ord som ska användas för autentisering med proxyservern. | Nej       | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

## <a name="SetRequestMethod"></a>Ange metod för begäran

Med `set-method` principen kan du ändra metoden för http-begäran för en begäran.

### <a name="policy-statement"></a>Princip kommentar

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Exempel

I den här exempel principen som `set-method` använder principen visas ett exempel på hur du skickar ett meddelande till ett slack chatt-rum om http-svars koden är större än eller lika med 500. Mer information om det här exemplet finns i [använda externa tjänster från Azure API Management-tjänsten](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

| Element    | Beskrivning                                                       | Obligatorisk |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-metod | Rot element. Värdet för elementet anger HTTP-metoden. | Ja      |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="SetStatus"></a>Ange status kod

`set-status` Principen anger HTTP-statuskoden till det angivna värdet.

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

| Element    | Beskrivning   | Obligatorisk |
| ---------- | ------------- | -------- |
| set-status | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut       | Beskrivning                                                | Obligatorisk | Standard |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="integer"  | Den HTTP-statuskod som ska returneras.                            | Ja      | Gäller inte     |
| Orsak = "sträng" | En beskrivning av orsaken till att status koden returneras. | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** utgående, Server del, på-fel
-   **Princip omfattningar:** alla omfattningar

## <a name="set-variable"></a>Ange variabel

Principen deklarerar en [kontext](api-management-policy-expressions.md#ContextVariables) variabel och tilldelar den ett värde som anges via ett uttryck eller en tecken sträng. [](api-management-policy-expressions.md) `set-variable` om uttrycket innehåller en literal kommer det att konverteras till en sträng och typen av värde blir `System.String`.

### <a name="set-variablePolicyStatement"></a>Princip kommentar

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="set-variableExample"></a>Exempel

Följande exempel visar en uppsättning variabel princip i avsnittet inkommande. Den här uppsättningen variabel principen skapar `isMobile` en boolesk [Sammanhangs](api-management-policy-expressions.md#ContextVariables) variabel som är inställd `User-Agent` på sant om begär ande rubriken innehåller texten `iPhone` `iPad` eller.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Element

| Element      | Beskrivning   | Obligatorisk |
| ------------ | ------------- | -------- |
| Set-Variable | Rot element. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                              | Obligatorisk |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Namnet på variabeln.                                                | Ja      |
| value     | Variabelns värde. Detta kan vara ett uttryck eller ett tecken värde. | Ja      |

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

`trace` Principen lägger till en anpassad spårning i API-kontrollens utdata, Application Insights telemetrivärden och/eller diagnostikloggar. 

* Principen lägger till en anpassad spårning i [API-kontrollens](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) utdata när spårning utlöses, d.v.s. `Ocp-Apim-Trace` begär ande huvudet finns och är inställt på True och `Ocp-Apim-Subscription-Key` begär ande huvudet finns och innehåller en giltig nyckel som tillåter spårning. 
* Principen skapar en [trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) -telemetri i Application Insights när [Application Insights integration](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) är `severity` aktive rad och nivån som anges i principen är på eller högre än den `verbosity` nivå som anges i diagnostiken inställningen. 
* Principen lägger till en egenskap i logg posten när [diagnostikloggar](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) är aktive rad och allvarlighets graden som anges i principen är på eller högre än den detaljerade nivån som anges i den diagnostiska inställningen.  


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

| Element | Beskrivning   | Obligatorisk |
| ------- | ------------- | -------- |
| Rita   | Rot element. | Ja      |
| message | En sträng eller ett uttryck som ska loggas. | Ja |
| metadata | Lägger till en anpassad egenskap i Application Insights [trace](https://docs.microsoft.com/en-us/azure/azure-monitor/app/data-model-trace-telemetry) -telemetri. | Nej |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                             | Obligatorisk | Standard |
| --------- | --------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Tecken strängen är meningsfull för spårnings visaren och anger källan till meddelandet. | Ja      | Gäller inte     |
| allvarlighetsgrad    | Anger spårningens allvarlighets nivå. Tillåtna värden är `verbose`, `information`, `error` (från lägsta till högsta). | Nej      | Utförlig     |
| name    | Egenskapens namn. | Ja      | Gäller inte     |
| value    | Egenskapens värde. | Ja      | Gäller inte     |

### <a name="usage"></a>Användning

Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Princip avsnitt:** inkommande, utgående, Server del, på-fel

-   **Princip omfattningar:** alla omfattningar

## <a name="Wait"></a>Vänta

`wait` Principen kör omedelbart underordnade principer parallellt och väntar på att alla eller en av dess omedelbara underordnade principer ska slutföras innan den slutförs. Wait-principen kan ha samma omedelbara underordnade principer som [skicka begäran](api-management-advanced-policies.md#SendRequest), [Hämta värde från cache](api-management-caching-policies.md#GetFromCacheByKey)och [kontrol lera flödes](api-management-advanced-policies.md#choose) principer.

### <a name="policy-statement"></a>Princip kommentar

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Exempel

I följande exempel finns det två `choose` principer som principer `wait` för omedelbara underordnade principer. Var och en `choose` av dessa principer körs parallellt. Varje `choose` princip försöker hämta ett cachelagrat värde. Om det finns ett cacheminne anropas en backend-tjänst för att ange värdet. I det här exemplet `wait` slutförs inte principen förrän alla dess omedelbara underordnade principer har slutförts, `for` eftersom attributet är inställt på `all`. I det här exemplet deklareras kontextnodens`execute-branch-one`( `value-one` `execute-branch-two`,, och `value-two`) utanför omfånget för den här exempel principen.

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

| Element | Beskrivning                                                                                                   | Obligatorisk |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Vänta    | Rot element. Får bara `send-request`innehålla underordnade element, `cache-lookup-value`och `choose` -principer. | Ja      |

### <a name="attributes"></a>Attribut

| Attribut | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                            | Obligatorisk | Standard |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| för       | Bestämmer om `wait` principen väntar på att alla omedelbara underordnade principer ska slutföras eller bara en. Tillåtna värden är:<br /><br /> - `all`– vänta tills alla direkta underordnade principer har slutförts<br />– vänta tills en omedelbar underordnad princip har slutförts. När den första omedelbara underordnade principen har slutförts `wait` avslutas principen och körningen av andra omedelbara underordnade principer avbryts. | Nej       | all     |

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
