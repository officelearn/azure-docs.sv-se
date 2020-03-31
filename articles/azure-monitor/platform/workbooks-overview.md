---
title: Översikt över Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 29e675f3ae35df9211f58d45ad8450566d67a588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658242"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor-arbetsböcker

Arbetsböcker ger en flexibel arbetsyta för dataanalys och skapandet av omfattande visuella rapporter i Azure-portalen. De gör att du kan utnyttja flera datakällor från hela Azure och kombinera dem till enhetliga interaktiva upplevelser. 

## <a name="data-sources"></a>Datakällor

Arbetsböcker kan fråga data från flera källor i Azure. Författare till arbetsböcker kan omvandla dessa data för att ge insikter om tillgänglighet, prestanda, användning och övergripande hälsa för de underliggande komponenterna. Till exempel analysera prestandaloggar från virtuella datorer för att identifiera hög CPU eller lågt minne instanser och visa resultaten som ett rutnät i en interaktiv rapport.
  
Men den verkliga kraften i arbetsböcker är möjligheten att kombinera data från olika källor i en enda rapport. Detta gör det möjligt att skapa sammansatta resursvyer eller kopplingar mellan resurser som möjliggör rikare data och insikter som annars skulle vara omöjliga.

Arbetsböcker är för närvarande kompatibla med följande datakällor:

