---
title: Azure Application Insights för JavaScript-webbappar
description: Hämta sidvisning och sessionsantal, webbklientdata, SPA (Single Page Applications) och spåra användningsmönster. Identifiera undantag och prestandaproblem på JavaScript-baserade webbsidor.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276080"
---
# <a name="application-insights-for-web-pages"></a>Application Insights för webbsidor

Visa prestanda och användning för webbsidor eller appar. Om du lägger till [Application Insights](app-insights-overview.md) till din sida skript, får du timings av sidan laster och AJAX samtal, räknas och information om webbläsarundantag och AJAX misslyckanden, samt användare och räknas session. Allt detta kan visas efter sida, klientoperativsystem- och webbläsarversion, geografisk plats och andra dimensioner. Du kan ställa in varningar för antal fel eller långsam sidinläsning. Och genom att infoga spårning av anrop i JavaScript-kod kan du spåra hur olika funktioner i ditt webbsideprogram används.

Application Insights kan användas med alla webbsidor – du lägger bara till ett stycke JavaScript-kod. Om din webbtjänst är [Java](java-get-started.md) eller [ASP.NET](asp-net.md)kan du använda SDK:er på serversidan tillsammans med JavaScript SDK på klientsidan för att få en heltäckande förståelse av appens prestanda.

## <a name="adding-the-javascript-sdk"></a>Lägga till JavaScript SDK

