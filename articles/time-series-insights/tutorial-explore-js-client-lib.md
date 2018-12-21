---
title: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights | Microsoft Docs
description: Läs mer om JavaScript-klientbiblioteket i Azure Time Series Insights och dess relaterade programmeringsmodell.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 33dcf6f69d1287b4e040b3cccf4164667db2b75f
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269982"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Självstudie: Utforska JavaScript-klientbiblioteket i Azure Time Series Insights

Vi har utvecklat ett JavaScript D3-baserat TSI-klientbibliotek för att hjälpa utvecklare när de frågar efter och visualiserar data som lagras i Time Series Insights (TSI).  Med ett exempel på ett webbprogram vägleder den här självstudien dig genom JavaScript-klientbiblioteket i TSI och den relaterade programmeringsmodellen.

Ämnena i den här självstudien ger dig möjligheter att experimentera med biblioteket för att få en djupare förståelse för hur du kommer åt TSI-data och använder diagramkontroller för att rendera och visualisera data. Målet är att förse dig med tillräckligt med information så att du kan använda biblioteket i ditt egna webbprogram.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * TSI-exempelprogrammet.
> * JavaScript-klientbiblioteket i TSI.
> * Hur exempelprogrammet använder biblioteket för att visualisera TSI-data.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>I den här videon introducerar vi Time Series Insights JavaScript SDK:n med öppen källkod.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien använder funktionen Utvecklarverktyg (även kallat DevTools eller F12) som finns i de flesta moderna webbläsare, inklusive [Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) med flera. Om du inte redan känner till den här funktionen, kan du utforska den i din webbläsare innan du fortsätter.

## <a name="time-series-insights-sample-application"></a>Time Series Insights-exempelprogram

I hela den här självstudien används exempelprogrammet Time Series Insights för att utforska källkoden bakom programmet, inklusive användningen av JavaScript-klientbiblioteket i TSI. Exemplet är ett webbprogram på en sida (SPA) som visar hur du använder i biblioteket. Exemplet visar hur du frågar och visualiserar data från en TSI-exempelmiljö.

