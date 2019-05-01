---
title: Felsöka Azure Monitor måttdiagram
description: Felsöka problem med att skapa, anpassa eller tolka måttdiagram
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939575"
---
# <a name="troubleshooting-metrics-charts"></a>Felsöka måttdiagram

Använd den här artikeln om du stöter på problem med att skapa, anpassa eller tolka diagram i Azure metrics explorer. Om du är nybörjare till mått kan du läsa om [komma igång med måttutforskaren](metrics-getting-started.md) och [avancerade funktioner i måttutforskaren](metrics-charts.md). Du kan också se [exempel](metric-chart-samples.md) av konfigurerade måttdiagram.

## <a name="cant-find-your-resource-to-select-it"></a>Det går inte att hitta resursen att välja den

Du klickade på den **väljer du en resurs** knappen, men inte ser din resurs i resurs objektväljare, dialogruta.

**Lösning:** Måttutforskaren måste du välja prenumerationer och resursgrupper innan lista över tillgängliga resurser. Om du inte ser din resurs:

1. Se till att du har valt rätt prenumeration i den **prenumeration** listrutan. Om prenumerationen inte visas klickar du på den **katalog + prenumeration inställningar** och lägga till en prenumeration med din resurs.

1. Se till att du har valt rätt resursgruppen.
    > [!WARNING]
    > För bästa prestanda när du först öppnar metrics explorer kan den **resursgrupp** listrutan har inga förvalda resursgrupper. Du måste välja minst en grupp innan du kan se några resurser.

## <a name="chart-shows-no-data"></a>Diagrammet visar inga data

Diagrammen kan ibland visar inga data när du har valt rätt resurser och mått. Detta kan bero på flera av följande orsaker:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Resursprovidern Microsoft.Insights är inte registrerad för din prenumeration

Utforska mått kräver *Microsoft.Insights* resursprovidern registrerad för din prenumeration. I många fall kan registreras den automatiskt (det vill säga när du konfigurerar en aviseringsregel, anpassa diagnostikinställningar för alla resurser eller konfigurera en regel för automatisk skalning). Om resursprovidern Microsoft.Insights inte har registrerats, måste du manuellt registrera den genom att följa stegen som beskrivs i [Azure resursproviders och resurstyper](../../azure-resource-manager/resource-manager-supported-services.md).

**Lösning:** Öppna **prenumerationer**, **resursprovidrar** och kontrollera som *Microsoft.Insights* är registrerad för din prenumeration.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Du behöver inte tillräckliga åtkomsträttigheter för din resurs

