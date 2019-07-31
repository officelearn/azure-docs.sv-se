---
title: 'Självstudier: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights | Microsoft Docs'
description: Självstudie för att lära dig om klient biblioteket för Azure Time Series Insights Java Script och den relaterade programmerings modellen.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 2c1013829a7e6e94712e22ae235a47490c8c5eaa
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677669"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Självstudier: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights

Java Script D3-baserade Azure Time Series Insights klient biblioteket utvecklades för att hjälpa webbutvecklare att fråga och visualisera data som lagrats i Time Series Insights. Den här självstudien vägleder dig genom Time Series Insights klient bibliotek och programmerings modell med hjälp av en värdbaserad exempel App.

I självstudien lär du dig hur du arbetar med biblioteket, hur du kommer åt Time Series Insights data och hur du använder diagram kontroller för att återge och visualisera data. Du lär dig också hur du experimenterar med olika typer av grafer för att visualisera data. I slutet av självstudien kommer du att kunna använda klient biblioteket för att inkludera Time Series Insights-funktioner i din webbapp.

Mer specifikt får du lära dig om:

> [!div class="checklist"]
> * Exempel programmet Time Series Insights
> * Klient biblioteket för Time Series Insights Java Script
> * Hur exempel programmet använder biblioteket för att visualisera Time Series Insights data