1. Gå till [Exempelprogrammet Time Series Insights](https://insights.timeseries.azure.com/clientsample). Du ser en sida som liknar följande bild med en uppmaning att logga in:

   ![TSD-klientens exempelinloggningsprompt](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Välj **Logga in** och ange eller välj dina autentiseringsuppgifter. Du kan antingen använda ett företags- eller organisationskonto (Azure Active Directory) eller ett personligt konto (Microsoft-konto eller MSA).

   ![Fråga om autentiseringsuppgifter i TSI-klientexemplet](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Efter en lyckad inloggning, ser du en sida som liknar följande bild. Sidan visar många sorters exempeldiagram som fylls i med TSI-data. Ditt användarkonto och **Logga ut**-alternativet visas i det övre högra hörnet:

   ![TSI-klientens exempelhuvudsida efter inloggning](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Sidkälla och struktur

Låt oss först se på HTML- och JavaScript-källkoden bakom sidan som renderades i din webbläsare. Vi går inte igenom alla element, men du lär dig mer om viktiga avsnitt vilket ger dig en uppfattning om hur sidan fungerar:

1. Öppna **Utvecklingsverktyg** i webbläsaren. Inspektera HTML-elementen som utgör den aktuella sidan (även kallade HTML- eller DOM-träd).

2. Expandera elementen `<head>` och `<body>` och lägg märke till följande avsnitt:

   - Under `<head>`-elementet hittar du element som hämtar in ytterligare filer för att underlätta sidans funktion:
     - Ett `<script>`-element som används för att referera till Azure Active Directory Authentication Library **adal.min.js** (även kallat ADAL). ADAL är ett JavaScript-bibliotek som innehåller OAuth 2.0-autentisering (inloggning) och tokenförvärv för åtkomst till API:er.
     - Flera `<link>`-element för formatmallar (även kallat CSS) som **sampleStyles.css** och **tsiclient.css**. Formatmallarna används för att styra visuell formatmallsinformation för sidan, till exempel färger, teckensnitt, avstånd och så vidare.
     - Ett `<script>`-element som används för att referera till TSI-klientens JavaScript-bibliotek **tsiclient.js**. Biblioteket används av sidan för att anropa TSI-tjänstens API:er och rendera diagramkontrollerna på sidan.

     >[!NOTE]
     > Källkoden för JavaScript-biblioteket i ADAL är tillgänglig i [azure-activedirectory-library-for-js repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Källkoden för TSI-klientens JavaScript-bibliotek är tillgänglig i [tsiclient repository](https://github.com/Microsoft/tsiclient).

   - Under `<body>`-elementet finns `<div>`-element, som fungerar som containrar för att definierar layouten för objekt på sidan, samt ett annat `<script>`-element:
     - Det första `<div>`-elementet anger **Inloggnings**-dialogrutan (`id="loginModal"`).
     - Det andra `<div>`-elementet agerar som överordnat för:
       - Ett rubrik `<div>`-element som används för statusmeddelanden och inloggningsinformation längst upp på sidan (`class="header"`).
       - Ett `<div>`-element för resten av sidans brödtextelement, inklusive alla diagram (`class="chartsWrapper"`).
       - Ett `<script>`-avsnitt som innehåller alla JavaScript som används för att styra sidan.

   [![TSI-klientexempel med utvecklarverktyg](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Expandera `<div class="chartsWrapper">`-elementet så hittar du flera underordnade `<div>` element. Dessa element används för att placera varje diagramkontrollexempel. Observera att det finns flera par `<div>`-element, ett för varje diagramexempel:

   - Det första (`class="rowOfCardsTitle"`) innehåller en beskrivande rubrik som sammanfattar vad diagrammen visar. Exempel: Statiskt linjediagram med teckenförklaringar i fullstorlek.
   - Det andra (`class="rowOfCards"`) elementet är överordnat och innehåller ytterligare underordnade `<div>`-element som placerar de faktiska diagramkontrollerna inom en rad.

   ![Body div-element](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Expandera nu `<script type="text/javascript">`-elementet som finns direkt under `<div class="chartsWrapper">`-elementet. Observera början av JavaScript-avsnittet på sidnivå som används för att hantera all sidlogik: autentisering, anropa TSI-tjänstens API:er, rendera diagramkontroller med mera:

   ![Brödtextskript](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>TSI JavaScript-klientbibliotekskoncept

Även om vi inte går igenom det i detalj, erbjuder TSI klientbiblioteket **tsclient.js** en abstraktion för två viktiga kategorier:

- **Omslutningsmetoder för att anropa fråge-API:er för TSI**: REST-API:er som gör det möjligt att fråga efter TSI-data med hjälp av sammansatta uttryck. Metoderna organiseras under `TsiClient.Server`-namnområdet för biblioteket.
- **Metoder för att skapa och fylla flera typer av diagramkontroller**: Metoder som används för att återge insamlade TSI-data på en webbsida. Metoderna organiseras under `TsiClient.UX`-namnområdet för biblioteket.

Följande begrepp är universella och gäller för TSI-klientbibliotekets API:er i allmänhet.

### <a name="authentication"></a>Autentisering

Som nämnts tidigare är det här exemplet ett SPA som använder sig av OAuth 2.0-stöd i ADAL för användarautentisering. Nedan visas några viktiga delar i det här avsnittet av skriptet:

1. När ADAL används för autentisering måste klientprogrammet registrera sig själv i programregistret för Azure Active Directory (Azure AD). Eftersom det är en SPA är programmet registrerat för att använda ”implicit” OAuth 2.0-auktorisering för att bevilja flödet. På motsvarande sätt anger programmet vissa av registreringsegenskaperna vid körning, t.ex klient-ID:ts GUID (`clientId`) och omdirigerings-URI (`postLogoutRedirectUri`), för att kunna ingå i flödet.

2. Programmet begär senare en ”åtkomsttoken” från Azure AD. Denna åtkomsttoken utfärdas för en begränsad uppsättning behörigheter och för en specifik tjänst/API-identifierare https://api.timeseries.azure.com. Tjänst/API-identifieraren kallas även för token-målgruppen. Tokenbehörigheterna utfärdas åt den inloggade användaren. Identifieraren för tjänsten/API:n är en annan egenskap som finns i programmets Azure AD-registrering. Efter att ADAL har returnerat åtkomsttoken till programmet, skickas den som en ägartoken vid åtkomst till TSI-tjänstens API:er.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Kontrollidentifiering

Som det nämnts tidigare ger `<div>`-elementen i `<body>`-elementet layouten för alla diagramkontroller som visas på sidan. Varje `<div>`-element anger egenskaperna för placering och visuella attribut för diagramkontrollen, inklusive en `id`-egenskap. `id`-egenskapen tillhandahåller en unik identifierare som används i JavaScript-koden för att identifiera och binda till varje kontroll for rendering och uppdatering.

### <a name="aggregate-expressions"></a>Mängduttryck

TSI-klientbibliotekets API:er har stor användning av mängduttryck. Med ett mängduttryck kan du skapa ett eller flera ”sökvillkor”. API:er fungerar ungefär likadant som när [Time Series Insights-utforskaren](https://insights.timeseries.azure.com/demo) använder sökintervall med predikat-, mått- och splitBy-värden. De flesta biblioteks-API:erna tar en matris med mängduttryck som används av tjänsten till att skapa en TSI-datafråga.

### <a name="call-pattern"></a>Anropsmönster

Att fylla i och rendera diagramkontroller följer ett allmänt mönster. Du kommer att upptäcka att det här mönstret används sidans JavaScript, där det instantierar och läser in TSI-exempelprogrammets kontroller:

1. Deklarerar en `array` för att hålla ett eller flera TSI-mängduttryck:

   ```javascript
   var aes =  [];
   ```

2. Skapa 1-till-n-objekt för mängduttrycket och lägg till dem i mängduttryckets matris:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `predicateObject` | Uttryck för filtrering av data. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Egenskapsnamn för det mått som används. | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Önskade sammansättningar för måttegenskapen. | `['avg', 'min']` |
   | `searchSpan`      | Mängduttryckets varaktighet och intervallstorlek. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Strängegenskapen som du vill dela med (valfritt – kan vara null). | `{property: 'Station', type: 'String'}` |
   | `color`         | Färgen på de objekt som du vill rendera. | `'pink'` |
   | `alias`           | Ett eget namn på mängduttrycket. | `'Factory3Temperature'` |
   | `contextMenuActions` | En matris med åtgärder som ska bindas till tidsserieobjekten i en visualisering (valfritt). | För mer information, se [Popup-snabbmenyer i avsnittet Avancerade funktioner.](#popup-context-menus) |

3. Anropa en TSI-fråga med hjälp av `TsiClient.Server` API:er för att begära sammanställda data:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | `token`     | Åtkomsttoken för TSI API:n. |  `authContext.getTsiToken()` Mer information finns i [autentiseringsavsnittet.](#authentication) |
   | `envFQDN`   | Det fullständigt kvalificerade domännamnet (FQDN) för TSI-miljön. | Från Azure-portalen, till exempel: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | En matris med TSI-frågeuttryck. | Använd `aes`-variabeln som det beskrevs tidigare: `aes.map(function(ae){return ae.toTsx()}`. |

4. Transformera det komprimerade resultatet som returnerades från TSI-frågan till JSON för visualisering:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Skapa en diagramkontroll med `TsiClient.UX`-API:er och bind den till ett av `<div>`-elementen på sidan:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Fyll i diagramkontrollen med de transformerade JSON-dataobjekten och rendera den kontrollen på sidan:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderingskontroller

TSI klientbiblioteket exponerar för tillfället åtta unika analyskontroller: linjediagram, cirkeldiagram, stapeldiagram, heatmap, hierarkikontroller, ett tillgängligt rutnät, diskreta händelsetidslinjer och tidslinjer för tillståndsövergång.

### <a name="line-bar-pie-chart-examples"></a>Exempel på linje-, stapel- och cirkeldiagram

Titta på koden bakom vissa av de standarddiagramkontroller som visas i programmet, samt programmeringsmodell/mönster för att skapa kontrollerna. Mer specifikt ska du undersöka HTML-avsnittet under `// Example 3/4/5`-kommentaren som renderar kontroller med ID-värdena `chart3`, `chart4` och `chart5`.

Som det visades i steg 3 i [avsnittet Sidkälla och struktur](#page-source-and-structure) är diagramkontrollerna ordnade i rader på sidan, där var och en har en beskrivande rubrikrad. I det här exemplet finns de tre diagrammen som fylls i under rubrik`<div>`-elementet Flera diagramtyper från samma data och binds till de tre `<div>`-elementen under rubriken:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Följande avsnitt av JavaScript-koden använder mönster som beskrevs tidigare för att skapa TSI-mängduttryck, använda dem för att fråga efter TSI-data och rendera de tre diagrammen. Observera de tre typerna som används av `tsiClient.ux`-namnområdet, `LineChart`, `BarChart` och `PieChart` för att skapa och rendera respektive diagram. Observera även att alla tre diagram kan använda samma data i mängduttrycket `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

De tre diagrammen visas på följande sätt vid renderingen:

[![Flera diagramtyper från samma data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Avancerade funktioner

TSD-klientbiblioteket exponerar även vissa valfria avancerade funktioner som du kan dra nytta av.

### <a name="states-and-events"></a>Tillstånd och händelser

Ett exempel på de avancerade funktionerna som tillhandahålls är möjligheten att lägga till tillståndsövergångar och diskreta händelser i diagram. Den här funktionen är användbar vid markering av incidenter, varningar och tillståndsväxlingar som på/av.

Titta på koden som ligger bakom avsnittet i HTML-koden som är under `// Example 10`-kommentaren. Koden återger en radkontroll under rubriken Linjediagram med flera serietyper och binder det till `<div>`-elementet med ID-värdet `chart10`.

1. Först definieras en struktur med namnet `events4` för att hålla statusändringselementen att spåra. Strukturen innehåller:

   - En strängnyckel som heter `Component States`.
   - En matris med värdeobjekt som representerar tillstånd. Varje objekt inkluderar:
     - En strängnyckel som innehåller en ISO JavaScript-tidsstämpel.
     - En matris som innehåller egenskaper för tillståndet: en färg och en beskrivning.

2. Sedan definieras `events5`-strukturen för incidenter, som innehåller en matris med händelseelement att spåra. Matrisstrukturen är samma form som strukturen som beskrivs för `events4`.

3. Slutligen renderas linjediagrammet och de två strukturerna med diagramalternativparametrarna skickas in: `events:` och `states:`. Lägg märke till de andra alternativparametrarna för att ange en `tooltip:`, `theme:` eller `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Visuellt, anger de rombformade markörerna/popup-fönstren som används för att ange incidenter och de färgade staplarna/popup-föntren längs tidsskalan indikerar tillståndsändringar:

[![Linjediagram med flera serietyper](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Popup-snabbmenyer

Ett annat exempel på avancerade funktioner är anpassade snabbmenyer (Högerklicka på popup-menyer). Anpassade snabbmenyer är användbara för att aktivera åtgärder och logiska nästa steg inom omfånget för ditt program.

Titta på koden som ligger bakom avsnittet i HTML-koden som är under `// Example 13/14/15`-kommentaren. Koden renderas från början som ett linjediagram under rubriken Linjediagram med kontextmeny för att skapa cirkel-/stapeldiagram och diagrammet binds till `<div>`-elementet med ID-värdet `chart13`. Med hjälp av snabbmenyer, ger linjediagrammet möjlighet att dynamiskt skapa ett cirkel- och stapeldiagram som är bundet till `<div>`-elementen med ID `chart14` och `chart15`. Dessutom använder både cirkel- och stapeldiagram snabbmenyer till att aktivera sina egna funktioner: t.ex. möjligheten att kopiera data från cirkeldiagrammet till stapeldiagrammet, samt att skriva ut stapeldiagramdata till webbläsarens konsolfönster.

1. Först definieras en serie med anpassade åtgärder. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:

   - `barChartActions`: Den här åtgärden definierar snabbmenyn för cirkeldiagrammet, som innehåller ett element för att definiera ett enstaka objekt:
     - `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
     - `action`: Åtgärden som är associerad med menyobjektet. Åtgärden är alltid en anonym funktion som tar tre argument som är baserade på mängduttrycket som används för att skapa diagrammet. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
       - `ae`: Mängduttrycksmatrisen.
       - `splitBy`: SplitBy-värdet.
       - `timestamp`: Tidsstämpeln.

   - `pieChartActions`: Den här åtgärden definierar snabbmenyn för stapeldiagrammet som innehåller ett element för att definiera ett enskilt objekt. Form och schema är samma som det föregående `barChartActions`-elementet, men observera att `action`-funktionen skiljer sig åt avsevärt: den instantierar och återger stapeldiagrammet. Observera att `ae`-argumentet används för att ange mängduttrycksmatrisen som skickades vid körning när menyalternativet öppnas. Funktionen anger också `ae.contextMenu`-egenskapen med `barChartActions`-snabbmenyn.
   - `contextMenuActions`: Den här åtgärden definierar snabbmenyn för linjediagram som innehåller tre element för att definiera tre menyalternativ. Form och schema för varje element är samma som de tidigare elementen som beskrevs. Precis som `barChartActions`-elementet, skriver det första objektet tre funktionsargument till konsolens webbläsarfönster. Precis som `pieChartActions`-elementet, instantierar och renderar de andra två objekten cirkel- respektive stapeldiagram. De andra två objekten anger också sina `ae.contextMenu`-egenskaper med snabbmenyerna `pieChartActions` och `barChartActions`.

2. Därefter pushas två mängduttryck till `aes`-sammanställda uttrycksmatrisen och anger `contextMenuActions`-matrisen för varje objekt. Dessa uttryck används med linjediagramkontrollen.

3. Slutligen renderas endast linjediagrammet, som både cirkel- och stapeldiagrammen kan renderas från vid körning.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Skärmbilden visar diagram med deras respektive popup-snabbmenyer. Cirkel- och stapeldiagrammen skapades dynamiskt med menyalternativen för linjediagrammen.

[![Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Penslar

Penslar används för att omfatta ett tidsintervall för att definiera åtgärder som zooma och utforska.

Den kod som används för att illustrera penslar visas i exemplet ovan Linjediagrammet med kontextmeny för att skapa cirkel/stapel linjediagrammet som beskriver [popup-snabbmenyer](#popup-context-menus-section).

1. Pensel åtgärder liknar en snabbmeny i att de definierar en serie av anpassade åtgärder för penseln. Varje åtgärd innehåller en matris med ett eller flera element. Varje element definierar ett enskilt kontextmenyalternativ:
   - `name`: Den text som ska användas för menyalternativet: ”Skriv ut parametrar till konsolen”.
   - `action`: Den åtgärd som är associerad med menyalternativet som alltid är en anonym funktion som tar två argument. I det här fallet skrivs argumenten till konsolfönstret i webbläsaren:
      - `fromTime`: Tidsstämpeln ”från” för penselmarkeringen.
      - `toTime`: Tidsstämpeln till för penselmarkeringen.

2. Penselåtgärder läggs till som en annan diagramalternativegenskap. Observera `brushContextMenuActions: brushActions`-egenskapen som överförs till `linechart.Render`-anropet.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Linjediagram med snabbmeny för att skapa cirkel/liggande diagram med penslar](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Logga in och utforska exempelprogrammet TSI och dess källa.
> * Använd API:er i TSI JavaScript-klientbiblioteket.
> * Använd JavaScript för att skapa och fylla i diagramkontrollerna med TSI-data.

Som vi nämnt tidigare använder TSI-exempelprogrammet en demo-datamängd. Om du vill veta hur du kan skapa din egen TSI-miljö och datamängd, fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Självstudie: Skapa en Azure Time Series Insights-miljö](tutorial-create-populate-tsi-environment.md)