I Azure, åtkomst till mätvärden styrs av [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Du måste vara medlem i [övervaka läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader), [övervaka deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor), eller [deltagare](../../role-based-access-control/built-in-roles.md#contributor) att utforska mått för alla resurser.

**Lösning:** Kontrollera att du har tillräcklig behörighet för resursen som du utforskar mått.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Resursen kunde inte generera mått under det valda tidsintervallet

Vissa resurser genererar inte ständigt sina mått. Azure kommer exempelvis inte samla in mått för stoppade virtuella datorer. Andra resurser kan generera sina mått endast när vissa villkor uppfylls. Till exempel kräver ett mått som visar bearbetningstiden för en transaktion minst en transaktion. Om det finns inga transaktioner i det valda tidsintervallet, diagrammet naturligt att vara tomt. Dessutom kan de flesta av mått i Azure har samlats in varje minut, men det finns några som samlas in mer sällan. I dokumentationen till mått och få mer information om det mått som du vill utforska.

**Lösning:** Ändra tiden för diagrammet till ett bredare spektrum. Du kan starta från ”senaste 30 dagarna” med hjälp av en större tidskornighet (eller förlita sig på alternativet ”Automatisk tidskornighet”).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Du har valt ett tidsintervall som är större än 30 dagar

[De flesta mått i Azure lagras i 93 dagar](data-platform-metrics.md#retention-of-metrics). Du kan dock endast fråga efter mer än 30 dagar med data på ett enkelt diagram. Den här begränsningen gäller inte för [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Lösning:** Om du ser ett tomt diagram eller diagrammet visas bara en del av mätvärden, kontrollerar du att skillnaden mellan start - och -slutdatum i tidsväljare inte överskrider 30-dagarsintervall.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Alla mått värden var utanför låst y-axelintervall

Genom att [låsning gränserna för y-axeln i diagrammet](metrics-charts.md#lock-boundaries-of-chart-y-axis), oavsiktligt du visningsområdet diagrammet visar inte diagrammet raden. Till exempel om y-axeln är låst till ett intervall mellan 0 och 50% och måttet har ett konstant värde på 100%, återges raden alltid utanför området synliga gör diagrammet är tomma.

**Lösning:** Kontrollera att gränserna för y-axeln i diagrammet inte är låst utanför mått värdeintervallet. Om gränserna för y-axeln är låst kan du tillfälligt återställa dem för att säkerställa att måttet värden inte faller utanför diagramområdet. Låsning y-axelintervall rekommenderas inte med automatisk kornighet för diagram med **summan**, **min**, och **max** aggregering eftersom deras värden ändras med kornighet genom att ändra storlek på webbläsarfönster eller kommer från en skärmupplösning till en annan. Byta kornighet kan lämna den diagrammet tomt visningsområdet.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Du tittar på ett gäst-OS-mått, men aktiverar inte Azure-Diagnostiktillägget

Insamling av **gäst-OS** mått kräver att konfigurera Azure Diagnostics-tillägget eller aktivera den med hjälp av den **diagnostikinställningar** panelen för din resurs.

**Lösning:** Om Azure Diagnostics-tillägget har aktiverats men du kan fortfarande inte att visa dina mått, följer du stegen som beskrivs i [felsökningsguide för Azure Diagnostics-tillägget](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Se även felsökningsstegen för [kan inte välja gäst-OS-namnområde och mått](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Meddelandet ”fel vid hämtning av data” på instrumentpanelen

Det här problemet är vanlig när instrumentpanelen har skapats med ett mått som senare inaktuell och tas bort från Azure. Kontrollera att det är fallet, öppna den **mått** fliken av din resurs och markera tillgängliga mått i väljaren för mått. Om måttet inte visas, har måttet tagits bort från Azure. Vanligtvis när en mätvärdet är inaktuellt, finns det ett bättre nya mått som ger resurshälsan liknande perspektiv.

**Lösning:** Uppdatera panelen misslyckas genom att välja ett annat mått i diagrammet på instrumentpanelen. Du kan [granska en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Diagrammet visar streckad linje

Azure måttdiagram används streckad linjeformatet för att indikera att det finns ett saknat värde (även kallat ”null-värde”) mellan två kända tidskorn datapunkter. Exempel: om i väljaren för tid du valt ”minut” tidskornighet men måttet rapporterades vid 07:26, 07:27 07:29 och 07:30 (Observera att en minut mellanrummet mellan datapunkter som andra och tredje), en streckad linje ska ansluta 07:27 och 07:29 och ansluter en heldragen linje andra datapunkter. Streckad linje släpper ned till noll när måttet använder **antal** och **summan** aggregering. För den **genomsnittlig**, **min** eller **max** aggregeringar, den streckade linjen ansluter två närmaste kända datapunkter. Dessutom när data saknas på längst till höger eller längst till vänster sida av diagrammet, expanderar den streckade linjen till riktning för den sakna datapunkten.
  ![mått-avbildning](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Lösning:** Det här beteendet är avsiktligt. Det är användbart för att identifiera saknade datapunkter. Linjediagrammet är ett överlägset alternativ för att visualisera trender Högdensitet mått, men kan vara svåra att tolka för mått med null-optimerade värden, särskilt när corelating värden med tidsintervallet är viktigt. Den streckade linjen gör läsning av dessa diagram enklare, men om diagrammet fortfarande oklart kan du visa dina mått med en annan diagramtyp. Till exempel ett utspridd diagram för samma mått tydligt visar varje tidsintervall genom att endast visualisera en punkt när det finns ett värde och hoppar över data för att helt och hållet när värdet saknas: ![mått bild](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Om du fortfarande vill ett linjediagram för dina mått kan flytta musen över diagrammet hjälpa dig för att utvärdera tidskornighet genom att markera datapunkt på platsen för muspekaren.

## <a name="chart-shows-unexpected-drop-in-values"></a>Diagrammet visar oväntat fall i värden

I många fall är upplevd dippen i måttvärdena en missförstånd data som visas i diagrammet. Du kan vara vilseleds genom en minskning av summor eller räknar när diagrammet visar de senaste minuterna eftersom senaste mått datapunkter inte har tagits emot eller bearbetas av Azure ännu. Svarstiden för bearbetning av mått kan vara inom intervallet för ett par minuter beroende på tjänsten. Diagram som visar ett senaste tidsintervall med en kornighet för 1 eller 5 minuter en minskning av värde under de senaste minuterna blir bättre: ![mått bild](./media/metrics-troubleshoot/drop-in-values.png)

**Lösning:** Det här beteendet är avsiktligt. Vi tror att visar data så fort vi får är bra även när data är *partiella* eller *ofullständig*. Gör det möjligt att göra viktiga slutsats snabbare och starta undersökning direkt. Till exempel för ett mått som visar antalet fel, se ett delvärde X talar om att det fanns minst X fel på en viss minut. Du kan börja undersöka problemet direkt, snarare än väntetiden för att se exakt antal fel som inträffat på den här minut, vilket inte kanske är lika viktigt. Diagrammet uppdateras när vi får hela uppsättningen av data, men då eventuellt visas också nya ofullständig datapunkter från nyare minuter.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Det går inte att välja gäst-OS-namnområde och mått

Virtuella datorer och skalningsuppsättningar för virtuella datorer har du två kategorier av mätvärden: **Virtuell värddator** mått som samlas in av Azure värdmiljön och **gäst-OS** mått som samlas in av den [övervakningsagent](agents-overview.md) som körs på dina virtuella datorer. Installation av övervakningsagenten genom att aktivera [Azure-Diagnostiktillägget](diagnostics-extension-overview.md).

Som standard lagras gäst-OS-mått i Azure Storage-konto som du väljer från den **diagnostikinställningar** fliken för din resurs. Om inte är gäst-OS-mått som samlas in eller måttutforskaren kan inte komma åt dem, visas bara den **virtuell värddator** mått namnområde:

![mått-avbildning](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Lösning:** Om du inte ser **gäst-OS** namnområde och mått i metrics explorer:

1. Bekräfta att [Azure-Diagnostiktillägget](diagnostics-extension-overview.md) är aktiverad och konfigurerad för att samla in mått.
    > [!WARNING]
    > Du kan inte använda [Log Analytics-agenten](agents-overview.md#log-analytics-agent) (kallas även Microsoft Monitoring Agent eller ”MMA”) att skicka **gäst-OS** till ett lagringskonto.

1. Kontrollera att lagringskontot inte är skyddad av brandväggen.

1. Använd den [Azure Lagringsutforskaren](https://azure.microsoft.com/features/storage-explorer/) att verifiera att mått flödar till lagringskontot. Om mått som inte samlas in, följer du de [felsökningsguide för Azure Diagnostics-tillägget](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om att komma igång med Metric Explorer](metrics-getting-started.md)
* [Lär dig mer om de avancerade funktionerna i Metric Explorer](metrics-charts.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](metrics-supported.md)
* [Se exempel på konfigurerade diagram](metric-chart-samples.md)