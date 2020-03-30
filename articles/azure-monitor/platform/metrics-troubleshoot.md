---
title: Felsöka Azure Monitor-måttdiagram
description: Felsöka problem med att skapa, anpassa eller tolka måttdiagram
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659670"
---
# <a name="troubleshooting-metrics-charts"></a>Felsöka måttdiagram

Använd den här artikeln om du stöter på problem med att skapa, anpassa eller tolka diagram i Azure metrics Explorer. Om du inte har gjort det för flera mått kan du läsa om [hur du kommer igång med statistikutforskaren](metrics-getting-started.md) och avancerade funktioner i [utforskaren för mått.](metrics-charts.md) Du kan också se [exempel på](metric-chart-samples.md) de konfigurerade måttdiagrammen.

## <a name="cant-find-your-resource-to-select-it"></a>Det går inte att hitta din resurs för att välja den

Du klickade på knappen **Välj en resurs**, men ser inte din resurs i resursväljaren.

**Lösning:** Statistikutforskaren kräver att du väljer prenumerationer och resursgrupper innan du listar tillgängliga resurser. Om du inte ser din resurs:

1. Kontrollera att du har valt rätt prenumeration i listrutan **Prenumeration**. Om prenumerationen inte visas klickar du på **Katalog + Prenumerationsinställningar** och lägger till en prenumeration med din resurs.

1. Kontrollera att du har valt rätt resursgrupp.
    > [!WARNING]
    > För bästa prestanda visas inga förvalda resursgrupper i listrutan **Resursgrupp** när du öppnar Metrics Explorer. Du måste välja minst en grupp innan du kan se några resurser.

## <a name="chart-shows-no-data"></a>Diagrammet visar inga data

Ibland kan diagrammen inte visa några data när du har valt rätt resurser och mått. Detta kan orsakas av flera av följande orsaker:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights-resursprovidern är inte registrerad för din prenumeration

*Microsoft.Insights*-resursprovidern måste vara registrerad för din prenumeration för att du ska kunna utforska mått. I många fall registreras den automatiskt (det vill säga när du har konfigurerat en aviseringsregel, anpassat diagnostikinställningar för resurser eller konfigurerat en regel för automatisk skalning). Om microsoft.insights-resursleverantören inte är registrerad måste du registrera den manuellt genom följande steg som beskrivs i [Azure-resursleverantörer och -typer](../../azure-resource-manager/management/resource-providers-and-types.md).

**Lösning:** Öppna **fliken Prenumerationer,** **Resursleverantörer** och kontrollera att *Microsoft.Insights* är registrerat för din prenumeration.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Du har inte tillräckliga åtkomsträttigheter för resursen

