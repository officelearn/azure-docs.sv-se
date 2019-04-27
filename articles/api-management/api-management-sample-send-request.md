---
title: Med API Management-tjänsten för att generera HTTP-begäranden
description: Lär dig att använda principer för begäranden och svar i API Management för att anropa externa tjänster från ditt API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 12/15/2016
ms.author: v-yiso
ms.date: 04/22/2019
ms.openlocfilehash: 2c4e5d0117f046343b140ef2b2c46c074c835075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557959"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Använda externa tjänster från Azure API Management-tjänsten
Principerna som är tillgängliga i Azure API Management-tjänsten kan göra en stor mängd arbete baserat helt och hållet på den inkommande begäranden, utgående svar och grundläggande konfigurationsinformation. Men att kunna interagera med externa tjänster från API Management principer öppnas många fler möjligheter.

Du tidigare har sett hur du interagerar med den [Azure Event Hub-tjänsten för loggning, övervakning och analys](api-management-log-to-eventhub-sample.md). Den här artikeln visar principer som gör att du kan interagera med alla externa HTTP-baserade tjänster. Dessa principer kan användas för att utlösa fjärrevenemang eller för att hämta information som används för att ändra den ursprungliga begäran och svaret på något sätt.

## <a name="send-one-way-request"></a>En-sätt – begäran om att skicka
Eventuellt enklaste externa interaktion är fire-and-forget-formatet för begäran som gör att en extern tjänst för att aviseras om någon typ av viktiga händelsen. Principer för åtkomstkontroll flow `choose` kan användas för att identifiera alla typer av villkor som du är intresserad av.  Om villkoret är uppfyllt, kan du göra en extern HTTP-begäran med hjälp av den [-en-sätt – begäran om att skicka](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) princip. Detta kan vara en begäran om att ett meddelandesystem som Hipchat eller Slack eller ett e-postmeddelande API som SendGrid eller MailChimp eller för viktiga supportärenden liknande PagerDuty. Alla dessa meddelandesystem har enkla HTTP-APIs som kan anropas.

### <a name="alerting-with-slack"></a>Aviseringar med Slack
I följande exempel visar hur du skickar ett meddelande till en Slack chattrum om HTTP-svarsstatuskod är större än eller lika med 500. Ett Intervallfel med 500 tyder på problem med serverdels-API som klienten om API: et inte kan lösa sig själva. Det krävs någon typ av åtgärd från API Management-sida.  

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

Slack har begreppet inkommande webhooks. När du konfigurerar en inkommande webhook, genererar en särskild URL, vilket gör att du kan göra en enkel POST-begäran och skicka ett meddelande till Slack-kanal i Slack. JSON-texten som du skapar baseras på ett format som definierats av Slack.

