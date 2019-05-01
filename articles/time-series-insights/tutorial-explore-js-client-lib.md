---
title: 'Självstudier: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights | Microsoft Docs'
description: Läs mer om JavaScript-klientbiblioteket i Azure Time Series Insights och dess relaterade programmeringsmodell.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: a91afdbeaa2ced37b237b4f2b80a8dbbe2c4a05c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717220"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Självstudie: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights

Vi har utvecklat ett JavaScript D3-baserat TSI-klientbibliotek för att hjälpa utvecklare när de frågar efter och visualiserar data som lagras i Time Series Insights (TSI). Den här självstudien vägleder dig genom de TSI-klientbiblioteket och programmeringsmodell med hjälp av en värdbaserad exempelapp.

Självstudiekursen beskriver hur du arbetar med åtkomst till TSI data och använda diagramkontroller att rendera och visualisera data. Du får också lära dig att experimentera med olika typer av diagram om du vill visualisera data. Vid slutet av självstudien kommer du att kunna använda klientbiblioteket för att införliva TSI-funktioner i web Apps.

Mer specifikt får du lära dig om:

> [!div class="checklist"]
> * TSI-exempelprogrammet.
> * JavaScript-klientbiblioteket i TSI.
> * Hur exempelprogrammet använder biblioteket för att visualisera TSI-data.

> [!NOTE]
> * I självstudiekursen använder vi en kostnadsfri, värdbaserad, [Time Series Insights web demo](https://insights.timeseries.azure.com/clientsample).
> * Källfilerna för Time Series Insights exempel app finns i den [GitHub-exempellagringsplats](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

## <a name="video"></a>Video

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>I den här videon introducerar vi Time Series Insights JavaScript SDK:n med öppen källkod.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien använder webbläsarens **utvecklarverktyg** funktionen. Moderna webbläsare ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/), och andra) vanligtvis ger åtkomst till **Inspector webbvy** via den `F12` tangenten. I annat fall hittar du det genom att högerklicka på en webbsida och välja **inspektera elementet**.

## <a name="time-series-insights-sample-application"></a>Time Series Insights-exempelprogram

I den här självstudien används en kostnadsfri, värdbaserad, Time Series Insights-exempelapp för att utforska källkoden bakom programmet och TSI JavaScript-klientbiblioteket. Via det lär du dig att interagera med TSI i JavaScript och visualisera data med hjälp av tabeller och diagram.

