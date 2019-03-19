---
title: Azure API Management-omvandlingsprinciper | Microsoft Docs
description: Läs mer om principerna för anspråksomvandling som är tillgängliga för användning i Azure API Management.
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
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 72348085a69746306e40029bc7473df271b60221
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105292"
---
# <a name="api-management-transformation-policies"></a>API Management-principer för anspråksomvandling
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Omvandlingsprinciper

-   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begära eller svaret body från JSON till XML.

-   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begära eller svaret body från XML till JSON.

-   [Sök och Ersätt sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) – söker efter en begäran eller ett svar delsträng och ersätter den med en annan understräng.

-   [Maskera URL: er i innehåll](api-management-transformation-policies.md#MaskURLSContent) -skriver (masker) länkar i svaret body så att de pekar på motsvarande länk via gatewayen.

-   [Ange serverdelstjänst](api-management-transformation-policies.md#SetBackendService) -ändras serverdelstjänst för en inkommande begäran.

-   [Ange brödtext](api-management-transformation-policies.md#SetBody) -anger meddelandets brödtext för inkommande och utgående förfrågningar.

-   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till en befintlig svar och/eller begärandehuvudet eller lägger till ett nytt svar och/eller begäran-huvud.

-   [Ange parametern för frågesträngen](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet för eller tar bort begäran frågesträngparametern.

-   [Omskrivningswebbadressen](api-management-transformation-policies.md#RewriteURL) – konverterar en begärd URL från sin offentliga form i formuläret som förväntas av webbtjänsten.

-   [Transformera XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) -gäller en XML-transformering för XML i brödtexten för begäran eller ett svar.

##  <a name="ConvertJSONtoXML"></a> Konvertera JSON till XML
 Den `json-to-xml` princip konverterar en brödtext för begäran eller ett svar från JSON till XML.

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
|json-to-xml|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|använd|Attributet måste anges till något av följande värden.<br /><br /> -alltid - alltid att gälla konvertering.<br />-innehåll-typ-json - convert endast om svar Content-Type-rubriken anger förekomsten av JSON.|Ja|Gäller inte|
|consider-accept-header|Attributet måste anges till något av följande värden.<br /><br /> gäller - true - konvertering om JSON har begärts i begäran Accept-huvud.<br />-false - alltid att gälla konvertering.|Nej|true|
|parsa datum|När värdet `false` datumvärden kopieras bara under omvandlingen|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående, vid fel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="ConvertXMLtoJSON"></a> Konvertera XML till JSON
 Den `xml-to-json` princip konverterar en brödtext för begäran eller ett svar från XML till JSON. Den här principen kan användas för att modernisera API: er baserat på serverdelen för endast XML-webbtjänster.

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
|xml-to-json|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|typ|Attributet måste anges till något av följande värden.<br /><br /> -javascript-vänlig - konverterade JSON har ett eget till JavaScript-utvecklare formulär.<br />– direkt - återspeglar konverterade JSON det ursprungliga XML-dokumentets struktur.|Ja|Gäller inte|
|använd|Attributet måste anges till något av följande värden.<br /><br /> Konvertera - alltid - alltid.<br />-innehåll-typ-xml - convert endast om svar Content-Type-rubriken anger förekomsten av XML.|Ja|Gäller inte|
|consider-accept-header|Attributet måste anges till något av följande värden.<br /><br /> tillämpa - true - konvertering om begärs XML i begäran Accept-huvud.<br />-false - alltid att gälla konvertering.|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående, vid fel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="Findandreplacestringinbody"></a> Sök och Ersätt sträng i brödtext
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
|find-and-replace|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|från|Sträng att söka efter.|Ja|Gäller inte|
|till|Ersättningssträngen. Ange en tom ersättningssträng för att ta bort söksträngen.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="MaskURLSContent"></a> Masken URL: er i innehåll
 Den `redirect-content-urls` princip skriver (masker) länkar i svarstexten så att de pekar på motsvarande länk via gatewayen. Använda i utgående avsnitt för att skriva svar brödtext länkar så att de pekar på gatewayen. Använd i avsnittet inkommande för en motsatt effekt.

> [!NOTE]
>  Den här principen ändrar inte alla värden i huvudet som `Location` rubriker. Du kan ändra värden i huvudet med den [angivet sidhuvud](api-management-transformation-policies.md#SetHTTPheader) princip.

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
|redirect-content-urls|Rotelement.|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="SetBackendService"></a> Set-servertjänst
 Använd den `set-backend-service` för att omdirigera en inkommande begäran till en annan serverdel än den som angetts i API-inställningarna för den åtgärden. Den här principen ändrar backend service bas-URL för inkommande begäran till den som angetts i principen.

### <a name="policy-statement"></a>Principframställning

```xml
<set-backend-service base-url="base URL of the backend service" />
```

eller

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Backend-enheter kan hanteras via hantering av [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

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
I det här exemplet dirigerar set service serverdelsprincip förfrågningar baserat på versionsvärdet som skickas i frågesträngen till en annan serverdelstjänst än det som angavs i API: et.

Den grundläggande Webbadressen för backend-tjänsten är inledningsvis hämtad från API-inställningar. Så förfrågans Webbadress `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` blir `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` där `http://contoso.com/api/10.4/` är backend-tjänst-URL som anges i inställningarna för API.

När den [< Välj\> ](api-management-advanced-policies.md#choose) Principframställning tillämpas den grundläggande Webbadressen för backend-tjänsten kan ändra igen antingen `http://contoso.com/api/8.2` eller `http://contoso.com/api/9.1`, beroende på värdet för Frågeparametern version begäran. Om värdet är till exempel `"2013-15"` sista begäran URL blir `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Om ytterligare transformering av begäran är önskade, andra [omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies) kan användas. Så här tar du bort version Frågeparametern nu att begäran dirigeras till en specifik version-serverdel exempelvis den [ange parametern för frågesträngen](api-management-transformation-policies.md#SetQueryStringParameter) princip kan användas för att ta bort nu redundant Versionsattributet.

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
I det här exemplet dirigerar principen begäran till en service fabric-serverdelen, med hjälp av frågesträngen användar-ID som partitionsnyckel och använder den primära repliken av partitionen.

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-backend-service|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|bas-url|Ny backend-tjänsten bas-URL.|En av `base-url` eller `backend-id` måste finnas.|Gäller inte|
|backend-id|Identifierare för att dirigera till serverdelen. (Serverdel entiteter hanteras via [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) och [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|En av `base-url` eller `backend-id` måste finnas.|Gäller inte|
|sf-partition-key|Gäller endast när serverdelen är en Service Fabric-tjänst och har angetts med hjälp av backend-id. Används för att lösa en specifik partition från Namnmatchningstjänsten.|Nej|Gäller inte|
|SF-replik-type|Gäller endast när serverdelen är en Service Fabric-tjänst och har angetts med hjälp av backend-id. Styr om begäran ska skickas till den primära eller sekundära repliken av en partition. |Nej|Gäller inte|
|sf-resolve-condition|Gäller endast när serverdelen är en Service Fabric-tjänst. Ange ett villkor för identifiera om anropet till Service Fabric-serverdelen har upprepas med nya lösningar.|Nej|Gäller inte|
|sf-service-instance-name|Gäller endast när serverdelen är en Service Fabric-tjänst. Du kan ändra instanser av tjänsten vid körning. |Nej|Gäller inte|
|sf-listener-name|Gäller endast när serverdelen är en Service Fabric-tjänst och har angetts med hjälp av backend-id. Service Fabric Reliable Services kan du skapa flera lyssnare i en tjänst. Det här attributet används för att välja en viss lyssnare när en serverdel tillförlitlig tjänst har mer än en lyssnare. Om det här attributet inte anges försöker API Management att använda en lyssnare utan namn. En lyssnare utan namn är typiskt för Reliable Services som har endast en lyssnare. |Nej|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, serverdel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="SetBody"></a> Ställ in brödtext
 Använd den `set-body` Grupprincip för att ange meddelandetexten för inkommande och utgående förfrågningar. Komma åt meddelandetext som du kan använda den `context.Request.Body` egenskapen eller `context.Response.Body`, beroende på om principen är i avsnittet inkommande eller utgående.

> [!IMPORTANT]
>  Observera att som standard när du har åtkomst till meddelandet body med `context.Request.Body` eller `context.Response.Body`, ursprungliga meddelandetexten går förlorad och måste anges genom att returnera innehållet i uttrycket. För att bevara brödtext, ange den `preserveContent` parameter `true` vid åtkomst till meddelandet. Om `preserveContent` är inställd på `true` och en annan text returnerades av uttrycket returnerade brödtext används.
> 
>  Observera följande när du använder den `set-body` principen.
> 
> - Om du använder den `set-body` princip för att returnera en ny eller uppdaterad text som du inte behöver ange `preserveContent` till `true` eftersom tillhandahåller du uttryckligen de nya innehållet i meddelandetexten.
>   -   Behålla innehållet i ett svar i den inkommande pipelinen vara inte meningsfullt eftersom det finns inget svar ännu.
>   -   Behålla innehållet i en begäran i utgående pipelinen vara inte meningsfullt eftersom begäran har redan skickats till serverdelen i det här läget.
>   -   Om den här principen används när det finns inga meddelandetexten, till exempel i en inkommande GET, genereras ett undantagsfel.

 Mer information finns i den `context.Request.Body`, `context.Response.Body`, och `IMessage` avsnitten i den [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables) tabell.

### <a name="policy-statement"></a>Principframställning

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Exempel

#### <a name="literal-text-example"></a>Citat-exempel

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>T.ex. åtkomst till innehållet som en sträng. Observera att vi bevarar den ursprungliga begärandetexten så att vi kan komma åt den senare i pipelinen.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>T.ex. åtkomst till innehållet som en JObject. Observera att eftersom vi inte reserverar ursprungliga begärandetexten, kommer åt dem senare i pipelinen resulterar i ett undantag.

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
 Det här exemplet visar hur du utför innehållsfiltrering genom att ta bort dataelement från svaret från serverdelstjänsten när du använder den `Starter` produkten. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 34:30. Starta vid 31:50 att se en översikt över [mörk Sky Prognostisera API](https://developer.forecast.io/) används för den här demon.

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

### <a name="using-liquid-templates-with-set-body"></a>Använd flytande mallar med Ställ in brödtext
Den `set-body` principen kan konfigureras för att använda den [flytande](https://shopify.github.io/liquid/basics/introduction/) mall språk att omvandla brödtexten i en begäran eller ett svar. Detta kan vara mycket effektivt om du behöver du helt Omforma formatet för meddelandet.

> [!IMPORTANT]
> Implementeringen av vätska som används i den `set-body` principen är konfigurerad för läget ”c”. Detta är särskilt viktigt när du gör sådant som filtrering. Till exempel med hjälp av ett filter måste du använda Pascal gemener och versaler och C# datum formatering t.ex.:
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> För att kunna bindas till en XML-text med en flytande mall, använda en `set-header` Grupprincip för att ange Content-Type till antingen application/xml, text/xml (eller någon typ som slutar med + xml); en JSON-brödtexten, det måste vara application/json, text/json (eller någon annan typ som slutar med + JSON).

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

#### <a name="transform-json-using-a-liquid-template"></a>Transformera JSON med en flytande mall
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
|Ställ in brödtext|Rotelement. Innehåller brödtexten eller ett uttryck som returnerar en brödtext.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|mall|Används för att ändra läget mall som principen set brödtext ska köras i. För närvarande är det enda värdet som stöds:<br /><br />-flytande - ange brödtext princip kommer att använda den flytande mall-motorn |Nej|Liquid|

För att komma åt information om begäran och svaret kan flytande mallen bindas till en context-objektet med följande egenskaper: <br />
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
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="SetHTTPheader"></a> Ange HTTP-huvud
 Den `set-header` principen tilldelas ett värde till en befintlig svar och/eller huvudet i begäran eller lägger till ett nytt svar och/eller begäran-huvud.

 Infogar en lista över HTTP-huvuden i ett HTTP-meddelande. När placeras i en inkommande pipeline kommer anger den här principen HTTP-huvuden för begäran som skickas till Måltjänsten. När placeras i en utgående pipeline kommer anger den här principen HTTP-huvuden för svar som skickas till gatewayens klienten.

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
 Det här exemplet visar hur du tillämpa principen på API-nivå för att ange kontextinformation till serverdelstjänsten. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 10:30. Det finns en demo för att anropa en åtgärd i developer-portalen där du kan se principen på arbetet vid 12:10.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Mer information finns i [principuttryck](api-management-policy-expressions.md) och [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Flera värden i ett sidhuvud sammanfogas till en CSV-sträng, till exempel: `headerName: value1,value2,value3`
>
> Undantagen omfattar standardiserad rubriker, vilka värden:
> - kan innehålla kommatecken (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - kan innehålla datum (`Cookie`, `Set-Cookie`, `Warning`),
> - innehåller datum (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> Vid dessa undantag flera värden i huvudet kommer inte att sammanfogas till en sträng och kommer att skickas som separata rubriker, till exempel: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-header|Rotelement.|Ja|
|värde|Anger värdet för rubriken anges. För flera rubriker med samma namn Lägg till ytterligare `value` element.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|exists-action|Anger vilken åtgärd som ska vidtas när rubriken har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för befintlig rubrik.<br />-skip - ersätter inte befintliga huvudets värde.<br />-Tillägg - lägger till värdet till det befintliga värdet för sidhuvudet.<br />-delete - tar bort huvudet i begäran.<br /><br /> När värdet `override` ta med flera poster med samma namn resulterar i rubriken anges enligt alla poster (som visas flera gånger); endast listade värdena anges i resultatet.|Nej|åsidosättning|
|namn|Anger namnet på rubriken anges.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående serverdelen, vid fel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="SetQueryStringParameter"></a> Ange parametern för frågesträngen
 Den `set-query-parameter` Grupprincip lägger till, ersätter värdet för, eller tar bort begär frågesträngparametern. Kan användas för att skicka fråga parametrar som förväntas av backend-tjänsten som är valfria eller aldrig finns i begäran.

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
 Det här exemplet visar hur du tillämpa principen på API-nivå för att ange kontextinformation till serverdelstjänsten. En demonstration av hur du konfigurerar och använder den här principen finns [Cloud Cover avsnittet 177: Fler API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 10:30. Det finns en demo för att anropa en åtgärd i developer-portalen där du kan se principen på arbetet vid 12:10.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Mer information finns i [principuttryck](api-management-policy-expressions.md) och [sammanhangsvariabel](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Element

|Namn|Beskrivning|Krävs|
|----------|-----------------|--------------|
|set-query-parameter|Rotelement.|Ja|
|värde|Anger värdet för Frågeparametern anges. För flera frågeparametrar med samma namn Lägg till ytterligare `value` element.|Ja|

### <a name="properties"></a>Egenskaper

|Namn|Beskrivning|Krävs|Standard|
|----------|-----------------|--------------|-------------|
|exists-action|Anger vilken åtgärd som ska vidtas när Frågeparametern har redan angetts. Det här attributet måste ha något av följande värden.<br /><br /> -åsidosätt - ersätter värdet för parametern befintliga.<br />-skip - ersätter inte fråga befintlig parametervärdet.<br />-Tillägg - lägger till värdet till det befintliga frågan parametervärdet.<br />-ta bort – tar bort Frågeparametern från begäran.<br /><br /> När värdet `override` ta med flera poster med samma namn resulterar i Frågeparametern anges enligt alla poster (som visas flera gånger); endast listade värdena anges i resultatet.|Nej|åsidosättning|
|namn|Anger namnet på parametern fråga att ställas in.|Ja|Gäller inte|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, serverdel

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="RewriteURL"></a> Omskrivningswebbadressen
 Den `rewrite-uri` princip konverterar en begärd URL från sin offentliga form i formuläret som förväntas av webbtjänsten som visas i följande exempel.

- Offentlig URL- `http://api.example.com/storenumber/ordernumber`

- Fråge-URL- `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Den här principen kan användas när en mänsklig och/eller webbläsare-vänlig URL ska omvandlas till URL-format som förväntas av webbtjänsten. Den här principen måste endast tillämpas när exponera ett annat URL-format, till exempel ren URL: er, RESTful URL: er, användarvänliga webbadresser eller SEO-vänlig URL: er som är helt och hållet strukturella URL: er som inte innehåller en frågesträng och i stället innehåller endast sökvägen till resursen ( efter schemat och behörighet). Detta sker ofta för dess estetiska egenskaper, användbarhet eller sökmotor optimeringssyfte (SEO).

> [!NOTE]
>  Du kan bara lägga till frågesträngparametrar som använder principen. Du kan inte lägga till extra sökväg mallparametrar i Skriv om URL-Adressen.

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
|Skriv om-uri|Rotelement.|Ja|

### <a name="attributes"></a>Attribut

|Attribut|Beskrivning|Krävs|Standard|
|---------------|-----------------|--------------|-------------|
|mall|Faktiska webbtjänstens URL med alla parametrar för frågesträngen. När du använder uttryck kan måste hela värdet vara ett uttryck.|Ja|Gäller inte|
|copy-unmatched-params|Anger om Frågeparametrar i den inkommande begäranden finns inte i den ursprungliga mallen URL läggs till den URL som definierats av mallen skriva|Nej|true|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande

-   **Princip-scope:** globala, produkt, API, igen

##  <a name="XSLTransform"></a> Transformera XML med hjälp av en XSLT
 Den `Transform XML using an XSLT` principen gäller en XML-transformering för XML i brödtexten för begäran eller ett svar.

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
|xsl-transform|Rotelement.|Ja|
|Parameter|Används för att definiera variabler som används i listan i transformeringen|Nej|
|XSL: stylesheet|Formatmall rotelement. Alla element och attribut som definierats i följer standarden [XSLT-specifikation](https://www.w3.org/TR/xslt)|Ja|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip-avsnitt:** inkommande, utgående

-   **Princip-scope:** globala, produkt, API, igen

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande avsnitt:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)