1. Först behöver du en Application Insights-resurs. Om du inte redan har en resurs- och instrumenteringsnyckel följer du [inskapandet av nya resursinstruktioner](create-new-resource.md).
2. Kopiera instrumenteringsnyckeln från den resurs där du vill att javascript-telemetrin ska skickas.
3. Lägg till Programstatistiken JavaScript SDK på din webbsida eller app via något av följande två alternativ:
    * [npm Installationsprogrammet](#npm-based-setup)
    * [JavaScript-kodavsnitt](#snippet-based-setup)

> [!IMPORTANT]
> Använd bara en metod för att lägga till JavaScript SDK i ditt program. Om du använder NPM-installationen ska du inte använda kodavsnittet och vice versa.

> [!NOTE]
> NPM-installationen installerar JavaScript SDK som ett beroende av projektet, vilket aktiverar IntelliSense, medan kodavsnittet hämtar SDK vid körning. Båda har samma stöd för samma funktioner. Utvecklare som önskar fler anpassade händelser och konfiguration väljer dock i allmänhet NPM-installation, medan användare som letar efter snabb aktivering av webbanalys utanför boxen väljer kodavsnittet.

### <a name="npm-based-setup"></a>npm-baserad inställning

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kodavsnittsbaserad inställning

Om din app inte använder npm kan du instrumentera dina webbsidor direkt med Application Insights genom att klistra in det här kodavsnittet högst upp på varje sida. Helst bör det vara det `<head>` första skriptet i ditt avsnitt så att det kan övervaka eventuella problem med alla dina beroenden. Om du använder Blazor Server App lägger du till `_Host.cshtml` kodavsnittet högst upp i filen i avsnittet. `<head>`

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Skicka telemetri till Azure-portalen

Som standard samlar Programinsikterna JavaScript SDK automatiskt ett antal telemetriobjekt som är användbara för att fastställa programmets och den underliggande användarupplevelsens hälsa. Exempel på dessa är:

- **Oavdrag undantag** i din app, inklusive information om
    - Stackspårning
    - Undantagsinformation och meddelande som medföljer felet
    - Rad & kolumn antal fel
    - URL där felet uppstod
- **Nätverksberoendebegäranden som** görs av din app **XHR-** och Fetch-begäranden (hämtningssamlingen är inaktiverad som standard), innehåller information om **Fetch**
    - Url till beroendekälla
    - Kommando & metod som används för att begära beroendet
    - Begärans varaktighet
    - Resultatkod och lyckad status för begäran
    - ID (om någon) för användaren som gör begäran
    - Korrelationskontext (om sådan finns) där begäran görs
- **Användarinformation** (till exempel Plats, nätverk, IP)
- **Enhetsinformation** (till exempel Webbläsare, OS, version, språk, upplösning, modell)
- **Sessionsinformation**

### <a name="telemetry-initializers"></a>Telemetriinitierare
Telemetriinitierare används för att ändra innehållet i insamlad telemetri innan de skickas från användarens webbläsare. De kan också användas för att förhindra att viss `false`telemetri skickas genom att returnera . Flera telemetriinitierare kan läggas till i application insights-instansen och de körs för att lägga till dem.

Indataargumentet som `addTelemetryInitializer` ska [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) anropas är en `boolean` motringning som tar ett argument och returnerar ett eller `void`. Om retur `false`skickas inte telemetriartikeln, annars fortsätter den till nästa telemetriinitierare, om sådan finns, eller skickas till slutpunkten för telemetrisamlingen.

Ett exempel på hur du använder telemetriinitierare:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfiguration
De flesta konfigurationsfält namnges så att de kan vara falska som standard. Alla fält är `instrumentationKey`valfria förutom .

| Namn | Default | Beskrivning |
|------|---------|-------------|
| instrumenteringNyckel | null | **Obligatoriskt**<br>Instrumenteringsnyckel som du har hämtat från Azure-portalen. |
| accountId | null | Ett valfritt konto-ID om din app grupperar användare i konton. Inga blanksteg, kommatecken, semikolon, lika med eller lodräta staplar |
| sessionRenewalMs | 1800000 | En session loggas om användaren är inaktiv under den här tiden i millisekunder. Standard är 30 minuter |
| sessionExpirationM | 86400000 | En session loggas om den har fortsatt under den här tiden i millisekunder. Standard är 24 timmar |
| maxBatchSizeInBytes | 10000 | Max storlek på telemetribatch. Om en batch överskrider denna gräns skickas den omedelbart och en ny batch startas |
| maxBatchInterval | 15 000 | Hur lång tid att batch telemetri för innan du skickar (millisekunder) |
| disableExceptionTracking | false | Om värdet är true samlas inga undantag automatiskt. Standardvärdet är false. |
| inaktiveraTLemetry | false | Om värdet är true samlas telemetri inte in eller skickas. Standardvärdet är false. |
| enableDebug | false | Om värdet är true genereras **interna** felsökningsdata som ett undantag **i stället för** att loggas, oavsett SDK-loggningsinställningar. Standardvärdet är false. <br>***Anm.:*** Om du aktiverar den här inställningen kommer det att resultera i bortfallen telemetri när ett internt fel inträffar. Detta kan vara användbart för att snabbt identifiera problem med din konfiguration eller användning av SDK. Om du inte vill förlora telemetri när du `consoleLoggingLevel` felsöker kan du överväga att använda eller `telemetryLoggingLevel` i stället för `enableDebug`. |
| loggaLevelConsole | 0 | Loggar **interna** application insights-fel till konsolen. <br>0: av, <br>1: Kritiska fel endast, <br>2: Allt (fel & varningar) |
| loggaLevelTelemetry | 1 | Skickar **interna** application insights-fel som telemetri. <br>0: av, <br>1: Kritiska fel endast, <br>2: Allt (fel & varningar) |
| diagnostikLogInterval | 10000 | (intern) Avsökningsintervall (i ms) för intern loggningskö |
| provtagningPercentage | 100 | Procentandel av händelser som ska skickas. Standard är 100, vilket innebär att alla händelser skickas. Ange detta om du vill bevara datataket för storskaliga program. |
| autoTrackPageVisitTime | false | Om värdet är sant spåras den tidigare instrumenterade sidans visningstid på en sidvisning och skickas som telemetri och en ny timer startas för den aktuella sidvisningen. Standardvärdet är false. |
| inaktiveraAjaxTracking | false | Om sant, Ajax samtal är inte automatiskt. Standardvärdet är false. |
| inaktiveraFetchTracking | true | Om värdet är true samlas inte Hämta begäranden automatiskt. Standard är sant |
| åsidosättaPageViewDuration | false | Om värdet är true ändras standardbeteendet för trackPageView till att registrera slutet av sidvisningets varaktighetsintervall när trackPageView anropas. Om det finns falsk och ingen anpassad varaktighet för att spåraPageView beräknas sidvisningsprestanda med hjälp av api:et för navigeringstider. Standardvärdet är false. |
| maxAjaxCallsPerView | 500 | Standard 500 - styr hur många Ajax-samtal som kommer att övervakas per sidvisning. Ställ in på -1 för att övervaka alla (obegränsade) Ajax samtal på sidan. |
| inaktiveraDataLossAnalys | true | Om det är falskt kontrolleras interna telemetriavsänkarbuffertar vid start för objekt som ännu inte har skickats. |
| inaktiveraKorrelationHeaders | false | Om det är falskt lägger SDK till två rubriker ("Request-Id" och "Request-Context") i alla beroendebegäranden för att korrelera dem med motsvarande begäranden på serversidan. Standardvärdet är false. |
| correlationHeaderExcludedDomains |  | Inaktivera korrelationsrubriker för specifika domäner |
| korrelationHeaderDomaner |  | Aktivera korrelationsrubriker för specifika domäner |
| inaktiveraFlushOnBeforeUnload | false | Standard falskt. Om värdet är sant anropas inte infärgningsmetoden när händelseutlösare utlöses |
| aktiveraSessionStorageBuffer | true | Standard sant. Om värdet är true lagras bufferten med all telemetri som inte är kopplad i sessionslagringen. Bufferten återställs vid sidinläsning |
| isCookieUseDisabled | false | Standard falskt. Om det är sant kommer SDK inte att lagra eller läsa några data från cookies.|
| cookieDomain | null | Anpassad cookie-domän. Detta är användbart om du vill dela Application Insights-cookies över underdomäner. |
| isRetryDisabled | false | Standard falskt. Om falskt, försök igen på 206 (delvis framgång), 408 (timeout), 429 (för många begäranden), 500 (internt serverfel), 503 (tjänsten inte tillgänglig) och 0 (offline, endast om det upptäcks) |
| isStorageUseDisabled | false | Om värdet är true lagras eller läss inte några data från lokal lagring och sessionslagring. Standardvärdet är false. |
| isBeaconApiDisabled | true | Om det är falskt skickar SDK all telemetri med [beacon-API:et](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Standard falskt. när fliken är stängd skickar SDK all återstående telemetri med [beacon-API:et](https://www.w3.org/TR/beacon) |
| sdkUtnämning | null | Anger sdk-tilläggets namn. Endast alfabetiska tecken är tillåtna. Tilläggets namn läggs till som ett prefix i taggen ai.internal.sdkVersion (till exempel "ext_javascript:2.0.0"). Standard är null. |
| ärBrowserLinkTrackingEnabled | false | Standardvärdet är false. Om värdet är true kommer SDK att spåra alla [begäranden om webbläsarlänk.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | null | AppId används för korrelationen mellan AJAX-beroenden som händer på klientsidan med begäranden på serversidan. När Beacon API är aktiverat kan det inte användas automatiskt, men kan ställas in manuellt i konfigurationen. Standard är null |
| enableCorsCorrelation | false | Om det är sant kommer SDK att lägga till två rubriker ("Request-Id" och "Request-Context") till alla CORS-begäranden för att korrelera utgående AJAX-beroenden med motsvarande begäranden på serversidan. Standard är falskt |
| namePrefix | Odefinierad | Ett valfritt värde som ska användas som namnpostfix för localStorage och cookie-namn.
| aktiveraAutoRouteTracking | false | Spåra dirigera ändringar automatiskt i SPA (Single Page Applications). Om värdet är true skickar varje ruttändring en ny sidvisning till Application Insights. Hash-ruttändringar`example.com/foo#bar`( ) registreras också som nya sidvisningar.
| aktiveraRequestHeaderTracking | false | Om sant spåras AJAX & Hämta begäranden spåras, är standardlösen falskt.
| aktiveraResponseHeaderTracking | false | Om sant, AJAX & Hämta begäran svar rubriker spåras, standard är falskt.
| distribuerasFåmode | `DistributedTracingModes.AI` | Ställer in läget för distribuerad spårning. Om AI_AND_W3C-läge eller W3C-läge har angetts genereras W3C-spårningskontexthuvuden (traceparent/tracestate) i alla utgående begäranden. AI_AND_W3C tillhandahålls för backkompatibilitet med äldre Application Insights-instrumenterade tjänster.

## <a name="single-page-applications"></a>Program med en sida

Som standard hanterar den här SDK:et **inte** tillståndsbaserad ruttväxling som sker i ensidiga program. Om du vill aktivera automatisk spårning av vägändringar för ditt ensidiga program kan du lägga `enableAutoRouteTracking: true` till i konfigurationen av din inställning.

För närvarande erbjuder vi en separat [React plugin](#react-extensions) som du kan initiera med denna SDK. Det kommer också att utföra ruttförändring spårning för dig, samt samla in [andra React specifik telemetri](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Reagera tillägg

| Tillägg |
|---------------|
| [Reagera](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagera inbyggt](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Utforska data på webbläsar-/klientsidan

Webbläsar-/klientdata kan visas genom att gå till **Mått** och lägga till enskilda mätvärden som du är intresserad av:

![](./media/javascript/page-view-load-time.png)

Du kan också visa dina data från JavaScript SDK via webbläsarupplevelsen i portalen.

Välj **Webbläsare** och välj sedan **Fel** eller **Prestanda**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Prestanda

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Beroenden

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analys

Om du vill fråga din telemetri som samlats in av JavaScript SDK väljer du knappen **Visa i loggar (Analytics).** Genom att `where` lägga `client_Type == "Browser"`till en sats av kommer du bara att se data från JavaScript SDK och all telemetri på serversidan som samlas in av andra SDK:er utesluts.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Stöd för källkarta

Den minifierade callstacken för ditt undantagstelemetri kan avminskas i Azure-portalen. Alla befintliga integreringar på panelen Undantagsinformation fungerar med den nyligen ovårdade callstacken.

#### <a name="link-to-blob-storage-account"></a>Länk till Blob-lagringskonto

Du kan länka din Application Insights-resurs till din egen Azure Blob Storage-behållare för att automatiskt avminska samtalsstaplar. Kom igång finns i [stöd för automatisk källkarta](./source-map-support.md).

### <a name="drag-and-drop"></a>Dra och släpp

1. Välj ett undantagstelemetriobjekt i Azure-portalen för att visa dess "Heltäckande transaktionsinformation"
2. Identifiera vilka källkartor som motsvarar den här anropsstacken. Källkartan måste matcha en stapelrams källfil, men suffix`.map`
3. Dra och släpp källmappningarna till anropsstacken i Azure-portalen![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Webbgrund för programinsikter

För en lätt upplevelse kan du istället installera den grundläggande versionen av Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Denna version levereras med det minsta antalet funktioner och funktioner och förlitar sig på dig att bygga upp det som du tycker passar. Till exempel utför det ingen autosamling (uncaught undantag, AJAX, etc.). API:erna för att skicka vissa `trackTrace` `trackException`telemetrityper, till exempel , etc., ingår inte i den här versionen, så du måste ange ditt eget omslag. Det enda API som `track`är tillgängligt är . Ett [prov](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) finns här.

## <a name="examples"></a>Exempel

Runnable exempel, se [Program Insights JavaScript SDK-exempel](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uppgradera från den gamla versionen av Application Insights

Bryta ändringar i SDK V2-versionen:
- För att möjliggöra bättre API-signaturer har vissa API-anrop, till exempel trackPageView och trackException, uppdaterats. Det går inte att köra i Internet Explorer 8 och tidigare versioner av webbläsaren.
- Telemetrikuvertet har fältnamn och strukturändringar på grund av uppdateringar av dataschema.
- `context.operation` Flyttad `context.telemetryTrace`till . Vissa fält ändrades`operation.id` --> `telemetryTrace.traceID`också ( ).
  - Om du vill uppdatera det aktuella sidvisnings-ID:t manuellt (till exempel i SPA-appar) använder du `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Om du vill att spårnings-ID:t ska vara unikt, där du tidigare använde, `Util.newId()`använder `Util.generateW3CId()`du nu . Båda hamnar i slutändan vara operation ID.

Om du använder de aktuella programinsikterna PRODUKTION SDK (1.0.20) och vill se om den nya SDK fungerar i körning, uppdaterar du URL:en beroende på ditt aktuella SDK-inläsningsscenario.

- Hämta via CDN-scenario: Uppdatera kodavsnittet som du för närvarande använder för att peka på följande URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm scenario: `downloadAndSetup` Ring för att ladda ner hela ApplicationInsights skriptet från CDN och initiera den med instrumenteringsnyckel:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Testa i intern miljö för att kontrollera att övervakningstelemetri fungerar som förväntat. Om allt fungerar uppdaterar du dina API-signaturer på lämpligt sätt till SDK V2-versionen och distribuerar i dina produktionsmiljöer.

## <a name="sdk-performanceoverhead"></a>SDK-prestanda/omkostnader

På bara 25 KB gzipped, och tar bara ~ 15 ms att initiera, Application Insights lägger till en försumbar mängd laddningstid till din webbplats. Med hjälp av kodavsnittet läses snabbt minimala komponenter i biblioteket in. Under tiden hämtas hela skriptet i bakgrunden.

Medan skriptet laddas ned från CDN ställs all spårning av sidan i kö. När det hämtade skriptet är klar asynkront initieras spåras alla händelser som köades. Som ett resultat kommer du inte att förlora någon telemetri under hela livscykeln på din sida. Den här installationsprocessen ger din sida ett sömlöst analyssystem som är osynligt för användarna.

> Sammanfattning:
> - **25 KB** gzipped
> - **15 ms** total initieringstid
> - **Noll** spårning missade under sidans livscykel

## <a name="browser-support"></a>Stöd för webbläsare

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome Senaste ✔ |  Firefox senaste ✔ | Dvs 9 + & Edge ✔ | Opera Senaste ✔ | Safari Senaste ✔ |

## <a name="open-source-sdk"></a>SDK med öppen källkod

Application Insights JavaScript SDK är öppen källkod för att visa källkoden eller för att bidra till projektet besök den [officiella GitHub-databasen](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Nästa steg
* [Spåra användning](usage-overview.md)
* [Anpassade händelser och mätvärden](api-custom-events-metrics.md)
* [Skapa – mät – lär](usage-overview.md)
