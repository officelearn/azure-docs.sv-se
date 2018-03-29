---
title: Principer för anspråksomvandling till Azure API Management | Microsoft Docs
description: Mer information om principer för anspråksomvandling tillgängligt för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 3eb9d6851c30f11980d47d4e48b158217e41995d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="api-management-transformation-policies"></a>API Management-principer för anspråksomvandling
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Principer för anspråksomvandling

-   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begäran eller svar body från JSON till XML.

-   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begäran eller svar body från XML till JSON.

-   [Sök och Ersätt strängen i brödtexten](api-management-transformation-policies.md#Findandreplacestringinbody) - söker efter en begäran eller ett svar delsträng och ersätter den med en annan understräng.

-   [Maskera URL: er i innehåll](api-management-transformation-policies.md#MaskURLSContent) -skriver (masker) länkar i svaret body så att de pekar på motsvarande länk via gatewayen.

-   [Ange serverdelstjänst](api-management-transformation-policies.md#SetBackendService) -ändras serverdelstjänst för en inkommande begäran.

-   [Konfigurera brödtext](api-management-transformation-policies.md#SetBody) -anger meddelandetexten för inkommande och utgående förfrågningar.

-   [Ange HTTP-huvudet](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till en befintlig svar och/eller huvudet i begäran eller lägger till nya svar och/eller begäran-huvud.

-   [Ange frågesträngparametern](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet eller tar bort frågesträngparametern för begäran.

-   [Skriv om URL: en](api-management-transformation-policies.md#RewriteURL) -konverterar en begärd URL från dess offentliga form i formuläret som förväntades av webbtjänsten.

-   [Transformera XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) -tillämpar en XSL-transformation på XML i begäran eller svar.

##  <a name="ConvertJSONtoXML"></a> Konvertera JSON till XML
 Den `json-to-xml` princip konverterar brödtext begäran eller ett svar från JSON till XML.

### <a name="policy-statement"></a>Principframställning

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|json-to-xml|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|använd|Attributet måste anges till ett av följande värden.<br /><br /> -alltid - alltid gäller konvertering.<br />-innehåll typ-json - Konvertera endast om response Content-Type-huvud anger förekomsten av JSON.|Ja|Gäller inte|
|consider-accept-header|Attributet måste anges till ett av följande värden.<br /><br /> tillämpa - true - konvertering om JSON begärs i begäran Accept-huvud.<br />-alltid gäller false - konvertering.|Nej|true|
|parse-date|Om värdet är `false` datumvärden kopieras bara under omvandling|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående, vid fel

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="ConvertXMLtoJSON"></a> Konvertera XML till JSON
 Den `xml-to-json` princip konverterar brödtext begäran eller ett svar från XML till JSON. Den här principen kan användas för att modernisera API: er baserat på backend endast XML-webbtjänster.

### <a name="policy-statement"></a>Principframställning

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|xml-to-json|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|typ|Attributet måste anges till ett av följande värden.<br /><br /> javascript-anpassad - konverterade JSON har ett eget JavaScript-utvecklare formulär.<br />-direkt - visar konverterade JSON det ursprungliga XML-dokumentets struktur.|Ja|Gäller inte|
|använd|Attributet måste anges till ett av följande värden.<br /><br /> -alltid - konvertera alltid.<br />-innehåll-typ-xml - Konvertera endast om response Content-Type-huvud anger förekomsten av XML.|Ja|Gäller inte|
|consider-accept-header|Attributet måste anges till ett av följande värden.<br /><br /> tillämpa - true - konvertering om begärs XML i begäran Accept-huvud.<br />-alltid gäller false - konvertering.|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående, vid fel

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="Findandreplacestringinbody"></a> Sök och Ersätt strängen i brödtext
 Den `find-and-replace` princip söker efter en begäran eller ett svar delsträng och ersätter den med en annan understräng.

### <a name="policy-statement"></a>Principframställning

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Exempel

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Sök och Ersätt|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|från|Sträng att söka efter.|Ja|Gäller inte|
|till|Ersättningssträngen. Ange ersättning nollängd för att ta bort söksträngen.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående backend fel

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="MaskURLSContent"></a> Mask-URL: er i innehåll
 Den `redirect-content-urls` princip skriver (masker) länkar i svarstexten så att de pekar på motsvarande länk via gatewayen. Använda i avsnittet utgående för att Skriv svaret brödtext länkar så att de pekar på gatewayen. Använd i avsnittet inkommande för en motsatt effekt.

> [!NOTE]
>  Den här principen ändras inte alla värden i huvudet som `Location` huvuden. Du kan ändra värden i huvudet i [set-huvudet](api-management-transformation-policies.md#SetHTTPheader) princip.

### <a name="policy-statement"></a>Principframställning

```xml
<redirect-content-urls />
```

### <a name="example"></a>Exempel

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|redirect-content-urls|Rotelementet.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="SetBackendService"></a> Ange backend-tjänst
 Använd den `set-backend-service` för att omdirigera en inkommande begäran till en annan serverdelen än den som angetts i API-inställningarna för den åtgärden. Den här principen ändras backend service bas-URL till den inkommande begäranden till den som anges i principen.

### <a name="policy-statement"></a>Principframställning

```xml
<set-backend-service base-url="base URL of the backend service" />
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
I det här exemplet skickar ange backend service princip förfrågningar baserat på versionsvärdet frågesträngen som skickas till en annan serverdelstjänst än den som anges i Programmeringsgränssnittet.

Den grundläggande Webbadressen för backend-tjänsten är ursprungligen härleds från API-inställningarna. Så den begärda Webbadressen `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` blir `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` där `http://contoso.com/api/10.4/` är backend-tjänst-URL som anges i inställningarna för API.

När den [< Välj\> ](api-management-advanced-policies.md#choose) Principframställning tillämpas den grundläggande Webbadressen för backend-tjänst kan ändra igen antingen `http://contoso.com/api/8.2` eller `http://contoso.com/api/9.1`, beroende på värdet på Frågeparametern version begäran. Om värdet är till exempel `"2013-15"` sista begäran URL blir `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Om ytterligare transformering av begäran är önskade, andra [principer för Anspråksomvandling](api-management-transformation-policies.md#TransformationPolicies) kan användas. Till exempel för att ta bort Frågeparametern versionen nu att begäran omdirigeras till en specifik version-serverdelen av [ange frågesträngparametern](api-management-transformation-policies.md#SetQueryStringParameter) princip kan användas för att ta bort Versionsattributet nu redundant.

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
I det här exemplet dirigerar principen begäran till en service fabric serverdel använder frågesträngen användar-ID som partitionsnyckel och använder den primära repliken av partitionen.

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-backend-service|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|bas-url|Nya backend service bas-URL.|Nej|Gäller inte|
|backend-id|Identifierare för att dirigera till serverdelen.|Nej|Gäller inte|
|sf-partition-key|Gäller endast när serverdelen är en tjänst för Service Fabric och anges med backend-id. Används för att lösa en specifik partition från Namnmatchningstjänsten.|Nej|Gäller inte|
|sf-replica-type|Gäller endast när serverdelen är en tjänst för Service Fabric och anges med backend-id. Styr om begäran ska skickas till primär eller sekundär replik av en partition. |Nej|Gäller inte|
|sf-resolve-condition|Gäller endast när serverdelen är en tjänst för Service Fabric. Identifiera om anropet till Service Fabric-serverdelen har upprepas med nya lösningar-villkor.|Nej|Gäller inte|
|sf-service-instance-name|Gäller endast när serverdelen är en tjänst för Service Fabric. Du kan ändra instanser av tjänsten vid körning. |Nej|Gäller inte|
|sf-listener-name|Gäller endast när serverdelen är en tjänst för Service Fabric och anges med backend-id. Service Fabric Reliable Services kan du skapa flera lyssnare i en tjänst. Det här attributet används för att välja en viss lyssnare när en serverdel tillförlitlig tjänst har mer än en lyssnare. Om det här attributet inte anges försöker API Management använda en lyssnare utan namn. En lyssnare utan namn är typiskt för tillförlitlig tjänsterna som har endast en lyssnare. |Nej|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, backend

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="SetBody"></a> Ange brödtext
 Använd den `set-body` Grupprincip för att ange brödtext för inkommande och utgående förfrågningar. Åtkomst till meddelandetexten som du kan använda den `context.Request.Body` egenskapen eller `context.Response.Body`, beroende på om principen finns i avsnittet inkommande eller utgående.

> [!IMPORTANT]
>  Observera att som standard när du använder meddelandet body med `context.Request.Body` eller `context.Response.Body`, den ursprungliga meddelandetexten försvinner och måste anges genom att returnera innehållet i uttrycket. Om du vill bevara brödtext innehållet, ange den `preserveContent` parameter till `true` vid åtkomst av meddelandet. Om `preserveContent` är inställd på `true` och en annan text returnerades av uttrycket returnerade brödtexten används.
>
>  Observera följande när du använder den `set-body` princip.
>
>  -   Om du använder den `set-body` princip för att returnera en ny eller uppdaterad text som du inte behöver ange `preserveContent` till `true` eftersom tillhandahåller du uttryckligen det nya innehållet i brödtexten.
> -   Bevara innehållet i ett svar i pipeline för inkommande passar inte eftersom det inte finns något svar ännu.
> -   Bevara innehållet i en begäran i pipeline för utgående vara inte meningsfullt eftersom begäran har redan skickats till serverdelen nu.
> -   Om den här principen används när det finns ingen brödtext, genereras till exempel i en inkommande GET ett undantag.

 Mer information finns i `context.Request.Body`, `context.Response.Body`, och `IMessage` avsnitten i den [kontexten variabeln](api-management-policy-expressions.md#ContextVariables) tabell.

### <a name="policy-statement"></a>Principframställning

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exempel

#### <a name="literal-text-example"></a>Citat-exempel

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exempel åtkomst till innehållet som en sträng. Observera att vi bevarar den ursprungliga begärandetexten så att vi kan komma åt den senare i pipelinen.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exempel åtkomst till innehållet som en JObject. Observera att eftersom vi inte reserverar ursprungliga begärandetexten, öppnar den senare i pipeline resulterar i ett undantag.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Filtrera respons baserat på produkt
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

### <a name="using-liquid-templates-with-set-body"></a>Med hjälp av flytande mallar med set-brödtext
Den `set-body` principen kan konfigureras för att använda den [flytande](https://shopify.github.io/liquid/basics/introduction/) templating språk till transfom brödtexten i en begäran eller ett svar. Detta kan vara mycket effektivt om du behöver helt Omforma formatet för meddelandet.

> [!IMPORTANT]
> Implementeringen av flytande används i den `set-body` principen är konfigurerad i 'C# mode'. Detta är särskilt viktigt när du gör saker, till exempel filtrering. Exempel med hjälp av ett filter kräver användning av Pascal skiftläge och C# datum formatering t.ex.:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> För att kunna bindas till en XML-meddelandetext med mallen flytande, använda en `set-header` Grupprincip för att ange Content-Type antingen application/xml, text/xml (eller någon typ som slutar med + xml) till, en JSON-meddelandetext, måste det vara application/json, text/json (eller någon typ som slutar med + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konvertera JSON till SOAP med en flytande mall
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="tranform-json-using-a-liquid-template"></a>Tranform JSON med en flytande mall
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|Ange brödtext|Rotelementet. Innehåller brödtexten eller ett uttryck som returnerar en brödtext.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|mall|Används för att ändra läget för templating som ange brödtext princip kommer att köras. För närvarande är det enda värdet som stöds:<br /><br />-flytande - ange brödtext princip kommer att använda flytande templating motorn |Nej|flytande|

För att komma åt information om begäran och svar, kan flytande mallen bindas till ett kontextobjekt med följande egenskaper: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående backend

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="SetHTTPheader"></a> Ange HTTP-huvud
 Den `set-header` princip tilldelar ett värde till en befintlig svar och/eller huvudet i begäran eller lägger till nya svar och/eller begäran-huvud.

 Infogar en lista över HTTP-huvuden i ett HTTP-meddelande. När den placeras i en inkommande pipeline i den här principen anger HTTP-huvuden för begäran som skickas till Måltjänsten. När den placeras i en utgående pipeline i den här principen anger HTTP-huvuden för svar som skickas till en gateway-klienten.

### <a name="policy-statement"></a>Principframställning

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontextinformation till serverdelstjänsten
 Det här exemplet visar hur du tillämpa principen på API-nivå för att leverera sammanhangsinformation till backend-tjänsten. En demonstration av hur du konfigurerar och använder den här principen finns [moln omfattar avsnitt 177: mer API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 10:30. Det finns en demonstration av anropa en funktion i developer-portalen där du kan se principen på arbetet vid 12:10.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Mer information finns i [principuttrycken](api-management-policy-expressions.md) och [kontexten variabeln](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-header|Rotelementet.|Ja|
|värde|Anger värdet för huvudet anges. För flera huvuden med samma namn Lägg till ytterligare `value` element.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|Det finns åtgärd|Anger vilken åtgärd som ska vidtas när huvudet har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintliga-huvud.<br />-skip - ersätter inte det befintliga huvudvärdet.<br />-Tillägg - lägger till värdet på det befintliga huvudvärdet.<br />-delete - tar bort huvudet i begäran.<br /><br /> Om värdet är `override` ta med flera poster med samma namn resulterar i sidhuvudet har angetts enligt alla poster (som visas flera gånger); endast listade värden anges i resultatet.|Nej|åsidosätt|
|namn|Anger namnet på huvudet som ska anges.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående backend fel

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="SetQueryStringParameter"></a> Ange frågesträngparametern
 Den `set-query-parameter` principen läggs till, ersätter värde, eller tar bort begära frågesträngparametern. Kan användas för att skicka fråga parametrar förväntades av backend-tjänsten som är valfria eller aldrig i begäran.

### <a name="policy-statement"></a>Principframställning

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Exempel

#### <a name="example"></a>Exempel

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontextinformation till serverdelstjänsten
 Det här exemplet visar hur du tillämpa principen på API-nivå för att leverera sammanhangsinformation till backend-tjänsten. En demonstration av hur du konfigurerar och använder den här principen finns [moln omfattar avsnitt 177: mer API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 10:30. Det finns en demonstration av anropa en funktion i developer-portalen där du kan se principen på arbetet vid 12:10.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Mer information finns i [principuttrycken](api-management-policy-expressions.md) och [kontexten variabeln](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-query-parameter|Rotelementet.|Ja|
|värde|Anger värdet för Frågeparametern anges. För flera frågeparametrar med samma namn Lägg till ytterligare `value` element.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|Det finns åtgärd|Anger vilken åtgärd som ska vidtas när Frågeparametern har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter befintliga parameterns värde.<br />-skip - ersätter inte befintliga parametervärdet för frågan.<br />-Tillägg - lägger till värdet till det befintliga frågeparametervärdet.<br />-delete - tar bort Frågeparametern från begäran.<br /><br /> Om värdet är `override` ta med flera poster med samma namn resulterar i Frågeparametern anges enligt alla poster (som visas flera gånger); endast listade värden anges i resultatet.|Nej|åsidosätt|
|namn|Anger namnet på Frågeparametern anges.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, backend

-   **Princip för scope:** globala, produkt, API, åtgärden

##  <a name="RewriteURL"></a> Omarbetning URL
 Den `rewrite-uri` princip konverterar en begärd URL från dess offentliga form i formuläret som förväntades av webbtjänsten som visas i följande exempel.

-   Offentlig URL- `http://api.example.com/storenumber/ordernumber`

-   URL-begäran- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Den här principen kan användas när en mänsklig och/eller webbläsare eget URL bör omvandlas till URL-formatet som förväntas av webbtjänsten. Den här principen behöver bara tillämpas när exponera en alternativ URL-format, till exempel ren URL: er, RESTful-URL: er, användarvänliga URL: er eller SEO eget URL: er som är enbart strukturella URL: er som inte innehåller en frågesträng och i stället innehåller endast sökvägen till resursen ( efter schemat och behörighet). Detta sker ofta för dess estetiska egenskaper, användbarhet eller sökmotor optimeringssyfte (SEO).

> [!NOTE]
>  Du kan bara lägga till frågan string-parametrar med principer. Du kan inte lägga till extra sökväg i mallparametrarna i URL: en för omarbetning.

### <a name="policy-statement"></a>Principframställning

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Exempel

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|omarbetning-uri|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|mall|Den faktiska webbtjänst-URL med någon fråga string-parametrar. När du använder uttryck måste hela värdet vara ett uttryck.|Ja|Gäller inte|
|copy-unmatched-params|Anger om Frågeparametrar i inkommande begäran finns inte i den ursprungliga mallen URL läggs till den URL som definieras i Skriv mallen|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande

-   **Princip för scope:** produkt, API, åtgärden

##  <a name="XSLTransform"></a> Transformera XML med hjälp av en XSLT-fil
 Den `Transform XML using an XSLT` principen gäller en XSL-transformation på XML i begäran eller svar.

### <a name="policy-statement"></a>Principframställning

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Exempel

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|xsl-transform|Rotelementet.|Ja|
|Parameter|Används för att definiera variabler som används i listan i transformeringen|Nej|
|XSL: stylesheet|Formatmallen rotelementet. Alla element och attribut som definierats inom följer standarden [XSLT-specifikationen](http://www.w3.org/TR/xslt)|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Avsnitt i princip:** inkommande, utgående

-   **Princip för scope:** globala, produkt, API, åtgärden

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande avsnitt:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)
