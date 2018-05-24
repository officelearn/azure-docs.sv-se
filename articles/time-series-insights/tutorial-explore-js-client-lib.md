---
title: Utforska JavaScript-klientbiblioteket i Time Series Insights
description: Läs mer om JavaScript-klientbiblioteket i Time Series Insights och dess relaterade programmeringsmodell.
documentationcenter: ''
services: time-series-insights
author: BryanLa
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: time-series-insights
ms.workload: na
ms.tgt_pltfrm: ''
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: 5b845f36dbb65b38d0e4ac2a118277027239b3d6
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-explore-the-time-series-insights-javascript-client-library"></a>Självstudie: Utforska JavaScript-klientbiblioteket i Time Series Insights

Vi har utvecklat ett JavaScript D3-baserat kontrollbibliotek som underlättar för utvecklare när de frågar efter och visualiserar data som lagras i Time Series Insights (TSI). Med ett exempel på ett webbprogram vägleder den här självstudien dig genom en undersökning av JavaScript-klientbiblioteket i TSI och den relaterade programmeringsmodellen. 

I ämnena visas möjligheter att experimentera, att få en djupare förståelse för hur du kommer åt TSI-data och hur du använder diagramkontroller till att rendera och visualisera data. Målet är att förse dig med tillräckligt med information så att du kan använda biblioteket i din egna webbapp.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * TSI-exempelprogrammet 
> * JavaScript-klientbiblioteket i TSI
> * Hur exempelprogrammet använder biblioteket till att visualisera TSI-data

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien använder funktionen ”Utvecklarverktyg” (även kallat DevTools eller F12) som finns i de flesta moderna webbläsare, som [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) med flera. Om du inte redan känner till den, kan du utforska den här funktionen i din webbläsare innan du fortsätter. 

## <a name="the-time-series-insights-sample-application"></a>Exempelprogrammet Time Series Insights

I hela den här självstudien används exempelprogrammet Time Series Insights till att utforska källkoden bakom programmet, inklusive användningen av JavaScript-klientbiblioteket i TSI. Programmet är en ensidesapplikation (SPA) som visar hur man använder biblioteket för att fråga efter och visualisera data i en TSI-exempelmiljö. 