1. Gå till [Exempelprogrammet Time Series Insights](https://insights.timeseries.azure.com/clientsample). Följande inloggningsprompten visas:

   [![TSI-klienten exempel inloggningsuppmaning](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Välj **logga in** anger eller väljer du dina autentiseringsuppgifter. Använda en enterprise-organisationskonto (Azure Active Directory) eller ett personligt konto (Account eller MSA).

   [![TSI exempel klientautentiseringsuppgifter fråga](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. När du loggar in visas en sida med flera olika typer av diagram som fyllts med data i TSD. Ditt användarkonto och **Logga ut**-alternativet visas i det övre högra hörnet:

   [![TSI-klienten exempel huvudsidan efter inloggning](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Sidkälla och struktur

<div id="page-source-and-structure"></div>

Först ska vi visa den [HTML och JavaScript-källkoden](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) renderade webb-växlat minne:

1. Öppna **Utvecklingsverktyg** i webbläsaren. Inspektera HTML-elementen som utgör den aktuella sidan (även kallade HTML- eller DOM-träd).

1. Expandera den `<head>` och `<body>` element och notera följande avsnitt:

   * Under den `<head>` element, hittar du sidan metadata och beroenden som möjliggör körning av appen:
     * Ett `<script>`-element som används för att referera till Azure Active Directory Authentication Library **adal.min.js** (även kallat ADAL). ADAL är ett JavaScript-bibliotek som innehåller OAuth 2.0-autentisering (inloggning) och tokenförvärv för åtkomst till API:er.
     * Flera `<link>`-element för formatmallar (även kallat CSS) som **sampleStyles.css** och **tsiclient.css**. Formatmallarna används för att styra visuell formatmallsinformation för sidan, till exempel färger, teckensnitt, avstånd och så vidare.
     * Ett `<script>`-element som används för att referera till TSI-klientens JavaScript-bibliotek **tsiclient.js**. Biblioteket används av sidan för att anropa TSI-tjänstens API:er och rendera diagramkontrollerna på sidan.

     >[!NOTE]
     > * Källkoden för JavaScript-biblioteket i ADAL är tillgänglig i [azure-activedirectory-library-for-js repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Källkoden för TSI-klientens JavaScript-bibliotek är tillgänglig i [tsiclient repository](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * Under den `<body>` element som du hittar `<div>` element, som kan hjälpa dig att definiera layouten för objekt på sidan och en annan `<script>` element:
     * Det första `<div>`-elementet anger **Inloggnings**-dialogrutan (`id="loginModal"`).
     * Det andra `<div>`-elementet agerar som överordnat för:
       * Ett rubrik `<div>`-element som används för statusmeddelanden och inloggningsinformation längst upp på sidan (`class="header"`).
       * Ett `<div>`-element för resten av sidans brödtextelement, inklusive alla diagram (`class="chartsWrapper"`).
       * Ett `<script>`-avsnitt som innehåller alla JavaScript som används för att styra sidan.

   [![TSI-klientexempel med utvecklarverktyg](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expandera den `<div class="chartsWrapper">` element och du hittar fler underordnade `<div>` element. Dessa element används för att placera varje diagramkontrollexempel. Observera att det finns flera par `<div>`-element, ett för varje diagramexempel:

   * Det första (`class="rowOfCardsTitle"`) innehåller en beskrivande rubrik som sammanfattar vad diagrammen visar. Exempel: `Static Line Charts With Full-Size Legends.`
   * Det andra (`class="rowOfCards"`) elementet är överordnat och innehåller ytterligare underordnade `<div>`-element som placerar de faktiska diagramkontrollerna inom en rad.

   [![Div-innehållselement](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Expandera nu `<script type="text/javascript">`-elementet som finns direkt under `<div class="chartsWrapper">`-elementet. Lägg märke till början av avsnittet sidnivå JavaScript som används för att hantera all logiken som sidan (autentisering, anropa TSI-tjänstens API: er, återgivning av diagramkontroller med mera):

   [![Brödtext skript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>TSI JavaScript-klientbibliotekskoncept

TSI-klientbibliotek (**tsclient.js**) ger abstraktioner för två viktiga JavaScript-funktioner:

* **Omslutningsmetoder för att anropa fråge-API:er för TSI**: REST-API:er som gör det möjligt att fråga efter TSI-data med hjälp av sammansatta uttryck. Metoderna organiseras under `TsiClient.Server`-namnområdet för biblioteket.

* **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som används för att återge insamlade TSI-data på en webbsida. Metoderna organiseras under `TsiClient.UX`-namnområdet för biblioteket.

Tack vare dessa förenklingar bygga utvecklare diagram och diagram användargränssnittskomponenter som drivs med TSI data enklare.

### <a name="authentication"></a>Autentisering

Den [Time Series Insights exempelprogrammet](https://insights.timeseries.azure.com/clientsample) är en ensidesapp med ADAL OAuth 2.0 stöd för autentisering av användare:

1. När du använder ADAL för autentisering, måste klientappen registreras i Azure Active Directory. I själva verket enkelsidig app har registrerats för användning i [flöde beviljat med OAuth 2.0 implicit](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Programmet måste därför ange vissa av egenskaperna för registrering vid körning. Dessa inkluderar klient-GUID (`clientId`) och omdirigerings-URI (`postLogoutRedirectUri`).
1. Senare, appen begär en **åtkomsttoken** från Azure Active Directory. Åtkomst-token utfärdas för en begränsad uppsättning behörigheter för en specifik tjänst/API-identifierare (`https://api.timeseries.azure.com`). Tokenbehörigheterna utfärdas åt den inloggade användaren. Identifieraren för tjänsten/API är en annan egenskap som finns i appregistreringen Azure Active Directory.
1. När ADAL returnerar åtkomsttoken till appen, den skickas som en **ägartoken** om åtkomst till TSD tjänstens API: er.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

### <a name="control-identification"></a>Kontrollidentifiering

I exemplet angivna `<div>` element är ordnade i överordnat `<body>` element att tillhandahålla en användningstyperna layout för alla diagramkontroller återges på sidan.

Varje `<div>` elementet anger egenskaper för placering och visuella attribut av diagramkontroller. HTML-element `id` egenskaper fungerar som unika identifierare för att binda till specifika kontroller för rendering och uppdatera visualiserade data.

### <a name="aggregate-expressions"></a>Mängduttryck

TSI-klientbiblioteket API: er använder mängduttryck:

* Ett mängduttryck ger dig möjlighet att skapa en eller flera **söka villkoren**.

* Klient-API: er är utformad för att ge liknande funktionalitet till en annan ange demoapp (den [Time Series Insights explorer](https://insights.timeseries.azure.com/demo)), som använder sökintervallet, where-predikat, mått och delning av värdet.

* De flesta av klientbiblioteket API: er tar en lista med mängduttryck som används av tjänsten för att skapa en fråga för TSI-data.

### <a name="call-pattern"></a>Anropsmönster

Fylla och rendering diagramkontroller följer du allmänna mönster. Den här allmänna mönster observeras i hela exempelappen och hjälper dig när du använder klientbiblioteket:

1. Deklarerar en `array` för att hålla ett eller flera TSI-mängduttryck:

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
   | `predicateObject` | Filteruttryck data. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Egenskapsnamn för det mått som används. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Önskade sammansättningar för måttegenskapen. | `['avg', 'min']` |
   | `searchSpan`      | Mängduttryckets varaktighet och intervallstorlek. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Strängegenskapen som du vill dela med (valfritt – kan vara null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Färgen på de objekt som du vill rendera. | `'pink'` |
   | `alias`           | Ett eget namn på mängduttrycket. | `'Factory3Temperature'` |
   | `contextMenuActions` | En matris med åtgärder som ska bindas till tidsserieobjekten i en visualisering (valfritt). | Mer information finns i avsnittet [popup snabbmenyer](#contextMenu) |

1. Anropa en TSI-fråga med hjälp av `TsiClient.Server` API:er för att begära sammanställda data:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `token`     | Åtkomsttoken för TSI API:n. |  `authContext.getTsiToken()` Mer information finns i [autentiseringsavsnittet.](#authentication) |
   | `envFQDN`   | Det fullständigt kvalificerade domännamnet (FQDN) för TSI-miljön. | Från Azure-portalen, till exempel: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | En matris med TSI-frågeuttryck. | Använd `aes`-variabeln som det beskrevs tidigare: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transformera det komprimerade resultatet som returnerades från TSI-frågan till JSON för visualisering:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Skapa en diagramkontroll med `TsiClient.UX`-API:er och bind den till ett av `<div>`-elementen på sidan:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Fyll i diagramkontrollen med de transformerade JSON-dataobjekten och rendera den kontrollen på sidan:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderingskontroller

TSI-klientbiblioteket innehåller åtta unika, out-of the box, analys-kontroller:

* **Linjediagram**
* **Cirkeldiagram**
* **Liggande stapeldiagram**
* **Termisk karta**
* **hierarkin kontroller**
* **tillgängliga rutnät**
* **Diskret händelse tidslinjer**
* **tillstånd övergången tidslinjer**

### <a name="line-bar-pie-chart-examples"></a>Exempel på linje-, stapel- och cirkeldiagram

Titta på demo-koden som används för att återge några av standard diagramkontrollen. Observera den programmeringsmodell och mönster för att skapa dessa kontroller. Mer specifikt granska avsnittet i HTML under den `// Example 3/4/5` kommentar som gör kontroller med HTML `id` värden `chart3`, `chart4`, och `chart5`.

Såsom anges i **steg 3** av den [sidan käll- och struktur avsnittet](#page-source-and-structure) att diagramkontroller ordnas i rader på sidan som har en beskrivande rubrik-rad. I det här exemplet är tre diagrammen fylls under rubriken `"Multiple Chart Types From the Same Data"` `<div>` element och är bunden till tre `<div>` element som finns under rubriken:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Följande avsnitt av JavaScript-koden använder mönster som beskrevs tidigare för att skapa TSI-mängduttryck, använda dem för att fråga efter TSI-data och rendera de tre diagrammen. Observera de tre typerna som används av `tsiClient.ux`-namnområdet, `LineChart`, `BarChart` och `PieChart` för att skapa och rendera respektive diagram. Observera även att alla tre diagram kan använda samma data i mängduttrycket `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

De tre diagrammen visas på följande sätt vid renderingen:

[![Flera diagramtyper från samma data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Avancerade funktioner

TSI-klientbiblioteket har flera nya funktioner som du kan använda för att implementera datavisualiseringar fritt.

### <a name="states-and-events"></a>Tillstånd och händelser

En avancerade funktioner är möjligheten att lägga till tillståndsövergångar diskreta händelser i diagram. Den här funktionen är användbart för markering av kodsyntax incidenter, aviseringar och skapa tillstånd växlar (på/av växlar till exempel).

Titta på den kod som omger den `// Example 10` kommentar. Koden visas med en rad kontroll under rubriken `"Line Charts with Multiple Series Types"`, och Binder det till den `<div>` element med HTML `id` värdet `chart10`.

1. Först måste en struktur med namnet `events4` har definierats för att lagra ändring av status-element för att spåra. Strukturen innehåller:

   * En strängnyckel som heter `Component States`.
   * En matris med värdeobjekt som representerar tillstånd. Varje objekt inkluderar:
     * En strängnyckel som innehåller en ISO JavaScript-tidsstämpel.
     * En matris som innehåller egenskaper för tillståndet: en färg och en beskrivning.

2. Därefter den `events5` strukturen har definierats för `Incidents`, som innehåller en matris med händelse-element för att spåra. Matrisstrukturen är samma form som strukturen som beskrivs för `events4`.

3. Slutligen renderas linjediagrammet och de två strukturerna med diagramalternativparametrarna skickas in: `events:` och `states:`. Lägg märke till de andra alternativparametrarna för att ange en `tooltip:`, `theme:` eller `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visuellt, anger de rombformade markörerna/popup-fönstren som används för att ange incidenter och de färgade staplarna/popup-föntren längs tidsskalan indikerar tillståndsändringar:

[![Linjediagram med flera serietyper](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Popup-snabbmenyer

<div id="contextMenu"></div>

En annan avancerade funktioner är möjligheten att skapa anpassade snabbmenyer (Högerklicka på eventuella snabbmenyer). Anpassade snabbmenyer är användbara för att aktivera åtgärder och logiska nästa steg inom omfånget för ditt program.

Letar du kod i `// Example 13/14/15` kommentar. Den här koden visas inledningsvis med ett linjediagram under rubriken `"Line Chart with Context Menu to Create Pie/Bar Chart"` och diagrammet är bunden till den `<div>` element med HTML `id` värdet `chart13`.

Med hjälp av snabbmenyer, ger linjediagrammet möjlighet att dynamiskt skapa ett cirkel- och stapeldiagram som är bundet till `<div>`-elementen med ID `chart14` och `chart15`. Dessutom använder både cirkel- och stapeldiagram snabbmenyer till att aktivera sina egna funktioner: t.ex. möjligheten att kopiera data från cirkeldiagrammet till stapeldiagrammet, samt att skriva ut stapeldiagramdata till webbläsarens konsolfönster.

1. Först definieras en serie med anpassade åtgärder. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:

   * `barChartActions`: Den här åtgärden definierar snabbmenyn för cirkeldiagrammet, som innehåller ett element för att definiera ett enstaka objekt:
     * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
     * `action`: Åtgärden som är associerad med menyobjektet. Åtgärden är alltid en anonym funktion som tar tre argument som är baserade på mängduttrycket som används för att skapa diagrammet. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
       * `ae`: Mängduttrycksmatrisen.
       * `splitBy`: Den `splitBy` värde.
       * `timestamp`: Tidsstämpeln.

   * `pieChartActions`: Den här åtgärden definierar snabbmenyn för stapeldiagrammet som innehåller ett element för att definiera ett enskilt objekt. Form och schema är samma som det föregående `barChartActions`-elementet, men observera att `action`-funktionen skiljer sig åt avsevärt: den instantierar och återger stapeldiagrammet. Observera att `ae`-argumentet används för att ange mängduttrycksmatrisen som skickades vid körning när menyalternativet öppnas. Funktionen anger också `ae.contextMenu`-egenskapen med `barChartActions`-snabbmenyn.
   * `contextMenuActions`: Den här åtgärden definierar snabbmenyn för linjediagram som innehåller tre element för att definiera tre menyalternativ. Form och schema för varje element är samma som de tidigare elementen som beskrevs. Precis som `barChartActions`-elementet, skriver det första objektet tre funktionsargument till konsolens webbläsarfönster. Precis som `pieChartActions`-elementet, instantierar och renderar de andra två objekten cirkel- respektive stapeldiagram. De andra två objekten anger också sina `ae.contextMenu`-egenskaper med snabbmenyerna `pieChartActions` och `barChartActions`.

2. Därefter pushas två mängduttryck till `aes`-sammanställda uttrycksmatrisen och anger `contextMenuActions`-matrisen för varje objekt. Dessa uttryck används med linjediagramkontrollen.

3. Slutligen renderas endast linjediagrammet, som både cirkel- och stapeldiagrammen kan renderas från vid körning.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Skärmbilden visar diagram med deras respektive popup-snabbmenyer. Cirkel- och stapeldiagrammen skapades dynamiskt med menyalternativen för linjediagrammen.

[![Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Penslar

Penslar används för att omfatta ett tidsintervall för att definiera åtgärder som zooma och utforska.

Den kod som används för att illustrera penslar visas i föregående exempel för ”Linjediagram med kontextmeny för att skapa cirkel-/stapeldiagram” som beskriver popup-snabbmenyer.

1. Pensel åtgärder liknar en snabbmeny i att de definierar en serie av anpassade åtgärder för penseln. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:
   * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
   * `action`: Den åtgärd som är associerad med menyalternativet som alltid är en anonym funktion som tar två argument. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
      * `fromTime`: Den `from` tidsstämpeln för pensel valet.
      * `toTime`: Den `to` tidsstämpeln för pensel valet.

2. Penselåtgärder läggs till som en annan diagramalternativegenskap. Observera `brushContextMenuActions: brushActions`-egenskapen som överförs till `linechart.Render`-anropet.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Linjediagram med snabbmenyn för att skapa cirkeldiagram/stapeldiagram med penslar](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Logga in och utforska exempelprogrammet TSI och dess källa.
> * Använd API:er i TSI JavaScript-klientbiblioteket.
> * Använd JavaScript för att skapa och fylla i diagramkontrollerna med TSI-data.

Enligt, använder TSI exempelprogrammet en demo-datauppsättning. Om du vill veta hur du kan skapa din egen TSI-miljö och datamängd, fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Självstudie: Skapa en Azure Time Series Insights-miljö](tutorial-create-populate-tsi-environment.md)

Eller visa TSI exempel appens källfiler:

> [!div class="nextstepaction"]
> [TSI databasen för exempelappen](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)