I Azure styrs åtkomsten till mått med [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Du måste vara medlem i någon av grupperna [Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Övervakningsdeltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor), eller [Deltagare](../../role-based-access-control/built-in-roles.md#contributor) för att kunna utforska mått för resurser.

**Lösning:** Kontrollera att du har tillräcklig behörighet för den resurs som du utforskar mått från.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Resursen genererade inga mått under det valda tidsintervallet

Vissa resurser genererar inte mått regelbundet. Exempelvis samlar inte Azure in mått för stoppade virtuella datorer. Andra resurser kanske bara genererar mått vid särskilda händelser. Till exempel kräver ett mått som visar bearbetningstiden för en transaktion att minst en transaktion skett. Om det inte finns några transaktioner i det valda tidsintervallet, kommer diagrammet givetvis att vara tomt. Och även om de flesta måtten i Azure samlas in varje minut, finns det några som samlas in mer sällan. I dokumentationen för mått finns mer information om det mått som du vill utforska.

**Lösning:** Ändra tiden för diagrammet till ett bredare intervall. Du kan börja från "Senaste 30 dagarna" med en större tidsgranularitet (eller förlita dig på alternativet "Automatisk tidsgranularitet").

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Du har valt ett tidsintervall som är längre än 30 dagar

[De flesta mått i Azure lagras i 93 dagar](data-platform-metrics.md#retention-of-metrics). Dock kan du högst fråga efter data för 30 dagar i ett enskilt diagram. Den här begränsningen gäller inte för [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Lösning:** Om du ser ett tomt diagram eller om diagrammet bara visar en del av måttdata kontrollerar du att skillnaden mellan start- och slutdatum i tidsväljaren inte överskrider 30-dagarsintervallet.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Alla mått låg utanför det låsta y-axelintervallet

Genom att [låsa gränserna för y-axeln i diagrammet](metrics-charts.md#lock-boundaries-of-chart-y-axis) kan du oavsiktligt göra så att diagramlinjen inte visas i diagrammets visningsområdet. Om y-axeln exempelvis är låst till ett intervall mellan 0 % och 50 % och måttet har ett konstant värde på 100 %, återges linjen alltid utanför det synliga området, vilket gör att diagrammet verkar vara tomt.

**Lösning:** Kontrollera att y-axelns gränser för diagrammet inte är låsta utanför intervallet för måttvärdena. Om gränserna för y-axeln är låsta kan du tillfälligt återställa dem så att måttvärdena inte faller utanför diagramintervallet. Vi avråder dig från att låsa y-axelintervallet med automatisk kornighet för diagram med aggregeringar som **summa**, **min** och **max** eftersom deras värden ändras med kornigheten när webbläsarfönstret eller skärmupplösningen ändras. Om du ändrar kornigheten kan det göra att du inte ser något i visningsområdet i diagrammet.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Du tittar på ett gästoperativsystem-mått men har inte aktiverat Azure Diagnostic Extension

Insamling av mått för **gästoperativsystem** kräver att Azure Diagnostics-tillägget konfigureras eller aktiveras via panelen **Diagnostikinställningar** för din resurs.

**Lösning:** Om Azure Diagnostics Extension är aktiverat men du fortfarande inte kan se dina mått följer du stegen i [felsökningsguiden](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)för Azure Diagnostics Extension . Se även felsökningsstegen för [Inte välja Gästoperativsystemnamnområde och mått](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Meddelandet "Fel vid hämtning av data" på instrumentpanelen

Det här problemet kan inträffa när instrumentpanelen har skapats med ett mått som senare blivit inaktuellt och tagits bort från Azure. Du kan kontrollera om detta är vad som hänt genom att öppna fliken **Mått** för din resurs och markera de tillgängliga måtten i måttväljaren. Om måttet inte visas, betyder det att det har tagits bort från Azure. När ett mätvärde har blivit inaktuellt och tagits bort finns det normalt ett nytt bättre mått som ger liknande information om resurshälsan.

**Lösning:** Uppdatera den felaktiga panelen genom att välja ett alternativt mått för diagrammet på instrumentpanelen. Du kan [granska en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Diagrammet visar streckad linje

Azure mått diagram använder streckade linjeformat för att ange att det finns ett värde som saknas (även känd som "null-värde") mellan två kända tid korn datapunkter. Om du till exempel i tidsväljaren valde "1 minut" tidsgranularitet men måttet rapporterades vid 07:26, 07:27, 07:29 och 07:30 (notera ett minutgap mellan andra och tredje datapunkter), ansluter en streckad linje 07:27 och 07:29 och en heldragen linje ansluter 07:27 och 07:29 och en heldragen linje ansluter 07:27 och 07:29 och en heldragen linje ansluter 07:27 och 07:29 och en heldragen linje ansluter 07:27 och 07:29 och en heldragen linje ansluter 07:27 och 07:29 och en heldragen linje ansluts 07:27 och 07:29 och en heldragen linje alla andra datapunkter. Den streckade linjen sjunker ner till noll när måttet använder **antal** och **summera** aggregering. För **avg-** **eller maxaggregeringarna** ansluter den streckade linjen två närmaste kända datapunkter. **max** Och om data saknas längst till höger eller längst till vänster i diagrammet expanderas den streckade linjen i riktningen mot datapunkten som saknas.
  ![bild på mått](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Lösning:** Detta är avsiktligt. Detta är användbart för att identifiera datapunkter som saknas. Linjediagrammet är ett överlägset val för att visualisera trender för mätvärden med hög densitet, men kan vara svårt att tolka för måtten med glesa värden, särskilt när det är viktigt att anpassa värden med tidskorn. Den streckade linjen gör det enklare att läsa dessa diagram, men om diagrammet fortfarande är svårtolkat bör du överväga att visa måtten i en annan typ av diagram. Ett punktdiagram för samma mått visar till exempel tydligt varje tidskorn genom att bara visualisera en punkt när det ![finns ett värde och hoppa över datapunkten helt när värdet saknas: måttbild](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Om du ändå föredrar ett linjediagram för dina mått kan det vara enklare att utvärdera tidskornigheten genom att föra musen över diagrammet så att datapunkten under muspekaren markeras.

## <a name="chart-shows-unexpected-drop-in-values"></a>Diagrammet visar oväntade värdesänkningar

Detta är ofta en feltolkning av diagrammets data. Summor eller antal kan falla när de senaste minuterna visas i diagrammet eftersom Azure inte har fått eller hunnit bearbeta de senaste måttdatapunkterna än. Beroende på tjänst tar det ett par minuter att bearbeta måtten. För diagram som visar ett nytt tidsintervall med en 1- eller 5-minuters granularitet, ![blir en droppe av värdet under de senaste minuterna mer märkbar: metrisk bild](./media/metrics-troubleshoot/drop-in-values.png)

**Lösning:** Detta är avsiktligt. Vi tror att det är bäst att visa data så fort de blir tillgängliga även om de är *partiella* eller *ofullständiga*. På så sätt kan du dra viktiga slutsatser snabbare och börja undersöka direkt. Om du till exempel ser delvärdet X för ett mått som visar antalet fel, betyder det att minst X fel inträffade under en viss minut. Du kan börja undersöka problemet direkt, i stället för att vänta och se det exakta antalet fel som inträffade under minuten, vilket kanske inte är så viktigt. Diagrammet uppdateras när vi får hela uppsättningen data, men då kan det också visa nya ofullständiga datapunkter från senare minuter.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Det går inte att välja namnområde och mått för gästoperativsystem

Virtuella datorer och skalningsuppsättningar för virtuella datorer har två kategorier av mått: Värden för **virtuella datorer** som samlas in av Azure-värdmiljön och **Gästoperativsystem (klassiska)** mått som samlas in av [övervakningsagenten](agents-overview.md) som körs på dina virtuella datorer. Du installerar övervakningsagenten genom att aktivera [Azure Diagnostics-tillägget](diagnostics-extension-overview.md).

Som standard lagras mått för gästoperativsystem i ett Azure Storage-konto, som du väljer från fliken **Diagnostikinställningar** för din resurs. Om inga mått för gästoperativsystem samlas in eller om Metrics Explorer inte kan komma åt dem visas bara namnrymden för **Virtuell värddator**-måttet:

![bild på mått](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Lösning:** Om du inte ser **Gästoperativsystem (klassiskt)** namnområde och mått i statistikutforskaren:

1. Bekräfta att [Azure Diagnostics-tillägget](diagnostics-extension-overview.md) är aktiverat och konfigurerat att samla in mått.
    > [!WARNING]
    > Du kan inte använda [Log Analytics-agenten](agents-overview.md#log-analytics-agent) (kallas även Microsoft Monitoring Agent eller MMA) för att skicka **Gästoperativsystem** till ett lagringskonto.

1. Kontrollera att **Microsoft.Insights** resursleverantör [är registrerad för din prenumeration](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Kontrollera att lagringskontot inte skyddas av brandväggen. Azure Portal behöver åtkomst till lagringskontot för att kunna hämta måttdata och rita diagrammen.

1. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera att måtten matas till lagringskontot. Om måtten inte samlas in följer du anvisningarna i [felsökningsguiden för Azure Diagnostics-tillägget](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om hur du kommer igång med Metric Explorer](metrics-getting-started.md)
* [Lär dig mer om avancerade funktioner i Metric Explorer](metrics-charts.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Visa exempel på konfigurerade diagram](metric-chart-samples.md)