* [Loggar](workbooks-data-sources.md#logs)
* [Statistik](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Aviseringar (förhandsversion)](workbooks-data-sources.md#alerts-preview)
* [Arbetsbelastningshälsa (förhandsversion)](workbooks-data-sources.md#workload-health-preview)
* [Azure Resource Health (förhandsversion)](workbooks-data-sources.md#azure-resource-health)
* [Utforskaren i Azure Data (förhandsversion)](workbooks-data-sources.md#azure-data-explorer-preview)

## <a name="visualizations"></a>Visualiseringar

Arbetsböcker ger en omfattande uppsättning funktioner för att visualisera dina data. Detaljerade exempel på varje visualiseringstyp kan du läsa exempellänkarna nedan:

* [Text](workbooks-visualizations.md#text)
* [Diagram](workbooks-visualizations.md#charts)
* [Rutnät](workbooks-visualizations.md#grids)
* [Plattor](workbooks-visualizations.md#tiles)
* [Träd](workbooks-visualizations.md#trees)
* [Grafer](workbooks-visualizations.md#graphs)

![Exempel på arbetsboksvisualiseringar](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Komma igång

Om du vill utforska arbetsböckerna navigerar du först till Azure Monitor-tjänsten. Detta kan göras genom att skriva **Monitor** i sökrutan i Azure-portalen.

Välj sedan **Arbetsböcker (förhandsgranskning)**.

![Skärmbild av förhandsgranskningsknappen Arbetsböcker markerad i en röd ruta](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galleri

Detta tar dig till arbetsboksgalleriet:

![Skärmbild av gallerivyn i Azure Monitor-arbetsböcker](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Arbetsböcker jämfört med arbetsboksmallar

Du kan se en _arbetsbok_ i grönt och ett antal _arbetsboksmallar_ i lila. Mallar fungerar som kurerade rapporter som är utformade för flexibel återanvändning av flera användare och team. Om du öppnar en mall skapas en tillfällig arbetsbok som fylls med innehållet i mallen. 

Du kan justera den mallbaserade arbetsbokens parametrar och utföra analyser utan rädsla för att bryta den framtida rapporteringsupplevelsen för kollegor. Om du öppnar en mall gör du vissa justeringar och väljer sedan sparikonen som du ska spara mallen som en arbetsbok som sedan visas i grönt och lämnar den ursprungliga mallen orörd. 

Under huven skiljer sig mallarna också från sparade arbetsböcker. Om du sparar en arbetsbok skapas en associerad Azure Resource Manager-resurs, medan den tillfälliga arbetsbok som skapades när du bara öppnar en mall inte har någon unik resurs kopplad till den. Mer information om hur åtkomstkontroll hanteras i arbetsböcker finns i [artikeln för åtkomstkontroll](workbooks-access-control.md)för arbetsböcker .

### <a name="exploring-a-workbook-template"></a>Utforska en arbetsboksmall

Välj **Programfelanalys om** du vill visa en av standardprogramarbetsboksmallarna.

![Skärmbild av analysmall för programfel](./media/workbooks-overview/failure-analysis.png)

Som tidigare nämnts skapar öppnandet av mallen en tillfällig arbetsbok som du kan interagera med. Som standard öppnas arbetsboken i läsläge som bara visar informationen för den avsedda analysupplevelsen som skapades av den ursprungliga mallförfattaren.

När det gäller den här arbetsboken är upplevelsen interaktiv. Du kan justera prenumerationen, riktade appar och tidsintervallet för de data du vill visa. När du har gjort dessa val rutnätet för HTTP-begäranden är också interaktivt där valet av en enskild rad ändrar vilka data som återges i de två diagrammen längst ned i rapporten.

### <a name="editing-mode"></a>Redigeringsläge

För att förstå hur den här arbetsboksmallen sätts samman måste du byta till redigeringsläge genom att välja **Redigera**. 

![Skärmbild av analysmall för programfel](./media/workbooks-overview/edit.png)

När du har bytt till redigeringsläge kommer du att märka ett antal **Redigera** rutor visas till höger motsvarande med varje enskild aspekt av arbetsboken.

![Skärmbild av knappen Redigera](./media/workbooks-overview/edit-mode.png)

Om vi väljer redigeringsknappen omedelbart under rutnätet med begärandedata kan vi se att den här delen av vår arbetsbok består av en Kusto-fråga mot data från en Application Insights-resurs.

![Skärmbild av underliggande Kusto-fråga](./media/workbooks-overview/kusto.png)

Om du klickar på de andra **redigera** knapparna till höger visas ett antal av de kärnkomponenter som utgör arbetsböcker som markeringsbaserade [textrutor, gränssnittselement](workbooks-visualizations.md#text)för [parameterval](workbooks-parameters.md) och andra [diagram-/visualiseringstyper](workbooks-visualizations.md). 

Att utforska de färdiga mallarna i redigeringsläge och sedan ändra dem så att de passar dina behov och spara din egen anpassade arbetsbok är ett utmärkt sätt att börja lära sig mer om vad som är möjligt med Azure Monitor-arbetsböcker.

## <a name="pinning-visualizations"></a>Fästa visualiseringar

Steg för text, frågor och mått i en arbetsbok kan fästas med hjälp av pin-knappen på dessa objekt medan arbetsboken är i pin-läge, eller om arbetsboksförfattaren har aktiverat inställningar för elementet så att pin-ikonen synlig. 

Om du vill komma åt pin-läge klickar du på **Redigera** för att gå in i redigeringsläge och väljer ikonen för blå stift i det övre fältet. En enskild pin-ikon visas sedan ovanför varje motsvarande arbetsboksdels *redigerarruta* till höger på skärmen.

![Pin-upplevelse](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Arbetsbokens tillstånd sparas vid tidpunkten för stiftet och fästa arbetsböcker på en instrumentpanel uppdateras inte om den underliggande arbetsboken ändras. Om du vill uppdatera en fäst arbetsboksdel måste du ta bort och fästa om den delen.

## <a name="dashboard-time-ranges"></a>Tidsintervall för instrumentpanelen

Fästa arbetsboksfrågedelar respekterar instrumentpanelens tidsintervall om det fästa objektet är konfigurerat för att använda en *parameter för tidsintervall.* Instrumentpanelens tidsintervallvärde används som tidsintervallparameterns värde, och varje ändring av instrumentpanelens tidsintervall gör att det fästa objektet uppdateras. Om en fäst del använder instrumentpanelens tidsintervall visas underrubriken för den fästa delen för att visa instrumentpanelens tidsintervall när tidsintervallet ändras. 

Dessutom uppdateras fästa arbetsboksdelar med hjälp av en parameter för tidsintervall automatiskt med en hastighet som bestäms av instrumentpanelens tidsintervall. Den sista gången frågan kördes visas i undertexten till den fästa delen.

Om ett fäst steg har ett uttryckligen angått tidsintervall (använder inte en parameter för tidsintervall) används alltid det tidsintervallet för instrumentpanelen, oavsett instrumentpanelens inställningar. Undertexten för den fästa delen visar inte instrumentpanelens tidsintervall och frågan uppdateras inte automatiskt på instrumentpanelen. Undertexten visar förra gången frågan kördes.

> [!NOTE]
> Frågor som använder *den kopplade* datakällan stöds för närvarande inte när du fäster på instrumentpaneler.

## <a name="sharing-workbook-templates"></a>Dela arbetsboksmallar

När du har börjat skapa egna arbetsboksmallar kanske du vill dela den med den bredare gruppen. Om du vill veta mer och utforska andra mallar som inte ingår i standardvyn i Azure Monitor-galleriet finns i vår [GitHub-databas](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Om du vill bläddra bland befintliga arbetsböcker besöker du [arbetsboksbiblioteket](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) på GitHub.

## <a name="next-step"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
