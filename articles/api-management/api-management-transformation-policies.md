---
title: Omvandlingsprinciper för Azure API Management | Microsoft-dokument
description: Lär dig mer om de omvandlingsprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 34a70a4698b69881a06cfb7a7017fa0c30647197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047684"
---
# <a name="api-management-transformation-policies"></a>Omvandlingsprinciper för API Management
Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Omvandlingsprinciper

-   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Konverterar begäran eller svarstext från JSON till XML.

-   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Konverterar begäran eller svarstext från XML till JSON.

-   [Söka efter och ersätta sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) - Söker efter en understräng för begäran eller svar och ersätter den med en annan delsträng.

-   [Mask webbadresser i innehåll](api-management-transformation-policies.md#MaskURLSContent) - Omskrivningar (masker) länkar i svarstexten så att de pekar på motsvarande länk via gatewayen.

-   [Ange serverdservice](api-management-transformation-policies.md#SetBackendService) - Ändrar serverdatjänsten för en inkommande begäran.

-   [Ange brödtext](api-management-transformation-policies.md#SetBody) - Anger meddelandetexten för inkommande och utgående begäranden.

-   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) - Tilldelar ett värde till ett befintligt svars- och/eller begärandehuvud eller lägger till ett nytt svar och/eller begäranden.

-   [Ange frågesträngparameter](api-management-transformation-policies.md#SetQueryStringParameter) - Lägger till, ersätter värdet för eller tar bort frågesträngparametern för begäran.

-   [Skriv om URL](api-management-transformation-policies.md#RewriteURL) - Konverterar en URL för begäran från dess offentliga formulär till det formulär som förväntas av webbtjänsten.

-   [Omforma XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) - Tillämpar en XSL-omvandling på XML i begäran eller svarstexten.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>Konvertera JSON till XML
 Principen `json-to-xml` konverterar en begäran eller svarstext från JSON till XML.

### <a name="policy-statement"></a>Policyuttalande

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
|json-till-xml|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|apply|Attributet måste anges till ett av följande värden.<br /><br /> - alltid - alltid tillämpa konvertering.<br />- content-type-json - konvertera endast om svaret Content-Type header indikerar närvaro av JSON.|Ja|Ej tillämpligt|
|överväga-acceptera-huvud|Attributet måste anges till ett av följande värden.<br /><br /> - sant - tillämpa konvertering om XML begärs i begäran Acceptera huvudet.<br />- falskt - alltid tillämpa konvertering.|Inga|true|
|tolkningsdatum|När inställda till `false` datumvärden helt enkelt kopieras under omvandling|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, på fel

-   **Principomfattningar:** alla scope

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>Konvertera XML till JSON
 Principen `xml-to-json` konverterar en begäran eller svarstext från XML till JSON. Den här principen kan användas för att modernisera API:er baserat på xml-backend-webbtjänster.

### <a name="policy-statement"></a>Policyuttalande

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
|xml-till-json|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|Typ|Attributet måste anges till ett av följande värden.<br /><br /> - Javascript-vänlig - den konverterade JSON har en form vänlig till JavaScript utvecklare.<br />- direkt - den konverterade JSON återspeglar det ursprungliga XML-dokumentets struktur.|Ja|Ej tillämpligt|
|apply|Attributet måste anges till ett av följande värden.<br /><br /> - alltid - konvertera alltid.<br />- content-type-xml - konvertera endast om svar Content-Type header indikerar förekomst av XML.|Ja|Ej tillämpligt|
|överväga-acceptera-huvud|Attributet måste anges till ett av följande värden.<br /><br /> - sant - tillämpa konvertering om JSON begärs i begäran Acceptera huvudet.<br />- falskt - alltid tillämpa konvertering.|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, på fel

-   **Principomfattningar:** alla scope

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Hitta och ersätta sträng i kroppen
 Principen `find-and-replace` hittar en understräng för begäran eller svar och ersätter den med en annan delsträng.

### <a name="policy-statement"></a>Policyuttalande

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
|hitta och ersätta|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|Från|Strängen att söka efter.|Ja|Ej tillämpligt|
|till|Ersättningssträngen. Ange en ersättningssträng med noll längd för att ta bort söksträngen.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>Mask-URL:er i innehåll
 Principen `redirect-content-urls` omskrivningar (masker) länkar i svarstexten så att de pekar på motsvarande länk via gatewayen. Använd i det utgående avsnittet för att skriva om svarstextlänkar för att få dem att peka på gatewayen. Använd i det inkommande avsnittet för en motsatt effekt.

> [!NOTE]
>  Den här principen ändrar inte `Location` några rubrikvärden, till exempel rubriker. Om du vill ändra rubrikvärden använder du principen [set-header.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Policyuttalande

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
|omdirigera-innehåll-url:ar|Rotelementet.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, utgående

-   **Principomfattningar:** alla scope

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Ange backend-tjänst
 Använd `set-backend-service` principen för att omdirigera en inkommande begäran till en annan serverdel än den som anges i API-inställningarna för den åtgärden. Den här principen ändrar serverdelstjänstens bas-URL för den inkommande begäran till den som anges i principen.

### <a name="policy-statement"></a>Policyuttalande

```xml
<set-backend-service base-url="base URL of the backend service" />
```

eller

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Serveringsenheter kan hanteras via [hanterings-API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

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
I det här exemplet dirigerar principbegäranden för princip för serverdelstjänstbegäranden baserat på versionsvärdet som skickas i frågesträngen till en annan serverdelstjänst än den som anges i API:et.

Ursprungligen härleds serverdelstjänstens bas-URL från API-inställningarna. Så begäran `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` URL `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` blir där är serversidan tjänsten URL anges i API-inställningarna.

När [<väljer\> ](api-management-advanced-policies.md#choose) principsats tillämpas kan url:en för backend-tjänstbasen ändras igen antingen till `http://contoso.com/api/8.2` eller `http://contoso.com/api/9.1`, beroende på värdet för frågeparametern för versionsbegäran. Om värdet till exempel `"2013-15"` är den `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`slutliga url:en för begäran blir .

Om ytterligare omvandling av begäran önskas kan andra [omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies) användas. Om du till exempel vill ta bort parametern versionfråga nu när begäran dirigeras till en versionsspecifik backend kan parameterprincipen [Ange frågesträng](api-management-transformation-policies.md#SetQueryStringParameter) användas för att ta bort det nu redundanta versionsattributet.

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
I det här exemplet dirigerar principen begäran till en service fabric-backend, med hjälp av userId-frågesträngen som partitionsnyckel och med hjälp av den primära repliken för partitionen.

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-backend-service|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|bas-url|Ny url för backend-tjänstbasen.|En `base-url` av `backend-id` eller måste vara närvarande.|Ej tillämpligt|
|backend-id|Identifierare för backend att dirigera till. (Serveringsenheter hanteras via [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|En `base-url` av `backend-id` eller måste vara närvarande.|Ej tillämpligt|
|sf-partition-nyckel|Gäller endast när backend är en Service Fabric-tjänst och anges med hjälp av "backend-id". Används för att lösa en viss partition från namnmatchningstjänsten.|Inga|Ej tillämpligt|
|sf-repliktyp|Gäller endast när backend är en Service Fabric-tjänst och anges med hjälp av "backend-id". Kontrollerar om begäran ska gå till den primära eller sekundära repliken för en partition. |Inga|Ej tillämpligt|
|sf-resolve-villkor|Gäller endast när backend är en Service Fabric-tjänst. Villkor som identifierar om anropet till Service Fabric-backend måste upprepas med ny upplösning.|Inga|Ej tillämpligt|
|sf-tjänst-instans-namn|Gäller endast när backend är en Service Fabric-tjänst. Gör det möjligt att ändra tjänstinstanser vid körning. |Inga|Ej tillämpligt|
|sf-lyssnaren-namn|Gäller endast när backend är en Service Fabric-tjänst och anges med hjälp av "backend-id". Service Fabric Reliable Services gör att du kan skapa flera lyssnare i en tjänst. Det här attributet används för att välja en specifik lyssnare när en tillförlitlig tjänst för backend har mer än en lyssnare. Om det här attributet inte anges försöker API Management använda en lyssnare utan namn. En lyssnare utan namn är typisk för Tillförlitliga tjänster som bara har en lyssnare. |Inga|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, bakåtsträvande

-   **Principomfattningar:** alla scope

##  <a name="set-body"></a><a name="SetBody"></a>Ställ in brödtext
 Använd `set-body` principen för att ange meddelandetexten för inkommande och utgående begäranden. För att komma åt meddelandetexten `context.Response.Body`kan du använda egenskapen `context.Request.Body` eller , beroende på om principen finns i det inkommande eller utgående avsnittet.

> [!IMPORTANT]
>  Observera att som standard när du `context.Request.Body` `context.Response.Body`öppnar meddelandetexten med eller försvinner den ursprungliga meddelandetexten och måste ställas in genom att brödtexten returneras i uttrycket. Om du vill bevara `preserveContent` brödtextinnehållet ställer du in parametern på `true` när du öppnar meddelandet. Om `preserveContent` är `true` inställd på och en annan kropp returneras av uttrycket, används den returnerade kroppen.
>
>  Observera följande överväganden när du `set-body` använder policyn.
>
> - Om du använder `set-body` principen för att returnera en ny eller `preserveContent` uppdaterad `true` brödtext behöver du inte ange eftersom du uttryckligen tillhandahåller det nya brödtextinnehållet.
>   -   Det är inte meningsfullt att bevara innehållet i ett svar i den inkommande pipelinen eftersom det inte finns något svar ännu.
>   -   Det är inte meningsfullt att bevara innehållet i en begäran i den utgående pipelinen eftersom begäran redan har skickats till serverposten just nu.
>   -   Om den här principen används när det inte finns något meddelande, till exempel i en inkommande GET, genereras ett undantag.

 Mer information finns `context.Request.Body`i `context.Response.Body`tabellen `IMessage` , och avsnitten i variabeln [Kontextvariabel.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Policyuttalande

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exempel

#### <a name="literal-text-example"></a>Exempel på bokstavlig text

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Exempel på åtkomst till brödtexten som en sträng. Observera att vi bevarar den ursprungliga begäran kroppen så att vi kan komma åt den senare i pipeline.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Exempel på åtkomst till kroppen som en JObject. Observera att eftersom vi inte reserverar den ursprungliga begärandetexten, kommer åtkomst till det senare i pipelinen att resultera i ett undantag.

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

#### <a name="filter-response-based-on-product"></a>Filterrespons baserat på produkt
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

### <a name="using-liquid-templates-with-set-body"></a>Använda flytande mallar med uppsättningskropp
Principen `set-body` kan konfigureras för att använda språket [Flytande](https://shopify.github.io/liquid/basics/introduction/) templating för att omvandla brödtexten för en begäran eller ett svar. Detta kan vara mycket effektivt om du behöver omforma formatet på meddelandet helt och hållet.

> [!IMPORTANT]
> Implementeringen av vätska `set-body` som används i principen konfigureras i "C#-läge". Detta är särskilt viktigt när du gör saker som filtrering. Som ett exempel kräver användning av Pascal-hölje och C#-datumformatering, t.ex.
>
> {{body.foo.startDateTime| Datum:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Om du vill binda till en XML-brödtext `set-header` korrekt med mallen Liquid använder du en princip för att ange content-type till antingen program/xml, text/xml (eller någon typ som slutar med +xml). För en JSON-kropp måste det vara ansökan/json, text/json (eller någon typ som slutar med +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Konvertera JSON till SOAP med hjälp av en flytande mall
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

#### <a name="transform-json-using-a-liquid-template"></a>Omvandla JSON med hjälp av en flytande mall
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
|set-body set-body set-body set-|Rotelementet. Innehåller brödtexten eller ett uttryck som returnerar en brödtext.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|mall|Används för att ändra det templating-läge som den inställda brödtextprincipen ska köras i. För närvarande är det enda värde som stöds:<br /><br />- vätska - den inställda kroppen politik kommer att använda flytande templating motorn |Inga||

Om du vill komma åt information om begäran och svar kan mallen Flytande binda till ett kontextobjekt med följande egenskaper: <br />
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
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, utgående, bakåtsträvande

-   **Principomfattningar:** alla scope

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>Ange HTTP-huvud
 Principen `set-header` tilldelar ett värde till ett befintligt svars- och/eller begärandehuvud eller lägger till ett nytt svar och/eller begäranden.

 Infogar en lista med HTTP-huvuden i ett HTTP-meddelande. När den här principen placeras i en inkommande pipeline anger den här principen HTTP-huvuden för den begäran som skickas till måltjänsten. När den här principen placeras i en utgående pipeline anger den här principen HTTP-huvuden för svaret som skickas till gatewayens klient.

### <a name="policy-statement"></a>Policyuttalande

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Exempel

#### <a name="example---adding-header-override-existing"></a>Exempel - lägga till rubrik, åsidosätt befintliga

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Exempel - ta bort sidhuvud

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontextinformation till backend-tjänsten
 Det här exemplet visar hur du tillämpar principen på API-nivå för att ange kontextinformation till serverdtjänsten. En demonstration av att konfigurera och använda den här principen finns i [Cloud Cover Episode 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och snabbspola fram till 10:30. Klockan 12:10 finns en demonstration av att ringa en åtgärd i utvecklarportalen där du kan se policyn på jobbet.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Mer information finns i [Principuttryck](api-management-policy-expressions.md) och [kontextvariabel](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Flera värden i ett huvud sammanfogas till en CSV-sträng, till exempel:`headerName: value1,value2,value3`
>
> Undantag inkluderar standardiserade rubriker, vilka värden:
> - kan innehålla kommatecken `WWW-Authenticate` `Proxy-Authenticate`(`User-Agent`, , ),
> - kan innehålla`Cookie`datum `Set-Cookie` `Warning`( , , ),
> - innehåller datum`Date` `Expires`( `If-Modified-Since` `If-Unmodified-Since`, `Last-Modified` `Retry-After`, , , , , ).
>
> Vid dessa undantag sammanfogas inte flera rubrikvärden till en sträng och skickas som separata rubriker, till exempel:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-header|Rotelementet.|Ja|
|värde|Anger värdet för huvudet som ska ställas in. För flera rubriker med samma `value` namn lägg till ytterligare element.|Inga|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|finns-åtgärd|Anger vilken åtgärd som ska vidtas när huvudet redan har angetts. Det här attributet måste ha något av följande värden.<br /><br /> - åsidosättning - ersätter värdet för det befintliga huvudet.<br />- hoppa över - ersätter inte det befintliga huvudvärdet.<br />- lägg till - lägger till värdet i det befintliga huvudvärdet.<br />- ta bort - tar bort huvudet från begäran.<br /><br /> När den `override` är inställd på att värva flera poster med samma namn resulterar i att huvudet ställs in enligt alla poster (som kommer att visas flera gånger); endast angivna värden kommer att anges i resultatet.|Inga|Åsidosätta|
|namn|Anger namnet på det huvud som ska anges.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Principavsnitt:** inkommande, utgående, bakåtsträvande, på-fel

-   **Principomfattningar:** alla scope

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Ange parameter för frågesträng
 Principen `set-query-parameter` lägger till, ersätter värdet för eller tar bort frågesträngparametern för begäran. Kan användas för att skicka frågeparametrar som förväntas av backend-tjänsten som är valfria eller aldrig finns i begäran.

### <a name="policy-statement"></a>Policyuttalande

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

#### <a name="forward-context-information-to-the-backend-service"></a>Vidarebefordra kontextinformation till backend-tjänsten
 Det här exemplet visar hur du tillämpar principen på API-nivå för att ange kontextinformation till serverdtjänsten. En demonstration av att konfigurera och använda den här principen finns i [Cloud Cover Episode 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och snabbspola fram till 10:30. Klockan 12:10 finns en demonstration av att ringa en åtgärd i utvecklarportalen där du kan se policyn på jobbet.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Mer information finns i [Principuttryck](api-management-policy-expressions.md) och [kontextvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-query-parameter|Rotelementet.|Ja|
|värde|Anger värdet på frågeparametern som ska ställas in. För flera frågeparametrar med `value` samma namn lägg till ytterligare element.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|finns-åtgärd|Anger vilken åtgärd som ska vidtas när frågeparametern redan är angiven. Det här attributet måste ha något av följande värden.<br /><br /> - åsidosättning - ersätter värdet för den befintliga parametern.<br />- hoppa över - ersätter inte det befintliga frågeparametervärdet.<br />- lägg till - lägger till värdet i det befintliga frågeparametervärdet.<br />- ta bort - tar bort frågeparametern från begäran.<br /><br /> När du `override` är inställd på att värva flera poster med samma namn resulterar frågeparametern som ställs in enligt alla poster (som kommer att visas flera gånger); endast angivna värden kommer att anges i resultatet.|Inga|Åsidosätta|
|namn|Anger namnet på den frågeparameter som ska anges.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, bakåtsträvande

-   **Principomfattningar:** alla scope

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>Skriv om URL
 Principen `rewrite-uri` konverterar en URL för begäran från sitt offentliga formulär till det formulär som förväntas av webbtjänsten, vilket visas i följande exempel.

- Offentlig WEBBADRESS -`http://api.example.com/storenumber/ordernumber`

- Url för begäran -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Den här principen kan användas när en mänsklig och/eller webbläsarvänlig URL ska omvandlas till det URL-format som förväntas av webbtjänsten. Den här principen behöver bara tillämpas när du exponerar ett alternativt URL-format, till exempel rena webbadresser, RESTful-webbadresser, användarvänliga webbadresser eller SEO-vänliga webbadresser som är rent strukturella webbadresser som inte innehåller en frågesträng och i stället bara innehåller resursens sökväg ( efter systemet och myndigheten). Detta görs ofta för estetiska, användbarhet, eller sökmotoroptimering (SEO) ändamål.

> [!NOTE]
>  Du kan bara lägga till frågesträngparametrar med hjälp av principen. Du kan inte lägga till extra mallsökvägsparametrar i omskrivnings-URL:en.

### <a name="policy-statement"></a>Policyuttalande

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
|skriv om-uri|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Default|
|---------------|-----------------|--------------|-------------|
|mall|Den faktiska webbtjänst-URL:en med eventuella frågesträngparametrar. När du använder uttryck måste hela värdet vara ett uttryck.|Ja|Ej tillämpligt|
|kopiera-oöverträffad-params|Anger om frågeparametrar i den inkommande begäran som inte finns i den ursprungliga URL-mallen läggs till i url:en som definieras av omskrivningsmallen|Inga|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande

-   **Principomfattningar:** alla scope

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>Omvandla XML med hjälp av ett XSLT
 Principen `Transform XML using an XSLT` tillämpar en XSL-omvandling på XML i begäran eller svarstexten.

### <a name="policy-statement"></a>Policyuttalande

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
|xsl-transformera|Rotelementet.|Ja|
|parameter|Används för att definiera variabler som används i transformeringen|Inga|
|xsl:formatmall|Rotformatmallelement. Alla element och attribut som definieras inom följer standard [XSLT-specifikationen](https://www.w3.org/TR/xslt)|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande, utgående

-   **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande avsnitt:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
