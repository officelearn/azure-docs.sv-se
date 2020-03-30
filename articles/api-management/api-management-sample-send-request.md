---
title: Använda API Management-tjänsten för att generera HTTP-begäranden
description: Lär dig att använda principer för begäran och svar i API-hantering för att anropa externa tjänster från ditt API
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
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190018"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Använda externa tjänster från Azure API Management-tjänsten
Principerna som är tillgängliga i Azure API Management-tjänsten kan göra ett brett spektrum av användbart arbete baserat enbart på inkommande begäran, det utgående svaret och grundläggande konfigurationsinformation. Men att kunna interagera med externa tjänster från API Management-principer öppnar många fler möjligheter.

Du har tidigare sett hur du interagerar med [Azure Event Hub-tjänsten för loggning, övervakning och analys](api-management-log-to-eventhub-sample.md). Den här artikeln visar principer som gör att du kan interagera med alla externa HTTP-baserade tjänster. Dessa principer kan användas för att utlösa fjärrhändelser eller för att hämta information som används för att manipulera den ursprungliga begäran och svaret på något sätt.

## <a name="send-one-way-request"></a>Skicka-en-vägs-begäran
Möjligen den enklaste externa interaktionen är brand-och-glöm stil begäran som gör att en extern tjänst som skall meddelas om någon form av viktig händelse. Styrflödesprincipen `choose` kan användas för att identifiera alla typer av villkor som du är intresserad av.  Om villkoret är uppfyllt kan du göra en extern HTTP-begäran med hjälp av principen [skicka enriktad begäran.](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) Detta kan vara en begäran till ett meddelandesystem som Hipchat eller Slack, eller ett e-post-API som SendGrid eller MailChimp, eller för kritiska supportincidenter något som PagerDuty. Alla dessa meddelandesystem har enkla HTTP-API:er som kan anropas.

### <a name="alerting-with-slack"></a>Avisering med Slack
I följande exempel visas hur du skickar ett meddelande till ett slackchattrum om HTTP-svarsstatuskoden är större än eller lika med 500. Ett 500-intervallfel indikerar ett problem med serverda-API:et som klienten för API:et inte kan lösa själva. Det kräver vanligtvis någon form av ingripande på API Management del.  

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

Slack har begreppet inkommande webbkrokar. När slack konfigurerar en inkommande webbkrok genererar du en speciell webbadress, vilket gör att du kan göra en enkel POST-begäran och skicka ett meddelande till Slack-kanalen. Den JSON-brödtext som du skapar baseras på ett format som definieras av Slack.

