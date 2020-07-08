---
title: Översikt över Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med förbyggda och anpassade parameterstyrda arbets böcker
author: mrbullwinkle
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: b06f39de9f977a9f777b21a53e93ca744fccdd93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85560164"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor arbets böcker

Arbets böcker ger en flexibel arbets yta för data analys och skapandet av rika visuella rapporter i Azure Portal. De gör att du kan trycka på flera data källor i Azure och kombinera dem till enhetliga interaktiva upplevelser. 

## <a name="data-sources"></a>Datakällor

Arbets böcker kan fråga efter data från flera källor i Azure. Författare av arbets böcker kan transformera dessa data för att ge insikter om tillgänglighet, prestanda, användning och övergripande hälso tillstånd för de underliggande komponenterna. Till exempel analyserar prestanda loggar från virtuella datorer för att identifiera höga processor-eller låga minnes instanser och visa resultaten som ett rutnät i en interaktiv rapport.
  
Men den verkliga kraften i arbets böcker är möjligheten att kombinera data från olika källor i en enda rapport. På så sätt kan du skapa sammansatta resursvyer eller ansluta mellan resurser som möjliggör bättre data och insikter som annars skulle vara omöjlig.

Arbets böcker är för närvarande kompatibla med följande data Källor:

* [Loggar](workbooks-data-sources.md#logs)
* [Mått](workbooks-data-sources.md#metrics)
* [Azure Resource Graph](workbooks-data-sources.md#azure-resource-graph)
* [Aviseringar (förhandsversion)](workbooks-data-sources.md#alerts-preview)
* [Arbets belastnings hälsa](workbooks-data-sources.md#workload-health)
* [Azure Resource Health](workbooks-data-sources.md#azure-resource-health)
* [Azure-datautforskaren](workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualiseringar

Arbets böcker ger en omfattande uppsättning funktioner för visualisering av dina data. Detaljerade exempel på varje visualiserings typ kan du se exempel länkarna nedan:

* [Text](workbooks-visualizations.md#text)
* [Diagram](workbooks-visualizations.md#charts)
* [Rutnät](workbooks-visualizations.md#grids)
* [Ikoner](workbooks-visualizations.md#tiles)
* [Träd](workbooks-visualizations.md#trees)
* [Diagram](workbooks-visualizations.md#graphs)

![Exempel på arbets boks visualiseringar](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Komma igång

För att utforska arbets bokens upplevelse går du först till Azure Monitors tjänsten. Detta kan göras genom att skriva **Monitor** i sökrutan i Azure Portal.

Välj sedan **arbets böcker (förhands granskning)**.

![Skärm bild av knappen för hands version av arbets böcker markerade i en röd ruta](./media/workbooks-overview/workbooks-preview.png)

### <a name="gallery"></a>Galleri

Det tar dig till galleriet för arbets böcker:

![Skärm bild av vyn Azure Monitor arbets böcker Galleri](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Arbets böcker jämfört med mallar för arbets böcker

Du kan se en _arbets bok_ i grönt och ett antal _mallar för arbets böcker_ i lila. Mallar fungerar som granskade rapporter som har utformats för flexibel åter användning av flera användare och team. När du öppnar en mall skapas en tillfällig arbets bok som är ifylld med mallens innehåll. 

Du kan justera den mallbaserade arbets bokens parametrar och utföra analyser utan att behöva bryta den framtida rapporterings upplevelsen för kollegor. Om du öppnar en mall gör du några justeringar och väljer sedan ikonen Spara som du kommer att spara mallen som en arbets bok som sedan visas i grönt, vilket gör att den ursprungliga mallen inte vidrörs. 

Under huven skiljer sig mallarna också från sparade arbets böcker. När du sparar en arbets bok skapas en associerad Azure Resource Manager resurs, medan den tillfälliga arbets boken som skapas när du bara öppnar en mall inte har någon unik resurs kopplad till sig. Mer information om hur åtkomst kontroll hanteras i arbets böcker finns i artikeln om [åtkomst kontroll för arbets böcker](workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Utforska en mall för arbets böcker

Välj **program haveri analys** för att se en av standard mallarna för programbegäran.

![Skärm bild av mall för program haveri analys](./media/workbooks-overview/failure-analysis.png)

Som tidigare nämnts skapar den här mallen en tillfällig arbets bok så att du kan interagera med. Som standard öppnas arbets boken i läsläge som bara visar informationen för den avsedda analys upplevelsen som skapades av den ursprungliga mal len författare.

När det gäller den här arbets boken är upplevelsen interaktiv. Du kan justera prenumerationen, mål programmen och tidsintervallet för de data som du vill visa. När du har gjort dessa val är rutnätet för HTTP-förfrågningar också interaktivt genom att välja en enskild rad som ska ändra vilka data som återges i de två diagrammen längst ned i rapporten.

### <a name="editing-mode"></a>Redigerings läge

Du måste växla till redigerings läge genom att välja **Redigera**för att förstå hur den här arbets boks mal len placeras tillsammans. 

![Skärm bild av mall för program haveri analys](./media/workbooks-overview/edit.png)

När du har växlat till redigerings läget visas ett antal **redigerings** rutor till höger som motsvarar varje enskild aspekt av arbets boken.

![Skärm bild av knappen Redigera](./media/workbooks-overview/edit-mode.png)

Om vi väljer knappen Redigera omedelbart under rutnätet med begär ande data kan vi se att den här delen av arbets boken består av en Kusto-fråga mot data från en Application Insights-resurs.

![Skärm bild av underliggande Kusto-fråga](./media/workbooks-overview/kusto.png)

Om du klickar på de andra **redigerings** knapparna till höger visas ett antal kärn komponenter som utgör arbets böcker som markdown [text rutor](workbooks-visualizations.md#text), gränssnitts element för [parameter val](workbooks-parameters.md) och andra [typer av diagram/visualiseringar](workbooks-visualizations.md). 

Utforska de färdiga mallarna i redigerings läge och ändra dem efter dina behov och spara din egen anpassade arbets bok är ett utmärkt sätt att börja med att lära dig om vad som är möjligt med Azure Monitor arbets böcker.

## <a name="pinning-visualizations"></a>Fästa visualiseringar

Steg för text, frågor och mått i en arbets bok kan fästas med hjälp av knappen Fäst på dessa objekt när arbets boken är i PIN-läge, eller om arbets bokens författare har aktiverat inställningar för det elementet för att göra PIN-ikonen synlig. 

Om du vill komma åt PIN-läget klickar du på **Redigera** för att ange redigerings läge och väljer den blå PIN-ikonen i det översta fältet. En enskild PIN-ikon visas sedan ovanför varje motsvarande arbets boks dels *redigerings* ruta på höger sida av skärmen.

![PIN-upplevelse](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Status för arbets boken sparas vid tidpunkten för PIN-koden, och fästa arbets böcker på en instrument panel uppdateras inte om den underliggande arbets boken ändras. För att kunna uppdatera en fast arbets bok del måste du ta bort och fästa om den delen.

## <a name="dashboard-time-ranges"></a>Tids intervall för instrument panelen

Den fästa arbets bokens fråga delar kommer att respektera instrument panelens tidsintervall om det fasta objektet har kon figurer ATS för att använda en *tids intervalls* parameter. Instrument panelens tidsintervalls värde används som tids intervall parameter värde och eventuell ändring av instrument panelens tidsintervall innebär att det fästa objektet uppdateras. Om en fast del använder instrument panelens tidsintervall visas under rubriken för den fasta del uppdateringen för att visa instrument panelens tidsintervall när tidsintervallet ändras. 

Dessutom uppdateras fasta arbets boks delar med hjälp av en tidsintervalls parameter automatiskt enligt en hastighet som fastställs av instrument panelens tidsintervall. Den senaste gången då frågan kördes visas i under rubriken för den fästa delen.

Om ett fäst steg har ett explicit set-tidsintervall (använder inte en tidsintervall-parameter) används alltid det tidsintervallet för instrument panelen, oavsett inställningarna för instrument panelen. Under rubriken för den fästa delen kommer inte att visa instrument panelens tidsintervall, och frågan uppdateras inte automatiskt på instrument panelen. Under rubriken visar den tidpunkt då frågan kördes senast.

> [!NOTE]
> Frågor som använder data källan för *koppling* stöds inte för närvarande när du fäster på instrument paneler.

## <a name="sharing-workbook-templates"></a>Dela mallar för arbets böcker

När du börjar skapa egna mallar för arbets böcker kanske du vill dela den med större community. Om du vill veta mer och utforska andra mallar som inte ingår i standard galleriet för Azure Monitor besöker du vår [GitHub-lagringsplats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Om du vill bläddra bland befintliga arbets böcker går du till [arbets boks biblioteket](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) på GitHub.

## <a name="next-step"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
