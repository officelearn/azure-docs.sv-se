---
title: 'Självstudier: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights | Microsoft Docs'
description: Läs mer om JavaScript-klientbiblioteket i Azure Time Series Insights och dess relaterade programmeringsmodell.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 7208c0d42cba0e7f04fc6876bf3ada9fa65a00d9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991445"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Självstudie: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights

JavaScript D3-baserade Azure Time Series Insights-klientbiblioteket har utvecklats för att hjälpa utvecklare webbfråga och visualisera data som lagras i Time Series Insights. Den här självstudien vägleder dig genom de Time Series Insights-klientbiblioteket och programmeringsmodell med hjälp av en värdbaserad exempelapp.

Självstudiekursen beskriver hur du arbetar med biblioteket, hur du kommer åt Time Series Insights-data och hur du använder diagramkontroller att rendera och visualisera data. Du får också lära dig att experimentera med olika typer av diagram om du vill visualisera data. I slutet av kursen kommer du att kunna använda klientbiblioteket för att införliva Time Series Insights-funktioner i web Apps.

Mer specifikt får du lära dig om:

> [!div class="checklist"]
> * Time Series Insights-exempelprogrammet
> * Time Series Insights JavaScript-klientbibliotek
> * Hur exempelprogrammet använder biblioteket för att visualisera Time Series Insights-data