![Slack Webhook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Är fire- and -forget tillräckligt bra?
Det finns vissa kompromisser när du använder en fire-and-forget-formatet för begäran. Om det av någon anledning misslyckas denna begäran, och sedan felet inte rapporteras. Komplexiteten med att ha sekundära fel reporting system- och ytterligare kostnaden för att vänta tills svaret är inte berättigat i den här specifika situation. För scenarier där det är viktigt att kontrollera svar, kommer [-begäran om att skicka](/azure/api-management/api-management-advanced-policies#SendRequest) principen är ett bättre alternativ.

## <a name="send-request"></a>Skicka begäran
Den `send-request` principen gör det möjligt med hjälp av en extern tjänst att utföra komplex bearbetningsfunktioner och returnera data till API management-tjänsten som kan användas för ytterligare behandling av princip.

### <a name="authorizing-reference-tokens"></a>Auktorisera referens token
En större funktion i API Management skyddar serverdelsresurser. Om auktoriseringsservern som används av ditt API skapar [JWT-tokens](https://jwt.io/) som en del av dess OAuth2-flöde som [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) gör det, kan du använda den `validate-jwt` policyn för att verifiera tokens giltighet. Vissa auktorisering servrar skapa vad kallas [referera token](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) som inte går att verifiera utan att göra en motringning till auktoriseringsservern.

### <a name="standardized-introspection"></a>Standardiserad introspektion
Tidigare var har det ingen standardiserad möjlighet att kontrollera en referens-token med en auktoriseringsservern. Men en nyligen föreslagna standard [RFC 7662](https://tools.ietf.org/html/rfc7662) publicerades av IETF som definierar hur resursservern kan verifiera giltigheten hos en token.

### <a name="extracting-the-token"></a>Extrahera token
Det första steget är att extrahera token från auktoriseringsrubriken. Huvudets värde ska vara formaterad med den `Bearer` auktoriseringsschema, ett blanksteg och sedan autentiseringstoken som per [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Det finns tyvärr fall där auktoriseringsschema utelämnas. För att kontot för den här vid parsning av, API Management delar upp huvudets värde på ett blanksteg och väljer den sista strängen returnerade matris med strängar. Detta ger en lösning för felaktigt formaterat auktorisering rubriker.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Gör begäran om verifiering
När API Management har autentiseringstoken, göra API Management begäran om att validera token. RFC 7662 anropar den här processen introspektion och kräver att du `POST` ett HTML-formulär till introspektion resursen. HTML-formulär minst måste innehålla ett nyckel/värde-par med nyckeln `token`. Denna begäran till auktoriseringsservern måste också autentiseras för att säkerställa att skadliga klienter inte kan gå fiske för giltig token.

```xml
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
```

### <a name="checking-the-response"></a>Kontrollera svaret
Den `response-variable-name` attributet används för att ge åtkomst till returnerade svaret. Namnet som definierats i den här egenskapen kan användas som en nyckel till den `context.Variables` ordlista att komma åt den `IResponse` objekt.

Svarsobjekt, kan du hämta brödtext och RFC 7622 API Management som ser svaret måste vara ett JSON-objekt och måste innehålla minst en egenskap som kallas `active` som är ett booleskt värde. När `active` är SANT och sedan token ska vara giltigt.

### <a name="reporting-failure"></a>Rapporterar fel
Du kan använda en `<choose>` princip för att identifiera om token är ogiltig och i så fall returneras ett 401-svar.

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

Enligt [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) som beskriver hur `bearer` token ska användas, API Management returnerar också en `WWW-Authenticate` huvud med 401-svar. WWW-autentisering är avsedd att instruera en klient på hur du skapar en korrekt auktoriserad begäran. På grund av det breda utbudet av metoder som är möjligt med OAuth2-ramverket är det svårt att kommunicera all information som behövs. Som tur är har pågår för att [klienter identifiera hur du godkänner begäranden till resursservern korrekt](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Sista lösning
I slutet kommer få du följande princip:

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

Detta är endast en av många exempel på hur `send-request` princip kan användas för att integrera användbara externa tjänster i processen för begäranden och svar som passerar genom API Management-tjänsten.

## <a name="response-composition"></a>Svaret sammansättning
Den `send-request` principen kan användas för att utöka en primär begäran till en backend-system, som du såg i exemplet ovan, eller den kan användas som en fullständig Ersätt för backend-anropet. Med den här tekniken skapa du enkelt sammansatta resurser som räknas samman från flera olika system.

### <a name="building-a-dashboard"></a>Att skapa en instrumentpanel
Ibland vill du kan exponera information som finns i flera backend-system, till exempel, för att driva en instrumentpanel. KPI: er kommer från alla olika serverdelar, men du vill inte ge direktåtkomst till dem och det skulle vara skönt om all information som kan hämtas i en enskild begäran. En del av backend-information behöver kanske vissa uppdelning och undantang och lite sanitizing först! Att cachelagra den sammansatta resursen är en användbar för att minska belastningen serverdelen som du vet användarna har vana hammering på F5 för att se om deras underpresterar mått kan komma att ändras.    

### <a name="faking-the-resource"></a>Faking resursen
Det första steget att skapa instrumentpanelen resursen är att konfigurera en ny åtgärd i Azure-portalen. Det här är en platshållare åtgärd som används för att konfigurera en sammansättning för att skapa dynamiska resurs.

![Instrumentpanel för åtgärden](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Förfrågningar
När åtgärden har skapats kan konfigurera du en princip specifikt för den åtgärden. 

![Instrumentpanel för åtgärden](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Det första steget är att extrahera alla frågeparametrar från den inkommande begäran så att du kan vidarebefordra dem till serverdelen. I det här exemplet på instrumentpanelen visar information baserat på en viss tidsperiod och därför har en `fromDate` och `toDate` parametern. Du kan använda den `set-variable` princip för att hämta informationen från fråge-URL.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

När du har den här informationen kan göra du begäranden till backend-system. Varje begäran skapar en ny URL med parameterinformation och anropar dess respektive server och lagrar svaret i en variabel i kontexten.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Dessa begäranden köra i ordningsföljd, vilket inte är idealiskt. 

### <a name="responding"></a>Svara
Du kan använda för att konstruera sammansatta svaret den [returnera svar](/azure/api-management/api-management-advanced-policies#ReturnResponse) princip. Den `set-body` element kan använda ett uttryck för att skapa en ny `JObject` med alla komponenter representationer inbäddad som egenskaper.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Fullständig principen ser ut så här:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

Du kan konfigurera instrumentpanelen resursen som ska cachelagras i minst en timme i konfigurationen av platshållaren igen. 

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten tillhandahåller flexibla principer som kan tillämpas selektivt på HTTP-trafik och gör sammansättning av backend-tjänster. Om du vill förbättra din API-gateway med aviseringar funktion, verifiering, verifiering funktioner eller skapa nya sammansatta resurser baserat på flera serverdelstjänster på `send-request` och tillhörande principer öppnar en värld av möjligheter.


