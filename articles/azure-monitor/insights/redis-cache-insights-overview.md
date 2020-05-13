---
title: Azure Monitor för Azure cache för Redis (för hands version) | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för Redis Cache-funktionen som tillhandahåller Azure cache för Redis-ägare med en snabb förståelse för prestanda-och användnings problem.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210980"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Utforska Azure Monitor för Azure cache för Redis (för hands version)

Azure Monitor för Azure cache för Redis (för hands version) ger en överblick över övergripande prestanda, fel, kapacitet och drifts hälsa för alla dina Azure-Cacheer för Redis-resurser i en enhetlig interaktiv upplevelse. Den här artikeln hjälper dig att förstå fördelarna med den nya övervaknings miljön och hur du kan ändra och anpassa upplevelsen så att den passar organisationens unika behov.

## <a name="introduction"></a>Introduktion

Innan du simhopp i upplevelsen bör du förstå hur den visar och visualiserar information.

Den ger:

* I **skalnings perspektiv** för Azure cache för Redis-resurser över alla dina prenumerationer på en enda plats, med möjlighet att selektivt begränsa till de prenumerationer och resurser som du är intresse rad av att utvärdera.

* **Nedbrytnings analys** av en viss Azure-cache för Redis-resurs för att hjälpa till att diagnostisera problem eller utföra detaljerad analys efter kategori användning, fel, kapacitet och åtgärder. Om du väljer något av dessa alternativ visas en djupgående vy över relevanta.  

* **Anpassningsbar** – den här upplevelsen bygger på Azure Monitor mallar för arbets böcker som gör att du kan ändra vilka mått som visas, ändra eller ange tröskelvärden som överensstämmer med dina gränser och sedan spara dem i en anpassad arbets bok. Diagram i arbets böckerna kan sedan fästas på Azure-instrumentpaneler.  

Den här funktionen kräver inte att du aktiverar eller konfigurerar något, dessa Azure-cache för Redis samlas in som standard.

>[!NOTE]
>Det kostar inget att få åtkomst till den här funktionen och du debiteras bara för Azure Monitor viktiga funktioner som du konfigurerar eller aktiverar, enligt beskrivningen på sidan [pris information för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Visa användnings-och prestanda mått för Azure cache för Redis

Utför följande steg för att visa användning och prestanda för dina lagrings konton i alla dina prenumerationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **övervakare** och välj **övervaka**.

    ![Sökrutan med ordet "Monitor" och en listruta som säger tjänster "Monitor" med en hastighets format bild](./media/cosmosdb-insights-overview/search-monitor.png)

3. Välj **Azure-cache för Redis (för hands version)**.

Om det här alternativet inte är tillgängligt klickar du på **mer** och väljer **Azure cache för Redis**.

### <a name="overview"></a>Översikt

I **Översikt**visar tabellen interaktiv Azure-cache för Redis-mått. Du kan filtrera resultaten baserat på de alternativ du väljer i följande List rutor:

* **Prenumerationer – endast** prenumerationer som har en Azure-cache för Redis-resursen visas.  

* **Azure cache för Redis** – du kan välja alla, en delmängd eller en Azure-cache för Redis-resursen.

* **Tidsintervall** – som standard visar de senaste 4 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare under List rutor slår upp det totala antalet Azure cache för Redis-resurser finns i de valda prenumerationerna. Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar transaktions mått. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena.

Om du väljer en nedrullningsbar pil bredvid en av Azure-cachen för Redis-resurser visas en nedbrytning av prestanda måtten på den enskilda resurs nivån:

![Skärm bild av översikts upplevelsen](./media/redis-cache-insights-overview/overview.png)

Om du väljer resurs namnet för Azure cache för Redis är markerat i blått att du ska gå till standard **översikten** för det associerade kontot. Den visar,,,,,, `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` `CPU` `Connected Clients` `Cache Misses` `Errors (Max)` .

### <a name="operations"></a>Åtgärder

Välj **åtgärder** överst på sidan och **drift** delen av arbets bokens mall öppnas. Den visar `Total Operations` ,,, `Total Operations Timeline` `Operations Per Second` `Gets` , `Sets` .

![Skärm bild av översikts upplevelsen](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Användning

Välj **användning** överst på sidan och **användnings** delen av arbets boks mal len öppnas. Den visar `Cache Read` ,,, `Cache Read Timeline` `CacheWrite` `CacheHits` , `Cache Misses` .

![Skärm bild av översikts upplevelsen](./media/redis-cache-insights-overview/usage.png)

Välj **felen** överst på sidan och **felen** i arbets bokens mall öppnas. Den visar,,,,,, `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` `AOF/Errors` `Export/Errors` `Dataloss/Errors` `Import/Errors` .

![Skärm bild av problem med nedbrytning efter HTTP-begäran](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Mått definitioner

En fullständig lista över mått definitionerna som utgör dessa arbets böcker finns i [artikeln tillgängliga mått och rapporterings intervall](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals).

## <a name="pin-export-and-expand"></a>Fäst, exportera och expandera

Du kan fästa något av mått avsnitten på en Azure- [instrumentpanel](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) genom att välja kartnål-ikonen längst upp till höger i avsnittet.

![Mått avsnitt fäst vid instrument panelen exempel](./media/cosmosdb-insights-overview/pin.png)

Om du vill exportera data till Excel-formatet väljer du nedåtpilen till vänster om ikonen kartnål.

![Ikonen Exportera arbets bok](./media/cosmosdb-insights-overview/export.png)

Om du vill visa eller dölja alla nedrullningsbara List rutor i arbets boken väljer du ikonen Expandera till vänster om export ikonen:

![Ikonen Visa arbets bok](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Anpassa Azure Monitor för Azure cache för Redis (för hands version)

Eftersom den här upplevelsen bygger på Azure Monitor arbetsboksmall kan du **Anpassa**  >  **Redigera** och **Spara** en kopia av den ändrade versionen i en anpassad arbets bok. 

![Anpassa stapel](./media/cosmosdb-insights-overview/customize.png)

Arbets böcker sparas i en resurs grupp, antingen i avsnittet **Mina rapporter** som är privat för dig eller i avsnittet **delade rapporter** som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Starta arbets boks galleriet från kommando fältet](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Nästa steg

* Konfigurera [mått aviseringar](../platform/alerts-metric.md) och [meddelanden om tjänst hälsa](../../service-health/alerts-activity-log-service-notifications.md) för att ställa in automatiserad avisering som hjälper till att identifiera problem.

* Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../app/usage-workbooks.md).