> [!NOTE]
> * I självstudiekursen använder vi en kostnadsfri, värdbaserad [Time Series Insights web demo](https://insights.timeseries.azure.com/clientsample).
> * Källfilerna för Time Series Insights exempel app finns i den [GitHub-exempellagringsplats](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Läs den [referensdokumentation för Time Series Insights klienten](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Video

I den här videon får introducerar vi öppen källkod Time Series Insights JavaScript SDK:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien använder webbläsarens **utvecklarverktyg** funktionen. Moderna webbläsare ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), och andra) vanligtvis ger åtkomst till **Inspector webbvy** via F12-tangenten på tangentbordet. Ett annat sätt att få åtkomst till vyn är att högerklicka på en webbsida och sedan välja **inspektera elementet**.

## <a name="time-series-insights-sample-application"></a>Time Series Insights-exempelprogram

I den här självstudien använder vi en kostnadsfri, värdbaserad Time Series Insights-exempelapp att utforska källkoden bakom programmet och utforska Time Series Insights JavaScript-klientbiblioteket. Med hjälp av exempelappen, lär du dig att interagera med Time Series Insights i JavaScript och visualisera data med hjälp av tabeller och diagram.

1. Gå till den [Time Series Insights exempelprogrammet](https://insights.timeseries.azure.com/clientsample). Följande inloggningsuppmaning visas:

   [![Time Series Insights klienten exempel inloggningsuppmaning](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Välj **logga in** anger eller väljer du dina autentiseringsuppgifter. Använda en enterprise-organisationskonto (Azure Active Directory) eller ett personligt konto (Microsoft-konto).

   [![Time Series Insights-klientautentiseringsuppgifter exempel fråga](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. När du har loggat in visas en sida som visar diagram som fyllts med Time Series Insights-data. Ditt användarkonto och **Logga ut**-alternativet visas i det övre högra hörnet:

   [![Time Series Insights klienten exempel huvudsidan efter inloggning](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Sidkälla och struktur

Först ska vi visa den [HTML och JavaScript-källkoden](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) på den renderade webbsidan:

1. Öppna **Utvecklingsverktyg** i webbläsaren. Inspektera HTML-elementen som utgör den aktuella sidan (även kallade HTML- eller DOM-träd).

1. Expandera den `<head>` och `<body>` element och notera följande avsnitt:

   * Under den `<head>` element, hittar du sidan metadata och beroenden som möjliggör körning av appen:
     * En `<script>` element som används för att hänvisa till filen Azure Active Directory Authentication Library (ADAL) *adal.min.js*. ADAL är ett JavaScript-bibliotek som innehåller OAuth 2.0-autentisering (inloggning) och tokenförvärv för åtkomst till API:er.
     * Flera `<link>` -element för formatmallar (även kallat *CSS*) som *sampleStyles.css* och *tsiclient.css*. Formatmallarna styra visual formatering sidinformationen, till exempel färger, teckensnitt och avstånd.
     * En `<script>` element som används för att referera till Time Series Insights JavaScript-klientbiblioteket *tsiclient.js*. Sidan använder biblioteket att anropa API: er för Time Series Insights-tjänsten och att återge diagramkontroller på sidan.

     >[!NOTE]
     > * Källkoden för ADAL JavaScript-bibliotek finns i den [lagringsplatsen för azure-activedirectory-biblioteket-för-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Källkoden för Time Series Insights JavaScript-klientbiblioteket finns i den [tsiclient databasen](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Under den `<body>` element som du hittar `<div>` element, som kan hjälpa dig att definiera layouten för objekt på sidan och en annan `<script>` element:
     * Först `<div>` elementet anger den **logga in** dialogrutan (`id="loginModal"`).
     * Det andra `<div>`-elementet agerar som överordnat för:
       * Ett rubrik `<div>`-element som används för statusmeddelanden och inloggningsinformation längst upp på sidan (`class="header"`).
       * En `<div>` element för resten av sidan brödtext element, inklusive diagrammen (`class="chartsWrapper"`).
       * En `<script>` avsnitt som innehåller JavaScript som används för att styra sidan.

   [![Time Series Insights Client sample med utvecklarverktyg](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expandera den `<div class="chartsWrapper">` element och du hittar fler underordnade `<div>` element. Dessa element används för att placera varje diagramkontrollexempel. Det finns flera par `<div>` element, en för varje diagramexempel:

   * Det första (`class="rowOfCardsTitle"`) innehåller en beskrivande rubrik som sammanfattar vad diagrammen visar. Exempel: `Static Line Charts With Full-Size Legends.`
   * Andra (`class="rowOfCards"`) elementet är en överordnad som innehåller ytterligare underordnade `<div>` element som placera diagrammet kontroller i en rad.

   [![Div-innehållselement](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Expandera den `<script type="text/javascript">` element som är direkt nedan den `<div class="chartsWrapper">` element. I början av sidnivå JavaScript-avsnitt används för att hantera all logiken som sidan (autentisering, anropa Time Series Insights-tjänstens API: er, återgivning av diagramkontroller med mera):

   [![Brödtext skript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Time Series Insights JavaScript klienten biblioteket begrepp

Time Series Insights-klientbibliotek (*tsclient.js*) ger abstraktioner för två viktiga JavaScript-funktioner:

* **Omslutning metoder för att anropa API: er för Time Series Insights fråga**: REST API: er som du kan använda för att fråga efter Time Series Insights data med hjälp av mängduttryck. Metoderna som är ordnade under TsiClient.Server namnområdet för biblioteket.

* **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som du kan använda för att återge Time Series Insights aggregera data i en webbsida. Metoderna som är ordnade under TsiClient.UX namnområdet för biblioteket.

Via dessa förenklingar, kan utvecklare enkelt skapa diagram och diagram användargränssnittskomponenter som drivs med Time Series Insights-data.

### <a name="authentication"></a>Autentisering

Den [Time Series Insights exempelprogrammet](https://insights.timeseries.azure.com/clientsample) är ett enkelsidigt program som har stöd för autentisering av ADAL OAuth 2.0-användare:

1. När du använder ADAL för autentisering, måste klientappen registreras i Azure Active Directory (AD Azure). I själva verket enkelsidig app har registrerats för användning i [flöde beviljat med OAuth 2.0 implicit](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Programmet måste ange vissa av egenskaperna för registrering vid körning. Egenskaperna är klient-GUID (`clientId`) och omdirigerings-URI (`postLogoutRedirectUri`).
1. Senare, appen begär en *åtkomsttoken* från Azure AD. Åtkomst-token utfärdas för en begränsad uppsättning behörigheter för en viss tjänst eller en API-identifierare (https:\//api.timeseries.azure.com). Tokenbehörigheterna utfärdas åt den inloggade användaren. Identifieraren för tjänsten eller API är en annan egenskap som finns i appens Azure AD-registrering.
1. När ADAL returnerar åtkomsttoken till appen, den skickas som en *ägartoken* när den får tillgång till API: er för Time Series Insights-tjänsten.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Läs mer om stöd för Microsoft Azure AD-autentiseringsbibliotek i den [referensdokumentation för Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Kontrollidentifiering

I exemplet angivna `<div>` element är ordnade i överordnat `<body>` element att tillhandahålla en användningstyperna layout för diagrammet kontroller att rendera på sidan.

Varje `<div>` elementet anger egenskaper för placering och visuella attribut av diagramkontroller. HTML-element `id` egenskaper fungerar som unika identifierare för att binda till specifika kontroller för att rendera och uppdatera visualiseras data.

### <a name="aggregate-expressions"></a>Mängduttryck

Time Series Insights-klientbiblioteket API: er använder mängduttryck:

* Ett mängduttryck ger dig möjlighet att skapa en eller flera *söka villkoren*.

* Klienten API: er är utformad för att ge liknande funktionalitet till en annan demoapp (den [Time Series Insights explorer](https://insights.timeseries.azure.com/demo)), som använder Sök intervall `where` predikat, mått, och `splitBy` värden.

* De flesta klientbiblioteket använda API: er en matris med mängduttryck som tjänsten använder för att skapa en fråga för Time Series Insights-data.

### <a name="call-pattern"></a>Anropsmönster

Fylla och rendering diagramkontroller följer du allmänna mönster. Du kan se det allmänna mönstret i hela exempelappen och kan hjälpa dig när du använder klientbiblioteket:

1. Deklarera en `array` för en eller flera Time Series Insights-aggregeringsuttryck:

   ```javascript
   var aes =  [];
   ```

1. Skapa *1* till *n* aggregera uttryck objekt. Lägg sedan till dem i mängduttryck matrisen:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `predicateObject` | Filteruttryck för data |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Egenskapens namn på det mått som används | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Sammanställning av mått-egenskap som du vill använda | `['avg', 'min']` |
   | `searchSpan`      | Varaktighet och intervall storleken på mängduttrycket | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Egenskapen för anslutningssträngen som du vill dela efter (valfritt: kan vara null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Färgen på de objekt du vill rendera | `'pink'` |
   | `alias`           | Ett eget namn för mängduttrycket | `'Factory3Temperature'` |
   | `contextMenuActions` | En matris med åtgärder som ska bindas till time series-objekt i en visualisering (valfritt) | Mer information finns i [popup snabbmenyer](#pop-up-context-menus). |

1. Anropa en Time Series Insights-fråga med hjälp av the TsiClient.Server APIs begär aggregerade data:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `token`     | Åtkomsttoken för API: T för Time Series Insights |  `authContext.getTsiToken()`<br />Mer information finns i [Autentisering](#authentication). |
   | `envFQDN`   | Det fullständigt kvalificerade domännamnet (FQDN) för Time Series Insights-miljö | Från Azure-portalen. Till exempel: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | En matris med Time Series Insights-frågeuttryck | Använd den `aes` variabeln enligt beskrivningen ovan: `aes.map(function(ae){return ae.toTsx()}`. |

1. Omvandla det komprimerade resultat som returneras från Time Series Insights-fråga till JSON för visualisering:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Skapa en diagramkontroll med TsiClient.UX APIs. Binda till en av de `<div>` elementen på sidan:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Fyll i diagramkontrollen med de transformerade data JSON-objekt och rendering av kontrollen på sidan:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Rendera kontroller

Time Series Insights-klientbiblioteket innehåller åtta unika, out-of the box analys-kontroller:

* **Linjediagram**
* **Cirkeldiagram**
* **Liggande stapeldiagram**
* **Termisk karta**
* **hierarkin kontroller**
* **tillgängliga rutnät**
* **Diskret händelse tidslinjer**
* **tillstånd övergången tidslinjer**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Cirkeldiagram exempel, stapeldiagram och linjediagram

Titta på demon koden som används för att återge några standard diagramkontroller. Observera den programmeringsmodell och mönster för att skapa dessa kontroller. Mer specifikt undersöka HTML under den `// Example 3/4/5` kommentar som gör kontroller med HTML `id` värden `chart3`, `chart4`, och `chart5`.

Såsom anges i steg 3 i den [sidan käll- och struktur avsnittet](#page-source-and-structure) att diagramkontroller ordnas i rader på sidan. Varje diagramkontrollens har en beskrivande rubrik-rad. I det här exemplet är tre diagrammen fylls den `Multiple Chart Types From the Same Data` rubrik `<div>` element och den är bunden till tre `<div>` element som finns under rubriken:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Följande avsnitt i JavaScript-kod använder mönster som beskrevs tidigare: skapa Time Series Insights mängduttryck, använda dem för att fråga efter Time Series Insights-data och sedan återge tre diagrammen. Tre diagramtyper som används från namnområdet tsiClient.ux: `LineChart`, `BarChart`, och `PieChart`. Diagramtyperna används för att skapa och generera respektive diagram. Alla tre diagram kan använda samma mängduttryck data `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

De tre diagrammen visas på följande sätt vid renderingen:

[![Flera diagramtyper från samma data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Avancerade funktioner

Time Series Insights-klientbiblioteket har flera nya funktioner som du kan använda för att implementera datavisualiseringar fritt.

### <a name="states-and-events"></a>Tillstånd och händelser

En avancerade funktioner är möjligheten att lägga till tillståndsövergångar diskreta händelser i diagram. Den här funktionen är användbart för markering av kodsyntax incidenter, aviseringar och skapa tillstånd växlar (på/av växlar, till exempel).

Titta på den kod som omger den `// Example 10` kommentar. Koden visas med en rad kontroll under rubriken `Line Charts with Multiple Series Types` och Binder det till den `<div>` element med HTML `id` värdet `chart10`.

Följande steg beskriver processen:

1. En struktur med namnet `events4` har definierats för att lagra ändring av status-element för att spåra. Strukturen innehåller:

   * En strängnyckel som heter `Component States`.
   * En matris med värdeobjekt som representerar tillstånd. Varje objekt inkluderar:
     * En strängnyckel som innehåller en ISO JavaScript-tidsstämpel.
     * En matris som innehåller egenskaper för tillståndet: en färg och en beskrivning.

1. Den `events5` strukturen har definierats för `Incidents`, som innehåller en matris med händelse-element för att spåra. Matrisstrukturen är samma form som strukturen som beskrivs för `events4`.

1. Linjediagrammet återges och skickar parametrar i två strukturer med alternativen diagram: `events:` och `states:`. Observera alternativet parametrar för att ange en `tooltip:`, `theme:`, eller `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Anger tillståndsändringar romb markörer/pop-up-registreringen fönster som används för att ange incidenter och färgade staplar/pop-up-upp windows längs tidsskalan:

[![Linjediagram med flera serietyper](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Popup-snabbmenyer

En annan avancerade funktioner är möjligheten att skapa anpassade snabbmenyer (Högerklicka på eventuella snabbmenyer). Anpassade snabbmenyer är användbara för att aktivera åtgärder och logiska nästa steg inom omfånget för ditt program.

Titta på koden runt den `// Example 13/14/15` kommentar. Den här koden visas inledningsvis med ett linjediagram under rubriken `Line Chart with Context Menu to Create Pie/Bar Chart`. Diagrammet är bunden till den `<div>` element med HTML `id` värdet `chart13`.

Med hjälp av snabbmenyer, ger linjediagrammet möjlighet att dynamiskt skapa ett cirkel- och stapeldiagram som är bundet till `<div>`-elementen med ID `chart14` och `chart15`. Både cirkeldiagrammet och stapeldiagrammet också använda snabbmenyer för att aktivera sina egna funktioner: möjligheten att kopiera data från cirkeldiagrammet till liggande diagram och skriva ut data i stapeldiagram till konsolen webbläsarfönstret respektive.

Följande steg beskriver processen:

1. En serie med anpassade åtgärder definieras. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:

   * `barChartActions`: Den här åtgärden definierar snabbmenyn för cirkeldiagrammet, som innehåller ett element för att definiera ett enstaka objekt:
     * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
     * `action`: Åtgärden som är associerad med menyobjektet. Åtgärden är alltid en anonym funktion som tar tre argument som är baserade på mängduttrycket som används för att skapa diagrammet. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
       * `ae`: Mängduttrycksmatrisen.
       * `splitBy`: Den `splitBy` värde.
       * `timestamp`: Tidsstämpeln.

   * `pieChartActions`: Den här åtgärden definierar snabbmenyn för stapeldiagrammet som innehåller ett element för att definiera ett enskilt objekt. Forma och schemat är samma som den `barChartActions` element som beskrivs ovan, men `action` funktionen skiljer sig avsevärt: den skapar en instans av och renderas stapeldiagrammet. Den `ae` argument för att ange mängduttryck matrisen som skickas vid körning när menyalternativet öppnas. Funktionen anger också `ae.contextMenu`-egenskapen med `barChartActions`-snabbmenyn.
   * `contextMenuActions`: Den här åtgärden definierar snabbmenyn för linjediagram som innehåller tre element för att definiera tre menyalternativ. Forma och schemat för varje element är samma som de delar som beskrivs ovan. Precis som `barChartActions`-elementet, skriver det första objektet tre funktionsargument till konsolens webbläsarfönster. Liknar den `pieChartActions` element, de andra två objekt skapa en instans av och renderas cirkeldiagram och stapeldiagram, respektive. De andra två objekten anger också sina `ae.contextMenu`-egenskaper med snabbmenyerna `pieChartActions` och `barChartActions`.

1. Två mängduttryck namnen på de `aes` mängduttryck matris. De anger den `contextMenuActions` matris för varje objekt. Dessa uttryck används med linjediagramkontrollen.

1. Endast linjediagrammet återges först, där både cirkeldiagrammet och stapeldiagrammet kan återges vid körning.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

I följande skärmbild visas diagram med sina respektive popup snabbmenyer. Cirkeldiagram och stapeldiagram har skapats dynamiskt genom att använda menyalternativen för rad diagrammet kontext.

[![Linjediagram med snabbmenyn för att skapa cirkeldiagram och stapeldiagram](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Penslar

Du kan använda penslar för att definiera omfattningen av ett tidsintervall för att definiera åtgärder som att zooma och utforska.

Den kod som används för att illustrera penslar visas i den `Line Chart with Context Menu to Create Pie/Bar Chart` exempel som beskriver popup snabbmenyer.

* Pensel åtgärder liknar en snabbmeny i att de definierar en serie av anpassade åtgärder för penseln. Varje åtgärd innehåller en matris som har ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:
   * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
   * `action`: Den åtgärd som är associerad med menyalternativet som alltid är en anonym funktion som tar två argument. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
     * `fromTime`: Den `from` tidsstämpeln för pensel valet.
     * `toTime`: Den `to` tidsstämpeln för pensel valet.

* Penselåtgärder läggs till som en annan diagramalternativegenskap. Den `brushContextMenuActions: brushActions` egenskap skickas till den `linechart.Render` anropa.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Linjediagram med snabbmenyn för att skapa cirkeldiagram och stapeldiagram med hjälp av penslar](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Logga in och utforska Time Series Insights-exempelprogrammet och dess källa
> * Använda API: er i Time Series Insights JavaScript-klientbibliotek
> * Använda JavaScript för att skapa och fylla i diagramkontroller med Time Series Insights-data

Time Series Insights-exempelprogrammet använder en demo-datauppsättning. Om du vill veta hur du kan skapa din egen Time Series Insights-miljö och en datauppsättning, finns i följande artikel:

> [!div class="nextstepaction"]
> [Självstudie: Skapa en Azure Time Series Insights-miljö](tutorial-create-populate-tsi-environment.md)

Alternativt kan du visa Time Series Insights exempel appens källfiler:

> [!div class="nextstepaction"]
> [Databasen för Time Series Insights exempelappen](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Läs API-referensdokumentation för Time Series Insights-klienten:

> [!div class="nextstepaction"]
> [Time Series Insights API-referensdokumentation](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