![Slack webbkrok](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Är eld och glömma tillräckligt bra?
Det finns vissa kompromisser när du använder en brand-och-glömma stil begäran. Om begäran av någon anledning misslyckas rapporteras inte felet. I denna speciella situation är komplexiteten i att ha ett sekundärt felrapporteringssystem och den extra prestandakostnaden för att vänta på svaret inte berättigad. För scenarier där det är viktigt att kontrollera svaret är [policyn för skicka begäran](/azure/api-management/api-management-advanced-policies#SendRequest) ett bättre alternativ.

## <a name="send-request"></a>Skicka-begäran
Principen `send-request` gör det möjligt att använda en extern tjänst för att utföra komplexa bearbetningsfunktioner och returnera data till API-hanteringstjänsten som kan användas för ytterligare principbearbetning.

### <a name="authorizing-reference-tokens"></a>Auktorisera referenstoken
En viktig funktion i API Management är att skydda serverdresurser. Om auktoriseringsservern som används av ditt API skapar [JWT-token](https://jwt.io/) som en del av `validate-jwt` dess OAuth2-flöde, som Azure Active [Directory](../active-directory/hybrid/whatis-hybrid-identity.md) gör, kan du använda principen för att verifiera tokens giltighet. Vissa auktoriseringsservrar skapar så kallade [referenstoken](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) som inte kan verifieras utan att en motringning görs till auktoriseringsservern.

### <a name="standardized-introspection"></a>Standardiserad introspektion
Tidigare har det inte funnits något standardiserat sätt att verifiera en referenstoken med en auktoriseringsserver. Men en nyligen föreslagen standard [RFC 7662](https://tools.ietf.org/html/rfc7662) publicerades av IETF som definierar hur en resursserver kan verifiera giltigheten av en token.

### <a name="extracting-the-token"></a>Extrahera token
Det första steget är att extrahera token från auktoriseringshuvudet. Huvudvärdet ska formateras med `Bearer` auktoriseringsschemat, ett enda blanksteg och sedan auktoriseringstoken enligt [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Tyvärr finns det fall där tillståndssystemet utelämnas. För att ta hänsyn till detta när tolkning, api management delar rubrikvärdet på ett utrymme och väljer den sista strängen från den returnerade matrisen med strängar. Detta ger en lösning för dåligt formaterade auktoriseringshuvuden.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Göra valideringsbegäran
När API Management har auktoriseringstoken kan API Management göra begäran om att validera token. RFC 7662 anropar den här processens `POST` introspektion och kräver att du får ett HTML-formulär till introspektionsresursen. HTML-formuläret måste åtminstone innehålla ett nyckel-/värdepar med nyckeln `token`. Den här begäran till auktoriseringsservern måste också autentiseras för att säkerställa att skadliga klienter inte kan trålning för giltiga token.

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
Attributet `response-variable-name` används för att ge åtkomst till det returnerade svaret. Namnet som definieras i den här egenskapen `context.Variables` `IResponse` kan användas som en nyckel till ordlistan för att komma åt objektet.

Från svarsobjektet kan du hämta brödtexten och RFC 7622 talar om för API Management `active` att svaret måste vara ett JSON-objekt och måste innehålla minst en egenskap som heter som är ett booleskt värde. När `active` är sant anses token vara giltig.

Alternativt, om auktoriseringsservern inte innehåller fältet "aktiv" för att ange om token är giltig, använder du ett verktyg som Postman för att avgöra vilka egenskaper som anges i en giltig token. Om ett giltigt tokensvar till exempel innehåller en egenskap som heter "expires_in", kontrollerar du om det finns egenskapsnamnet i auktoriseringsserversvaret på det här sättet:

<när condition="@((IResponse)-sammanhang. Variabler["tokenstate"]). Body.As<JObject>(). Egenskap("expires_in") == null)">

### <a name="reporting-failure"></a>Rapporteringsfel
Du kan `<choose>` använda en princip för att identifiera om token är ogiltig och i så fall returnera ett 401-svar.

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

Enligt [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) som `bearer` beskriver hur token ska användas returnerar API Management också ett `WWW-Authenticate` huvud med 401-svaret. WWW-Autentisera är avsedd att instruera en klient om hur du skapar en korrekt auktoriserad begäran. På grund av de många olika tillvägagångssätt som är möjliga med OAuth2-ramen är det svårt att kommunicera all nödvändig information. Lyckligtvis finns det ansträngningar pågår för att hjälpa [klienter att upptäcka hur man korrekt auktorisera förfrågningar till en resursserver](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Slutlig lösning
I slutet får du följande policy:

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

Detta är bara ett av `send-request` många exempel på hur principen kan användas för att integrera användbara externa tjänster i processen för begäranden och svar som flödar via API Management-tjänsten.

## <a name="response-composition"></a>Responssammansättning
Principen `send-request` kan användas för att förbättra en primär begäran till ett backend-system, som du såg i föregående exempel, eller så kan den användas som en fullständig ersättning för backend-anropet. Med den här tekniken kan du enkelt skapa sammansatta resurser som aggregeras från flera olika system.

### <a name="building-a-dashboard"></a>Skapa en instrumentpanel
Ibland vill du kunna exponera information som finns i flera backend-system, till exempel för att driva en instrumentpanel. KPI: er kommer från alla olika back-ends, men du skulle föredra att inte ge direkt tillgång till dem och det skulle vara trevligt om all information kunde hämtas i en enda begäran. Kanske en del av backend information behöver lite skivning och tärning och lite sanering först! Att kunna cachelagra den sammansatta resursen skulle vara ett användbart för att minska backend-belastningen som du vet att användarna har en vana att hamra F5-nyckeln för att se om deras underpresterande mått kan ändras.    

### <a name="faking-the-resource"></a>Fejka resursen
Det första steget för att skapa instrumentpanelsresursen är att konfigurera en ny åtgärd i Azure-portalen. Det här är en platshållaråtgärd som används för att konfigurera en sammansättningsprincip för att skapa den dynamiska resursen.

![Åtgärd för instrumentpanel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Göra förfrågningar
När åtgärden har skapats kan du konfigurera en princip specifikt för den åtgärden. 

![Åtgärd för instrumentpanel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Det första steget är att extrahera alla frågeparametrar från den inkommande begäran, så att du kan vidarebefordra dem till serverdelen. I det här exemplet visar instrumentpanelen information baserat på `fromDate` `toDate` en tidsperiod och har därför en och parameter. Du kan `set-variable` använda principen för att extrahera informationen från url:en för begäran.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

När du har den här informationen kan du göra förfrågningar till alla backend-system. Varje begäran skapar en ny URL med parameterinformationen och anropar respektive server och lagrar svaret i en kontextvariabel.

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

Dessa begäranden körs i följd, vilket inte är idealiskt. 

### <a name="responding"></a>Svara
Om du vill skapa det sammansatta svaret kan du använda [retursvarsprincipen.](/azure/api-management/api-management-advanced-policies#ReturnResponse) Elementet `set-body` kan använda ett uttryck `JObject` för att skapa ett nytt med alla komponentrepresentationer inbäddade som egenskaper.

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

Den fullständiga principen ser ut som följer:

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

I konfigurationen av platshållaråtgärden kan du konfigurera instrumentpanelsresursen som ska cachelagras i minst en timme. 

## <a name="summary"></a>Sammanfattning
Azure API Management-tjänsten tillhandahåller flexibla principer som kan tillämpas selektivt på HTTP-trafik och möjliggör sammansättning av serverd-tjänster. Oavsett om du vill förbättra din API-gateway med varningsfunktioner, verifiering, valideringsfunktioner `send-request` eller skapa nya sammansatta resurser baserat på flera serverdtjänster öppnar och relaterade principer en värld av möjligheter.

