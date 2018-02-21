---
title: "Azure API Management begränsning åtkomstprinciper | Microsoft Docs"
description: "Läs mer om de begränsning åtkomstprinciper kan användas i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 5dd762cffbca909c5f682a16c86ef5a86f4860de
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="api-management-access-restriction-policies"></a>Principer för begränsning av API Management-åtkomst
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Principer för begränsning av åtkomst  
  
-   [Kontrollera HTTP-huvudet](api-management-access-restriction-policies.md#CheckHTTPHeader) -tillämpar existens och/eller värdet för ett HTTP-huvud.  
-   [Gränsen anropet frekvensen av prenumerationen](api-management-access-restriction-policies.md#LimitCallRate) -förhindrar API användning ger spikar i diagrammet genom att begränsa anropet frekvensen på grundval av per prenumeration.  
-   [Gränsen anropet frekvensen av nyckeln](#LimitCallRateByKey) -förhindrar API användning ger spikar i diagrammet genom att begränsa anropet frekvensen på grundval av per nyckel.  
-   [Begränsa anroparen IP-adresser](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar)-anrop från särskilda IP-adresser och/eller -adressintervall.  
-   [Kvot för uppsättningen av prenumerationen](api-management-access-restriction-policies.md#SetUsageQuota) -du kan tillämpa en förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per prenumeration.  
-   [Kvot för uppsättningen av nyckeln](#SetUsageQuotaByKey) -du kan tillämpa en förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per nyckel.  
-   [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) -tillämpar förekomst och giltigheten för en JWT extraheras från ett angivna HTTP-sidhuvud eller en angiven frågeparameter.  
  
##  <a name="CheckHTTPHeader"></a> Kontrollera HTTP-huvud  
 Använd den `check-header` princip för att genomdriva att en begäran har en angivna HTTP-huvudet. Du kan du kan också kontrollera om sidhuvudet har ett specifikt värde eller Sök efter en tillåtna värdeintervallet. Om den inte principen avslutar bearbeta begäran och returnerar det HTTP-kod och fel statusmeddelande som anges i principen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|check-header|Rotelementet.|Ja|  
|värde|Tillåtet värde för HTTP-huvud. När flera element med värdet har angetts, betraktas lyckas kontrollen om något av värdena finns en matchning.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|Felmeddelande om att returnera i texten för HTTP-svar om huvudet finns inte eller har ett ogiltigt värde. Det här meddelandet måste ha specialtecken (escaped) korrekt.|Ja|Gäller inte|  
|failed-check-httpcode|HTTP-statuskoden ska returneras om huvudet finns inte eller har ett ogiltigt värde.|Ja|Gäller inte|  
|header-name|Namnet på det HTTP-huvud för att kontrollera.|Ja|Gäller inte|  
|Ignorera skiftläge|Kan anges till True eller False. Om värdet är SANT om ignoreras när huvudets värde jämförs uppsättningen av godkända värden.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande, utgående  
  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
##  <a name="LimitCallRate"></a> Gränsen anropet frekvensen av prenumerationen  
 Den `rate-limit` princip förhindrar API användningstoppar på grundval av per prenumeration genom att begränsa anropet frekvensen till ett angivet antal per angiven tidsperiod. När den här principen utlöses anroparen tar emot en `429 Too Many Requests` Svarets statuskod.  
  
> [!IMPORTANT]
>  Den här principen kan användas en gång per dokument.  
>   
>  [Principuttrycken](api-management-policy-expressions.md) kan inte användas i något av attribut för principen för den här principen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="name" calls="number" renewal-period="seconds">  
        <operation name="name" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|Ställ in gräns|Rotelementet.|Ja|  
|api|Lägg till en eller flera av de här elementen att införa en anropet hastighetsbegränsning på API: er inom produkten. Produkt- och API anropa räntesats gränser som är oberoende av varandra.|Nej|  
|åtgärden|Lägg till en eller flera av de här elementen att införa en anropet hastighetsbegränsning på åtgärder i en API. Produkt-, API- och åtgärden anropa räntesats gränser som är oberoende av varandra.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|namn|Namnet på API som gräns för överföringshastigheten.|Ja|Gäller inte|  
|anrop|Det maximala totalt antalet anrop tillåts under en tidsperiod som angetts i den `renewal-period`.|Ja|Gäller inte|  
|renewal-period|Hur lång tid i sekunder, efter vilken kvoten återställs.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
  
-   **Princip för scope:** produkt  
  
##  <a name="LimitCallRateByKey"></a> Gränsen anropet frekvensen av nyckel  
 Den `rate-limit-by-key` princip förhindrar API användningstoppar på grundval av per nyckel genom att begränsa anropet frekvensen till ett angivet antal per angiven tidsperiod. Nyckeln kan ha ett godtyckligt värde och anges vanligtvis med ett principuttryck. Valfritt steg villkor kan läggas till ange vilka begäranden som ska räknas till gränsen. När den här principen utlöses anroparen tar emot en `429 Too Many Requests` Svarets statuskod.  
  
 Mer information och exempel på den här principen finns [avancerade begäran begränsning med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Den här principen kan användas en gång per dokument.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Exempel  
 I följande exempel aktiveras gräns för överföringshastigheten i anroparen IP-adress.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|Ställ in gräns|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|anrop|Det maximala totalt antalet anrop tillåts under en tidsperiod som angetts i den `renewal-period`.|Ja|Gäller inte|  
|motparterna nyckel|Nyckeln som ska användas för hastighet gränsen principen.|Ja|Gäller inte|  
|increment-condition|Den booleska uttryck som anger om begäran ska räknas mot kvoten (`true`).|Nej|Gäller inte|  
|renewal-period|Hur lång tid i sekunder, efter vilken kvoten återställs.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
##  <a name="RestrictCallerIPs"></a> Begränsa anroparen IP-adresser  
 Den `ip-filter` princip filtrerar (tillåter/nekar)-anrop från särskilda IP-adresser och/eller -adressintervall.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|ip-filter|Rotelementet.|Ja|  
|address|Anger en IP-adress som ska filtreras.|Minst en `address` eller `address-range` -element krävs.|  
|address-range from="address" to="address"|Anger ett intervall av IP-adresser som ska filtreras.|Minst en `address` eller `address-range` -element krävs.|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|address-range from="address" to="address"|Ett IP-adressintervall om du vill tillåta eller neka åtkomst för.|Krävs när den `address-range` elementet används.|Gäller inte|  
|IP-filteråtgärd = ”Tillåt &#124; förbjuda ”|Anger om anrop ska tillåtas eller inte för den angivna IP-adresser och intervall.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
##  <a name="SetUsageQuota"></a> Ange kvot för prenumeration  
 Den `quota` princip tillämpar förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per prenumeration.  
  
> [!IMPORTANT]
>  Den här principen kan användas en gång per dokument.  
>   
>  [Principuttrycken](api-management-policy-expressions.md) kan inte användas i något av attribut för principen för den här principen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="name" calls="number" bandwidth="kilobytes">  
        <operation name="name" calls="number" bandwidth="kilobytes" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|kvot|Rotelementet.|Ja|  
|api|Lägg till en eller flera av de här elementen att införa en kvot på API: er inom produkten. Produkt- och API-kvoter tillämpas oberoende av varandra.|Nej|  
|åtgärden|Lägg till en eller flera av de här elementen att införa en kvot på åtgärder i en API. Produkt-, API- och åtgärden kvoter tillämpas oberoende av varandra.|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|namn|Namnet på API: et eller åtgärden som kvoten gäller.|Ja|Gäller inte|  
|Bandbredd|Det maximala totala antalet kilobyte tillåts under en tidsperiod som angetts i den `renewal-period`.|Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges.|Gäller inte|  
|anrop|Det maximala totalt antalet anrop tillåts under en tidsperiod som angetts i den `renewal-period`.|Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges.|Gäller inte|  
|renewal-period|Hur lång tid i sekunder, efter vilken kvoten återställs.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** produkt  
  
##  <a name="SetUsageQuotaByKey"></a> Kvot för uppsättningen av nyckel  
 Den `quota-by-key` princip tillämpar förnyas eller livstid anropet volym och/eller bandbredd kvoten på grundval av per nyckel. Nyckeln kan ha ett godtyckligt värde och anges vanligtvis med ett principuttryck. Valfritt steg villkor kan läggas till ange vilka begäranden som ska räknas mot kvoten.  
  
 Mer information och exempel på den här principen finns [avancerade begäran begränsning med Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Den här principen kan användas en gång per dokument.  
>   
>  [Principuttrycken](api-management-policy-expressions.md) kan inte användas i något av attribut för principen för den här principen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Exempel  
 I följande exempel aktiveras kvoten av anroparen IP-adressen.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|kvot|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|Bandbredd|Det maximala totala antalet kilobyte tillåts under en tidsperiod som angetts i den `renewal-period`.|Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges.|Gäller inte|  
|anrop|Det maximala totalt antalet anrop tillåts under en tidsperiod som angetts i den `renewal-period`.|Antingen `calls`, `bandwidth`, eller båda tillsammans måste anges.|Gäller inte|  
|motparterna nyckel|Nyckeln som ska användas för kvot principen.|Ja|Gäller inte|  
|increment-condition|Den booleska uttryck som anger om begäran ska räknas mot kvoten (`true`)|Nej|Gäller inte|  
|renewal-period|Hur lång tid i sekunder, efter vilken kvoten återställs.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
##  <a name="ValidateJWT"></a> Validera JWT  
 Den `validate-jwt` princip tillämpar existens och giltigheten för en JWT extraheras från antingen angivna HTTP-huvudet eller angivna frågeparameter.  
  
> [!IMPORTANT]
>  Den `validate-jwt` principen kräver att den `exp` registrerade anspråk ingår i JWT-token om `require-expiration-time` attribut anges och värdet `false`.  
> Den `validate-jwt` stöder HS256 och RS256 signering algoritmer. Nyckeln måste anges infogad i princip i base64-kodade formatet för för HS256. RS256 har nyckeln att tillhandahålla via en öppen ID configuration slutpunkt.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Exempel  
  
#### <a name="azure-mobile-services-token-validation"></a>Azure Mobile Services token verifiering  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory token verifiering  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C-token verifiering  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Auktorisera åtkomst till åtgärder baserat på token anspråk  
 Det här exemplet visar hur du använder den [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) för att bevilja åtkomst till åtgärder före baserat på token anspråk. En demonstration av hur du konfigurerar och använder den här principen finns [moln omfattar avsnitt 177: mer API Management-funktioner med Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 13:50. Spola fram till 15:00 för att se de principer som konfigurerats i Redigeraren för grupprinciper och sedan till 18:50 för en demonstration av anropa en funktion från utvecklarportal både med och utan autentiseringstoken som krävs.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Element  
  
|Element|Beskrivning|Krävs|  
|-------------|-----------------|--------------|  
|validate-jwt|Rotelementet.|Ja|  
|målgrupper|Innehåller en lista över godkända målgruppen anspråk som kan ingå i token. Om flera målgruppen värden finns och sedan varje värde testas förrän antingen alla uttöms (i så fall verifieringen misslyckas) eller tills ett lyckas. Du måste ange minst en målgrupp.|Nej|  
|issuer-signing-keys|En lista över Base64-kodad säkerhetsnycklar som används för att verifiera signerade token. Om det finns flera säkerhetsnycklar och sedan varje nyckel testas förrän antingen alla uttöms (i så fall verifieringen misslyckas) eller tills ett lyckas (användbart för token förnyelse). Viktiga delar har en valfri `id` attribut som används för matchning mot `kid` anspråk.|Nej|  
|certifikatutfärdare|En lista över godkända säkerhetsobjekt som utfärdade token. Om flera utfärdaren värden finns och sedan varje värde testas förrän antingen alla uttöms (i så fall verifieringen misslyckas) eller tills ett lyckas.|Nej|  
|openid-config|Det element som används för att ange en kompatibel öppna ID configuration-slutpunkt som signering nycklar och utfärdaren kan hämtas.|Nej|  
|krävs anspråk|Innehåller en lista över anspråkstyper som förväntas finnas på token att anses vara giltiga. När den `match` -attributet är inställt på `all` varje anspråksvärde i principen måste finnas i token för verifiering ska lyckas. När den `match` -attributet är inställt på `any` måste finnas minst ett anspråk i token för verifiering ska lyckas.|Nej|  
|zumo-master-key|Huvudnyckeln för token som utfärdats av Azure Mobile Services|Nej|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|clock-skew|Timespan. Använd för att ange maximal förväntade tidsskillnad mellan systemklockorna hos token utfärdaren och API Management-instans.|Nej|0 sekunder|  
|failed-validation-error-message|Felmeddelande om att returnera i texten för HTTP-svar om JWT inte klarar valideringen. Det här meddelandet måste ha specialtecken (escaped) korrekt.|Nej|Standardfelmeddelandet beror på verifieringsfel, till exempel ”JWT finns inte”.|  
|failed-validation-httpcode|HTTP-statuskoden ska returneras om JWT inte valideras.|Nej|401|  
|header-name|Namnet på det HTTP-huvud som denna token.|Antingen `header-name` eller `query-paremeter-name` måste vara anges, men inte båda.|Gäller inte|  
|id|Den `id` attributet för den `key` element kan du ange den sträng som matchas mot `kid` anspråk i token (om sådan finns) att ta reda på lämplig nyckel ska användas för signaturverifiering.|Nej|Gäller inte|  
|Matcha|Den `match` attributet för den `claim` element anger om varje anspråksvärde i principen måste finnas i token för verifiering ska lyckas. Möjliga värden:<br /><br /> -                          `all` -värde för alla anspråk i principen måste finnas i token för verifiering ska lyckas.<br /><br /> -                          `any` -minst en anspråksvärdet måste finnas i token för verifiering ska lyckas.|Nej|all|  
|query-paremeter-name|Namnet på den Frågeparametern denna token.|Antingen `header-name` eller `query-paremeter-name` måste vara anges, men inte båda.|Gäller inte|  
|require-expiration-time|Booleskt värde. Anger om ett förfallodatum anspråk krävs i token.|Nej|sant|
|kräver schema|Namnet på token schemat, t.ex. "Bearer". När det här attributet anges säkerställer principen för det angivna schemat finns i huvudvärde auktorisering.|Nej|Gäller inte|
|Kräv-signerad-token|Booleskt värde. Anger om det krävs en token som ska signeras.|Nej|sant|  
|avgränsare|Sträng. Anger avgränsare (t.ex. ””,) som ska användas för att extrahera en uppsättning värden från ett anspråk med flera värden.|Nej|Gäller inte| 
|url|Öppna ID configuration slutpunkts-URL från där du kan hämta öppna ID configuration metadata. Använd följande URL för Azure Active Directory: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` ersätter din klient katalognamn, t.ex. `contoso.onmicrosoft.com`.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
-   **Princip för scope:** globala, produkt, API, åtgärden  
  
## <a name="next-steps"></a>Nästa steg

Arbeta med principer, Läs mer:

+ [Principer för i API-hantering](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