1. Gå till [Exempelprogrammet Time Series Insights](https://insights.timeseries.azure.com/clientsample). En sida som liknar följande öppnas och du uppmanas att logga in: ![Inloggningsfråga för TSI-klientexempel](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Klicka på knappen ”Logga in” och ange eller välj dina autentiseringsuppgifter. Du kan antingen använda ett konto för företag/organisation (Azure Active Directory) eller ett personligt konto (Microsoft-konto eller MSA). 

   ![Fråga om behörighet i TSI-klientexempel](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. När du har loggat in visas en sida som liknar följande, med flera olika sorters exempeldiagram som är ifyllda med TSI-data. Observera också ditt användarkonto och länken ”logga ut” i det övre högra hörnet: ![Huvudsida efter inloggning i TSI-klientexempel](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Sidkälla och struktur

Låt oss först se på HTML- och JavaScript-källkoden bakom sidan som renderades i webbläsaren. Vi går inte igenom alla element, men du lär dig mer om viktiga avsnitt vilket ger dig en uppfattning om hur sidan fungerar:

1. Öppna ”Utvecklarverktyg” i webbläsaren och inspektera de HTML-element som utgör den aktuella sidan, vilket även kallas för HTML- eller DOM-träd.

2. Expandera elementen `<head>` och `<body>` och lägg märke till följande avsnitt:
   - Under `<head>` hittar du element som hämtar in ytterligare filer för att underlätta funktionen på sidan:
     - ett `<script>`-element som refererar till autentiseringsbiblioteket i Azure Active Directory (adal.min.js), vilket även kallas för ADAL. Detta är ett JavaScript-bibliotek som innehåller OAuth 2.0-autentisering (inloggning) och tokenförvärv för åtkomst till API:er:
     - `<link>`-element för formatmallar (sampleStyles.css, tsiclient.css), vilket även kallas för CSS. De används för att styra den visuella sidans formatinformation, till exempel färger, teckensnitt, avstånd osv. 
     - ett `<script>`-element som refererar till TSI-klientbiblioteket (tsiclient.js). Det är ett JavaScript-bibliotek som används av sidan för att anropa TSI-tjänstens API:er och rendera diagramkontrollerna på sidan.

     >[!NOTE]
     > Källkoden för JavaScript-biblioteket i ADAL är tillgänglig i [azure-activedirectory-library-for-js repository](https://github.com/AzureAD/azure-activedirectory-library-for-js).  
     > Källkoden för TSI-klientens JavaScript-bibliotek är tillgänglig i [tsiclient repository](https://github.com/Microsoft/tsiclient).

   - Under `<body>` finns `<div>`-element som är behållare som definierar layouten av objekt på sidan, samt ett annat `<script>`-element:
     - det första `<div>` anger dialogrutan ”Logga in” (`id="loginModal"`).
     - det andra `<div>` fungerar som överordnad för:
       - en rubrik `<div>` som används för statusmeddelanden och inloggningsinformation längst upp på sidan (`class="header"`).
       - en `<div>` för resten av sidans textelement, inklusive alla diagram (`class="chartsWrapper"`).
       - ett `<script>`-avsnitt som innehåller alla JavaScript som används för att styra sidan.

   [![TSI-klientexempel med DevTools](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. När du expanderar elementet `<div class="chartsWrapper">` hittar du flera underordnade `<div>`-element som används för att placera varje diagramkontrollexempel. Observera att det finns flera par `<div>`-element, ett för varje diagramexempel:
   - Den första (`class="rowOfCardsTitle"`) innehåller en beskrivande rubrik som sammanfattar vad diagrammen visar. Till exempel: ”Statiskt linjediagram med förklaringar i fullstorlek”
   - Den andra (`class="rowOfCards"`) är överordnad och innehåller ytterligare underordnade `<div>`-element som placerar diagramkontrollerna inom en rad. 

  ![Visa texten ”divs”](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Expandera nu `<script type="text/javascript">`-elementet som finns direkt under `<div class="chartsWrapper">`-elementet. Du ser början av JavaScript-avsnittet på sidnivå som används för att hantera all sidlogik för till exempel autentisering, anropa TSI-tjänstens API:er, rendera diagramkontroller med mera:

  ![Visa textskriptet](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-client-javascript-library-concepts"></a>Begrepp för TSI-klientens JavaScript-bibliotek

Även om vi inte granskar det i detalj, innehåller TSI-klientbiblioteket (tsclient.js) en abstraktion för två viktiga kategorier:

- **Omslutningsmetoder för att anropa TSI-frågans API:er** – Med REST-API:er kan du fråga efter TSI-data med hjälp av mängduttryck. Dessa finns under `TsiClient.Server`-namnområdet för biblioteket. 
- **Metoder för att skapa och fylla i flera typer av diagramkontroller** – Används för rendering av TSI-sammanställda data på en webbsida och finns under `TsiClient.UX`-namnområdet för biblioteket. 

Följande begrepp är universella och gäller för TSI-klientbibliotekets API:er i allmänhet. 

### <a name="authentication"></a>Autentisering

Som nämnts tidigare är det här exemplet en ensidesapplikation som använder OAuth 2.0-stöd i ADAL för autentiseringen av användare. Nedan visas några viktiga delar i det här avsnittet av skriptet:

1. När ADAL för autentisering används måste klientprogrammet registrera sig själv i programregistret för Azure Active Directory (Azure AD). Eftersom det är en SPA är programmet registrerat för att använda ”implicit” OAuth 2.0-auktorisering för att bevilja flödet. På motsvarande sätt anger programmet vissa av registreringsegenskaperna vid körning, t.ex klient-ID:ts GUID (`clientId`) och omdirigerings-URI (`postLogoutRedirectUri`), för att kunna ingå i flödet.

2. Programmet begär senare en ”åtkomsttoken” från Azure AD. Denna åtkomsttoken utfärdas för en begränsad uppsättning behörigheter och för en specifik tjänst/API-identifierare (https://insights.timeseries.azure.com), även kallad ”tokenmålgrupp”. Tokenbehörigheterna utfärdas åt den inloggade användaren. Identifieraren för tjänsten/API:n är en annan egenskap som finns i programmets Azure AD-registrering. När ADAL har returnerat åtkomsttoken till programmet, skickas den som en ”ägartoken” vid åtkomst till TSI-tjänstens API:er. 

   [!code-javascript[head-sample](~/samples-javascript/pages/index.html?range=140-199&highlight=4-9,36-39)]

### <a name="control-identification"></a>Kontrollidentifiering

Som nämnts tidigare ger `<div>`-elementen i `<body>` layouten för alla diagramkontroller som visas på sidan. Var och en av dem anger egenskaper för placering och visuella attribut för diagramkontrollen, inklusive en `id`-egenskap. `id`-egenskapen innehåller en unik identifierare som används i JavaScript-koden för att identifiera och binda till varje kontroll vid rendering och uppdatering. 

### <a name="aggregate-expressions"></a>Mängduttryck

TSI-klientbibliotekets API:er har stor användning av mängduttryck. Med ett mängduttryck kan du skapa ett eller flera ”sökvillkor”. API:er fungerar ungefär likadant som när [Time Series Insights-utforskaren](https://insights.timeseries.azure.com/demo) använder sökintervall med predikat-, mått- och splitBy-värden. De flesta biblioteks-API:er tar en matris med mängduttryck som används av tjänsten till att skapa en TSI-datafråga.

### <a name="call-pattern"></a>Anropsmönster

Att fylla i och rendera diagramkontroller följer ett allmänt mönster. Du kommer att upptäcka att det här mönstret används på hela sidan med JavaScript, där det instantierar och läser in TSI-exempelprogrammets kontroller:

1. Deklarera en matris till att innehålla ett eller flera TSI-mängduttryck.  

   ```javascript
   var aes =  [];
   ```

2. Skapa 1-till-n-objekt för mängduttrycket och lägg till dem i mängduttryckets matris.  

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```
   **aggregateExpression parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | predicateObject | Uttryck för filtrering av data. |`{predicateString: "Factory = 'Factory3'"}` |
   | measureObject   | Egenskapsnamn för det mått som används. | `{property: 'Temperature', type: "Double"}` |
   | measureTypes    | Önskade sammansättningar för måttegenskapen. | `['avg', 'min']` |
   | searchSpan      | Mängduttryckets varaktighet och intervallstorlek. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | splitByObject   | Strängegenskapen som du vill dela med (valfri – kan vara null). | `{property: 'Station', type: 'String'}` |
   | color           | Färgen på de objekt som du vill rendera. | `'pink'` |
   | alias           | Ett eget namn på mängduttrycket. | `'Factory3Temperature'` |
   | contextMenuActions | En matris med åtgärder som ska bindas till tidsserieobjekten i en visualisering (valfritt). | Se [Popup-snabbmenyer i avsnittet Avancerade funktioner.](#popup-context-menus) |

3. Anropa en TSI-fråga med hjälp av `TsiClient.Server`-API:er för att begära sammanställda data.  

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```
   **getAggregates parameters**

   | Parameter | Beskrivning | Exempel |
   | --------- | ----------- | ------- |
   | token     | Åtkomsttoken för TSI:ns API | `authContext.getTsiToken()` Se [autentiseringsavsnittet.](#authentication) |
   | envFQDN     | Fullständigt domännamn för TSI-miljön | Från Azure Portal, till exempel `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | aeTsxArray | Matris med TSI-frågeuttryck | Använd `aes`-variabeln som beskrevs tidigare: `aes.map(function(ae){return ae.toTsx()}` |

4. Transformera det komprimerade resultat som returnerades från TSI-frågan till JSON för visualisering.

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Skapa en diagramkontroll med `TsiClient.UX`-API:er och bind den till ett av `<div>`-elementen på sidan.

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Fyll i diagramkontrollen med de transformerade JSON-dataobjekten och rendera den på sidan.

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Renderingskontroller

Biblioteket innehåller för närvarande åtta unika analyskontroller. De inkluderar linjediagram, cirkeldiagram, stapeldiagram, heatmap, hierarkikontroller, ett tillgängligt rutnät, diskreta händelsetidslinjer och tidslinjer för tillståndsövergång.   

### <a name="line-bar-pie-chart-examples"></a>Exempel på linje-, stapel- och cirkeldiagram 

Först ska vi titta på koden bakom vissa standarddiagramkontroller som visas i programmet, samt programmeringsmodell/mönster för att skapa dem. Mer specifikt ska du undersöka HTML-avsnittet under `// Example 3/4/5`-kommentaren som renderar kontroller med ID-värdena `chart3`, `chart4` och `chart5`. 

Som visades i steg 3 i [avsnittet Sidkälla och struktur](#page-source-and-structure) är diagramkontrollerna ordnade i rader på sidan, där var och en har en beskrivande rubrikrad. I det här exemplet finns de tre diagrammen som fylls i under rubriken ”Flera diagramtyper från samma data” `<div>` med en bindning till de tre `<div>`-elementen under den:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/index.html?range=60-74&highlight=1,5,9,13)]

I följande avsnitt av JavaScript-koden används mönstret som beskrevs tidigare till att skapa TSI-mängduttryck, använda dem för att fråga efter TSI-data och rendera de tre diagrammen. Observera de tre typerna som används av `tsiClient.ux`-namnområdet, `LineChart`, `BarChart`, `PieChart` för att skapa och rendera respektive diagram. Observera också att alla tre diagrammen kan använda samma data i mängduttrycket, `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/index.html?range=236-257&highlight=13-14,16-17,19-20)]

De tre diagrammen visas på följande sätt vid renderingen:

[![Flera diagramtyper från samma data](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Avancerade funktioner

I biblioteket finns även vissa valfria avancerade funktioner som du kan få nytta av.  

### <a name="states-and-events"></a>Tillstånd och händelser

Ett exempel på den avancerade funktionalitet som finns är möjligheten att lägga till tillståndsövergångar och diskreta händelser i diagram. Den här funktionen är användbar vid markering av incidenter, varningar och tillståndsväxlingar som på/av. 

Här ser du koden bakom HTML-avsnittet under `// Example 10`-kommentaren. Koden renderar en linjekontroll under rubriken ”Linjediagram med flera serietyper” som binds till `<div>` med ID-värdet `chart10`:

1. Först definieras en struktur med namnet `events4` som ska innehålla de tillståndsändrade element som ska spåras. Den innehåller:
   - En strängnyckel med namnet `"Component States"` 
   - En matris med värdeobjekt som motsvarar tillstånden. Vart och ett innehåller:
     - En strängnyckel som innehåller en tidsstämpel för JavaScript ISO
     - En matris med egenskaper för tillståndet
       - en färg
       - en beskrivning

2. Sedan definieras `events5`-strukturen för `"Incidents"`, vilken innehåller en matris med de händelseelement som ska spåras. Matrisstrukturen har samma format som den som beskrevs för `events4`. 

3. Slutligen renderas linjediagrammet som skickar de två strukturerna med diagramalternativens parametrar: `events:` och `states:`. Observera de andra alternativparametrarna som anger en `tooltip:`, `theme:` eller `grid:`. 

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/index.html?range=332-384&highlight=5,26,51)]

Visuellt används rombformade markörer/popups för att visa incidenter och färgade staplar/popups längs tidsskalan för att visa tillståndsändringar:

[![Linjediagram med flera serietyper](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="popup-context-menus"></a>Popup-snabbmenyer

Ett annat exempel på avancerade funktioner är anpassade snabbmenyer (vid högerklick på popup-menyer), som är användbara för att aktivera åtgärder och logiska steg inom omfånget för ditt program.

Här ser vi koden bakom HTML under `// Example 13/14/15`. Den här koden renderar först ett linjediagram under rubriken ”Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram”, med en bindning till `<div>`-elementet med ID-värdet `chart13`. Med snabbmenyer ger linjediagrammet möjlighet att dynamiskt skapa ett cirkel- och stapeldiagram med en bindning till `<div>`-elementet med ID-värdena `chart14` och `chart15`. Dessutom använder både cirkel- och stapeldiagram snabbmenyer till att aktivera sina egna funktioner: t.ex. möjligheten att kopiera data från cirkeldiagrammet till stapeldiagrammet, samt att skriva ut stapeldiagramdata till webbläsarens konsolfönster.

1. Först definieras en serie med anpassade åtgärder. Varje serie innehåller en matris med ett eller flera element, där varje element definierar ett objekt för en snabbmeny: 
   - `barChartActions`: definierar snabbmenyn för cirkeldiagrammet, med ett element som definierar ett enskilt objekt:
     - `name`: den text som används för menykommandot: ”Skriv ut parametrar till konsolen”
     - `action`: åtgärden associeras med menykommandot, som alltid är en anonym funktion där tre argument baseras på mängduttrycket för att skapa diagrammet. De skrivs i detta fall till konsolfönstret i webbläsaren:
       - `ae`: mängduttryckets matris
       - `splitBy`: splitBy-värdet
       - `timestamp`: tidsstämpeln
   - `pieChartActions`: definierar snabbmenyn för stapeldiagrammet, med ett element som definierar ett enskilt objekt. Format och schema är samma som i tidigare `barChartActions`, men observera att `action`-funktionen skiljer sig avsevärt eftersom den instantierar och återger stapeldiagrammet. Observera också att den använder `ae`-argumentet till att ange mängduttryckets matris som skickades vid körning med popup-fönstret i menykommandot. Funktionen anger också `ae.contextMenu`-egenskapen med `barChartActions`-snabbmenyn.
   - `contextMenuActions`: definierar snabbmenyn för linjediagrammet, som innehåller tre element för att definiera tre menykommandon. Format och schema för varje element är samma som i de tidigare. Precis som i `barChartActions`, skriver det första objektet de tre funktionsargumenten till webbläsarens konsolfönster. Liknande som i `pieChartActions` instantierar och återger de andra två objekten cirkel- och stapeldiagrammen. De andra två objekten anger också sina `ae.contextMenu`-egenskaper med snabbmenyerna `pieChartActions` och `barChartActions`.

2. De två mängduttrycken överförs sedan till `aes`-mängduttryckets matris som anger `contextMenuActions`-matrisen för var och en. Dessa används med linjediagramkontrollen.

3. Slutligen renderas endast linjediagrammet, som både cirkel- och stapeldiagrammen kan renderas från vid körning.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/index.html?range=456-535&highlight=7,16,29,61-64,78)]

Skärmbild som visar diagrammen med deras respektive popup-snabbmenyer. Cirkel- och stapeldiagrammen skapades dynamiskt, med hjälp av linjediagrammets snabbmenyalternativ:

[![Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Penslar

Penslar kan användas för att definiera ett tidsintervall för åtgärder som att zooma och utforska. 

Den kod som används för att illustrera penslarna visas även i föregående exempel ”Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram”, som beskriver [Popup-snabbmenyer](#popup-context-menus-section). 

1. Penselåtgärder är mycket lika en snabbmeny och definierar serier med anpassade åtgärder för penseln. Varje serie innehåller en matris med ett eller flera element, där varje element definierar ett objekt för en snabbmeny:
   - `name`: den text som används för menykommandot: ”Skriv ut parametrar till konsolen”
   - `action`: åtgärden som associeras med menykommandot. Detta är alltid en anonym funktion med två argument. De skrivs i detta fall till konsolfönstret i webbläsaren:
      - `fromTime`: tidsstämpeln ”från” i penselmarkeringen
      - `toTime`: tidsstämpeln ”till” i penselmarkeringen

2. Penselåtgärder läggs till som en annan diagramalternativegenskap. Observera att `brushContextMenuActions: brushActions`-egenskapen skickas till `linechart.Render`-anropet.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/index.html?range=521-535&highlight=1,13)]

![Linjediagram med snabbmeny för att skapa cirkel-/stapeldiagram med penslar](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Logga in och utforska TSI-exempelprogrammet och dess källa
> * Använda API:er i JavaScript-klientbiblioteket i TSI
> * Använda JavaScript för att skapa och fylla i diagramkontroller med TSI-data

Som vi nämnt tidigare använder TSI-exempelprogrammet en demo-datamängd. Mer information om hur du kan skapa din egna TSI-miljö och datamängd finns i följande artikel:

> [!div class="nextstepaction"]
> [Planera din Azure Time Series Insights-miljö](time-series-insights-environment-planning.md)