> [!NOTE]
> * Självstudien använder en kostnads fri, värdbaserad [Time Series Insights webb demonstration](https://insights.timeseries.azure.com/clientsample).
> * Källfilerna för Time Series Insights exempel-appen finns i [GitHub-exempel lagrings platsen](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Läs [referens dokumentationen för Time Series Insights-klienten](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Förutsättningar

* I den här självstudien används webbläsarens **utvecklarverktyg** funktion. Moderna webbläsare ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)och andra) ger vanligt vis åtkomst till webbkontrollanten via F12-snabb tangenten på tangent bordet. Ett annat sätt att komma åt vyn är att högerklicka på en webb sida och sedan välja **Undersök element**.

## <a name="video"></a>Video

I den här videon introducerar vi Time Series Insights Java Script SDK med öppen källkod:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="time-series-insights-sample-application"></a>Time Series Insights-exempelprogram

I den här självstudien använder vi en kostnads fri, värdbaserad Time Series Insights exempel-app för att utforska käll koden bakom programmet och för att utforska Time Series Insights Java Script-klient bibliotek. Genom att använda exempel appen får du lära dig hur du interagerar med Time Series Insights i Java Script och visualiserar data genom diagram och diagram.

1. Gå till [Time Series Insights exempel program](https://insights.timeseries.azure.com/clientsample). Följande meddelande om inloggning visas:

   [![Prompt för Time Series Insights klient exempel inloggning](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Välj **Logga** in för att ange eller Välj dina autentiseringsuppgifter. Använd antingen ett företags organisations konto (Azure Active Directory) eller ett personligt konto (Microsoft-konto).

   [![Fråga om Time Series Insights klient exempel referenser](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. När du har loggat in visas en sida med diagram som är ifyllda med Time Series Insights data. Ditt användarkonto och **Logga ut**-alternativet visas i det övre högra hörnet:

   [![Exempel på huvud sida för Time Series Insights-klient efter inloggning](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Sidkälla och struktur

Först ska vi visa [HTML-och JavaScript-källkoden](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) för den återgivna webb sidan:

1. Öppna **Utvecklingsverktyg** i webbläsaren. Inspektera HTML-elementen som utgör den aktuella sidan (även kallade HTML- eller DOM-träd).

1. Expandera- `<body>` och-elementen och Observera följande avsnitt: `<head>`

   * `<head>` Under elementet hittar du sidans metadata och beroenden som gör att appen kan köras:
     * Ett `<script>` element som används för att referera till ADAL-filen (Azure Active Directory Authentication Library) *ADAL. min. js*. ADAL är ett JavaScript-bibliotek som innehåller OAuth 2.0-autentisering (inloggning) och tokenförvärv för åtkomst till API:er.
     * Flera `<link>` element för formatmallar (kallas även *CSS*) som *sampleStyles. CSS* och *tsiclient. CSS*. Formatmallarna styr information om sid format för visuella sidor, till exempel färger, teckensnitt och avstånd.
     * Ett `<script>` element som används för att referera till Time Series Insights JavaScript-klientcertifikatet *tsiclient. js*. Sidan använder-biblioteket för att anropa Time Series Insights tjänst-API: er och återge diagram kontroller på sidan.

     >[!NOTE]
     > * Käll koden för ADAL JavaScript-biblioteket finns i [databasen Azure-ActiveDirectory-Library-for-JS](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Käll koden för klient biblioteket för Time Series Insights java script är tillgänglig i [tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)-lagringsplatsen.

   * Under elementet hittar `<div>` du element, som hjälper dig att definiera layouten för objekt på sidan och ett annat `<script>` element: `<body>`
     * Det första `<div>` elementet anger dialog rutan **Logga** in (`id="loginModal"`).
     * Det andra `<div>`-elementet agerar som överordnat för:
       * Ett rubrik `<div>`-element som används för statusmeddelanden och inloggningsinformation längst upp på sidan (`class="header"`).
       * Ett `<div>` element för resten av sidans text element, inklusive diagram (`class="chartsWrapper"`).
       * Ett `<script>` avsnitt som innehåller det JavaScript-skript som används för att kontrol lera sidan.

   [![Time Series Insights klient exempel med Utvecklarverktyg](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Expandera elementet och Sök efter fler underordnade `<div>` element. `<div class="chartsWrapper">` Dessa element används för att placera varje diagramkontrollexempel. Det finns flera par `<div>` element, en för varje diagram exempel:

   * Det första (`class="rowOfCardsTitle"`) innehåller en beskrivande rubrik som sammanfattar vad diagrammen visar. Exempel: `Static Line Charts With Full-Size Legends.`
   * Det andra (`class="rowOfCards"`)-elementet är en överordnad som `<div>` innehåller ytterligare underordnade element som placerar de faktiska diagram kontrollerna i en rad.

   [![Div-element för brödtext](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Expandera det `<script type="text/javascript">` element som är direkt `<div class="chartsWrapper">` under elementet. Början av JavaScript-avsnittet på sidnivå används för att hantera all sid logik (autentisering, anropa Time Series Insights tjänst-API: er, återge diagram kontrollerna med mera):

   [![Body-skript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Time Series Insights JavaScript-koncept för klient bibliotek

Time Series Insights klient biblioteket (*tsiclient. js*) tillhandahåller abstraktioner för två viktiga JavaScript-funktioner:

* **Omslutnings metoder för att anropa API: er för Time Series Insights frågor**: REST API: er som du kan använda för att fråga efter Time Series Insights data genom att använda mängd uttryck. Metoderna är ordnade under TsiClient. Server namespace i biblioteket.

* **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som du kan använda för att rendera Time Series Insights aggregera data på en webb sida. Metoderna är ordnade under TsiClient. UX-namnrymden i biblioteket.

Med hjälp av dessa förenklingar kan utvecklare enklare bygga GRÄNSSNITTs diagram och diagram komponenter som är baserade på Time Series Insights data.

### <a name="authentication"></a>Authentication

[Exempel programmet Time Series Insights](https://insights.timeseries.azure.com/clientsample) är en app med en enda sida som har ADAL OAuth 2,0-stöd för användarautentisering:

1. När du använder ADAL för autentisering måste klient appen registreras i Azure Active Directory (Azure AD). I själva verket är den enda sidan registrerad för att använda det [implicita tilldelnings flödet OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Programmet måste ange några av registrerings egenskaperna vid körning. Egenskaperna inkluderar klientens GUID (`clientId`) och omdirigerings-URI (`postLogoutRedirectUri`).
1. Senare begär appen en *åtkomsttoken* från Azure AD. Åtkomsttoken utfärdas för en begränsad uppsättning behörigheter för en angiven tjänst eller API-identifierare (https:\//API.timeseries.Azure.com). Tokenbehörigheterna utfärdas åt den inloggade användaren. Identifieraren för tjänsten eller API: t är en annan egenskap som finns i appens Azure AD-registrering.
1. När ADAL returnerar åtkomsttoken till appen skickas den som en Bearer-token när den får åtkomst till Time Series Insights tjänst-API: er.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Mer information om Azure AD Authentication-bibliotek som stöds av Microsoft finns i [referens dokumentationen för Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Kontrollidentifiering

I det här exemplet `<div>` är elementen ordnade i det överordnade `<body>` elementet för att tillhandahålla en lämpliga layout för de diagram kontroller som återges på sidan.

Varje `<div>` -element anger egenskaper för placerings-och visuella attribut för diagram kontroller. Egenskaper för `id` HTML-element fungerar som unika identifierare för att binda till specifika kontroller för att återge och uppdatera visualiserade data.

### <a name="aggregate-expressions"></a>Mängduttryck

Time Series Insights klient biblioteks-API: er använder mängd uttryck:

* Ett agg regerings uttryck ger möjlighet att konstruera en eller flera *Sök villkor*.

* Klient-API: erna är utformade för att ge liknande funktionalitet till en annan demo app ( [Time Series Insights Explorer](https://insights.timeseries.azure.com/demo)), som använder `where` Sök omfång, predikat, `splitBy` mått och värden.

* De flesta klient biblioteks-API: er tar en matris med mängd uttryck som tjänsten använder för att skapa en Time Series Insights data fråga.

### <a name="call-pattern"></a>Anropsmönster

Att fylla i och återge diagram kontroller följer ett allmänt mönster. Du kan studera det allmänna mönstret i exempel programmet och kan hjälpa dig när du använder klient biblioteket:

1. Deklarera en `array` som ska innehålla ett eller flera Time Series Insights agg regerings uttryck:

   ```javascript
   var aes =  [];
   ```

1. Build *1* till *n* agg regerings uttrycks objekt. Lägg sedan till dem i matrisen för sammanställda uttryck:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `predicateObject` | Data filtrerings uttrycket |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Egenskaps namnet för det mått som används | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agg regeringar för den mått egenskap som du vill använda | `['avg', 'min']` |
   | `searchSpan`      | Längd och intervall storlek för mängd uttrycket | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Den sträng egenskap som du vill dela efter (valfritt: kan vara null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Färgen på de objekt som du vill återge | `'pink'` |
   | `alias`           | Ett eget namn för mängd uttrycket | `'Factory3Temperature'` |
   | `contextMenuActions` | En matris med åtgärder som ska bindas till Time Series-objekt i en visualisering (valfritt) | Mer information finns i [snabb menyer för popup-fönster](#pop-up-context-menus). |

1. Anropa en Time Series Insights fråga med hjälp av API: erna TsiClient. Server för att begära sammanställda data:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `token`     | Åtkomsttoken för Time Series Insights API |  `authContext.getTsiToken()`<br />Mer information finns i [Autentisering](#authentication). |
   | `envFQDN`   | Det fullständigt kvalificerade domän namnet (FQDN) för Time Series Insightss miljön | Från Azure Portal. Till exempel: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | En matris med Time Series Insights frågeuttryck | Använd variabeln som beskrivits `aes.map(function(ae){return ae.toTsx()}`tidigare:. `aes` |

1. Transformera det komprimerade resultatet som returneras från Time Series Insights-frågan till JSON för visualisering:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Skapa en diagram kontroll med hjälp av API: erna TsiClient. UX. Bind det till ett av `<div>` elementen på sidan:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Fyll i diagram kontrollen med transformerade JSON-dataobjekt och återge kontrollen på sidan:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Återge kontroller

Time Series Insights klient biblioteket innehåller åtta unika analys kontroller direkt:

* **linje diagram**
* **cirkel diagram**
* **stapeldiagram**
* **termisk karta**
* **hierarkiska kontroller**
* **tillgängligt rutnät**
* **tids linjer för diskret händelse**
* **tids linjer för tillstånds över gång**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Exempel på linje diagram, liggande diagram och cirkel diagram

Titta på demo koden som används för att återge några av standard kontrollerna i diagrammet. Anteckna programmerings modellen och mönster för att skapa dessa kontroller. Titta närmare på HTML-koden under `// Example 3/4/5` kommentaren, som återger kontroller med HTML `id` - `chart3`värden `chart4`, och `chart5`.

Kom ihåg från steg 3 i [avsnittet sid källa och struktur](#page-source-and-structure) om att diagram kontrollerna är ordnade i rader på sidan. Varje diagram kontroll har en beskrivande rubrik rad. I det här exemplet fylls de tre diagrammen under `Multiple Chart Types From the Same Data` rubrik `<div>` elementet och binds till de tre `<div>` element som är under rubriken:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

I följande avsnitt av JavaScript-kod används mönster som beskrivs tidigare: Bygg Time Series Insights agg regerings uttryck, Använd dem för att fråga efter Time Series Insights data och återge sedan de tre diagrammen. Tre diagram typer används från tsiClient. UX-namn området: `LineChart`, `BarChart`och `PieChart`. Diagram typerna används för att skapa och återge respektive diagram. Alla tre diagram kan använda samma agg regerings uttrycks data `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

De tre diagrammen visas på följande sätt vid renderingen:

[![Flera diagramtyper från samma data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Avancerade funktioner

Time Series Insights klient biblioteket har flera ytterligare funktioner som du kan använda för att implementera data visualiseringar kreativt.

### <a name="states-and-events"></a>Tillstånd och händelser

En avancerad funktion är möjligheten att lägga till tillstånds över gångar och diskreta händelser i diagram. Den här funktionen är användbar för att markera incidenter, aviseringar och skapa tillstånds växlar (till exempel på/av växlar).

Titta på koden som omger `// Example 10` kommentaren. Koden `Line Charts with Multiple Series Types` återger en rad kontroll under rubriken och binder den `<div>` till elementet med HTML- `id` värdet `chart10`.

Följande steg beskriver processen:

1. En struktur som `events4` heter är definierad för att innehålla de tillstånds ändrings element som ska spåras. Strukturen innehåller:

   * En strängnyckel som heter `Component States`.
   * En matris med värdeobjekt som representerar tillstånd. Varje objekt inkluderar:
     * En strängnyckel som innehåller en ISO JavaScript-tidsstämpel.
     * En matris som innehåller egenskaper för tillståndet: en färg och en beskrivning.

1. Strukturen definieras för `Incidents`, som innehåller en matris med de händelse element som ska spåras. `events5` Matrisstrukturen är samma form som strukturen som beskrivs för `events4`.

1. Linje diagrammet återges och överförs i de två strukturerna med diagram alternativ parametrarna: `events:` och. `states:` Observera de andra alternativ parametrarna för att ange `tooltip:`en `theme:`, eller `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Diamond-markörerna/popup-fönstren som används för att Visa incidenter och färgade fält/popup-fönster längs tids skalan indikerar tillstånds ändringar:

[![Linje diagram med flera serie typer](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Popup-snabbmenyer

En annan avancerad funktion är möjligheten att skapa anpassade snabb menyer (Högerklicka på popup-menyer). Anpassade snabbmenyer är användbara för att aktivera åtgärder och logiska nästa steg inom omfånget för ditt program.

Titta på koden runt `// Example 13/14/15` kommentaren. Den här koden återger ursprungligen ett linje diagram under rubriken `Line Chart with Context Menu to Create Pie/Bar Chart`. Diagrammet är kopplat till `<div>` elementet med HTML- `id` värdet `chart13`.

Med hjälp av snabbmenyer, ger linjediagrammet möjlighet att dynamiskt skapa ett cirkel- och stapeldiagram som är bundet till `<div>`-elementen med ID `chart14` och `chart15`. Både cirkel diagrammet och stapeldiagrammet använder också Sammanhangs menyer för att aktivera sina egna funktioner: möjligheten att kopiera data från cirkel diagrammet till stapeldiagrammet och att skriva ut diagram data till fönstret för webb läsar konsolen.

Följande steg beskriver processen:

1. En serie med anpassade åtgärder definieras. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:

   * `barChartActions`: Den här åtgärden definierar snabbmenyn för cirkeldiagrammet, som innehåller ett element för att definiera ett enstaka objekt:
     * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
     * `action`: Åtgärden som är associerad med menyobjektet. Åtgärden är alltid en anonym funktion som tar tre argument som är baserade på mängduttrycket som används för att skapa diagrammet. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
       * `ae`: Mängduttrycksmatrisen.
       * `splitBy`: `splitBy` Värdet.
       * `timestamp`: Tidsstämpeln.

   * `pieChartActions`: Den här åtgärden definierar snabbmenyn för stapeldiagrammet som innehåller ett element för att definiera ett enskilt objekt. Formen och schemat är samma som det `barChartActions` element som beskrivs ovan, `action` men funktionen är dramatiskt annorlunda: den instansierar och återger stapeldiagrammet. `ae` Argumentet används för att ange den aggregerade uttrycks mat ris som skickas vid körning när meny alternativet öppnas. Funktionen anger också `ae.contextMenu`-egenskapen med `barChartActions`-snabbmenyn.
   * `contextMenuActions`: Den här åtgärden definierar snabbmenyn för linjediagram som innehåller tre element för att definiera tre menyalternativ. Figuren och schemat för varje element är detsamma som de element som beskrevs tidigare. Precis som `barChartActions`-elementet, skriver det första objektet tre funktionsargument till konsolens webbläsarfönster. På samma sätt `pieChartActions` som elementet, instansieras det andra två objekten och återger cirkel diagrammet och stapeldiagrammet. De andra två objekten anger också sina `ae.contextMenu`-egenskaper med snabbmenyerna `pieChartActions` och `barChartActions`.

1. Två mängd uttryck skickas till matrisen `aes` för aggregerade uttryck. De anger `contextMenuActions` matrisen för varje objekt. Dessa uttryck används med linjediagramkontrollen.

1. Endast linje diagrammet återges från början, där både cirkel diagrammet och stapeldiagrammet kan återges vid körning.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Följande skärm bild visar diagrammen med deras respektive popup-menyer. Cirkel diagrammet och stapeldiagrammet har skapats dynamiskt med hjälp av snabb meny alternativen i linje diagrammet.

[![Linje diagram med snabb meny för att skapa ett cirkel diagram och stapeldiagram](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Penslar

Du kan använda penslar för att begränsa tidsintervallet för att definiera åtgärder som zooma och utforska.

Den kod som används för att illustrera penslar visas i `Line Chart with Context Menu to Create Pie/Bar Chart` exemplet som beskriver snabb menyer i popup-fönster.

* Pensel åtgärder liknar en snabbmeny i att de definierar en serie av anpassade åtgärder för penseln. Varje åtgärd innehåller en matris som har ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:
   * `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
   * `action`: Den åtgärd som är associerad med menyalternativet som alltid är en anonym funktion som tar två argument. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
     * `fromTime`: `from` Tidsstämpeln för pensel valet.
     * `toTime`: `to` Tidsstämpeln för pensel valet.

* Penselåtgärder läggs till som en annan diagramalternativegenskap. `brushContextMenuActions: brushActions` Egenskapen skickas tillanropet`linechart.Render` .

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Linje diagram med snabb meny för att skapa ett cirkel diagram och stapeldiagram med hjälp av penslar](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Nu när du har slutfört självstudien rensar du de resurser som du har skapat:

1. På den vänstra menyn i [Azure Portal](https://portal.azure.com)väljer du **alla resurser**, letar reda på Azure Time Series Insights resurs gruppen.
1. Ta antingen bort hela resurs gruppen (och alla resurser som ingår i den) genom att välja **ta bort** eller ta bort varje resurs individuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Logga in och utforska Time Series Insights exempel program och dess källa
> * Använda API: er i klient biblioteket för Time Series Insights Java Script
> * Använd Java Script för att skapa och fylla i diagram kontroller med Time Series Insights data

Exempel programmet Time Series Insights använder en demo data uppsättning. Läs följande artikel för att lära dig hur du kan skapa en egen Time Series Insights-miljö och data uppsättning:

> [!div class="nextstepaction"]
> [Självstudier: Skapa en Azure Time Series Insights-miljö](tutorial-create-populate-tsi-environment.md)

Du kan också Visa källfilerna för Time Series Insights exempel program:

> [!div class="nextstepaction"]
> [Time Series Insights exempel på App-lagringsplats](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Läs dokumentationen för Time Series Insights klient-API-referens:

> [!div class="nextstepaction"]
> [Dokumentation om Time Series Insights API-referens](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
