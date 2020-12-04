---
title: Övervaka åtgärder, händelser och räknare för offentliga Basic-Load Balancer
titleSuffix: Azure Load Balancer
description: Lär dig hur du aktiverar loggning för Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572787"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Azure Monitor loggar för standard Azure Load Balancer

Du kan använda olika typer av loggar i Azure för att hantera och felsöka standard belastnings utjämning. Loggar kan strömmas till en händelsehubben eller en arbets yta för Log Analytics. Alla loggar kan extraheras från Azure Blob Storage och visas i olika verktyg, till exempel Excel och Power BI.  Du kan lära dig mer om de olika typerna av loggar i listan nedan.

* **Aktivitets loggar:** Du kan använda [Visa aktivitets loggar för att övervaka åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md) för att visa all aktivitet som skickas till din Azure-prenumeration (er) och deras status. Aktivitets loggar är aktiverade som standard och kan visas i Azure Portal. Dessa loggar är tillgängliga för både Basic-och standard belastningsutjämnare.
* **Standard Load Balancer mått:** Du kan använda den här loggen för att fråga de mått som exporteras som loggar för din standard Azure Load Balancer. De här loggarna är endast tillgängliga för standard belastnings utjämning.

> [!IMPORTANT]
> **Hälso avsökning och Load Balancer varnings händelse loggar fungerar inte för tillfället och anges i de [kända problemen för Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Loggar är bara tillgängliga för resurser som distribueras i distributions modellen för Resource Manager. Du kan inte använda loggar för resurser i den klassiska distributions modellen. Mer information om distributions modellerna finns i [förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivera loggning

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Aktivera loggning av händelse-och hälso avsökning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning.

Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har en belastningsutjämnare måste du [skapa en belastningsutjämnare](./quickstart-load-balancer-standard-public-portal.md) innan du fortsätter.

1. I portalen klickar du på **resurs grupper**.
2. Välj **\<resource-group-name>** var belastningsutjämnaren är.
3. Välj din lastbalanserare.
4. Välj **Activity log**  >  **diagnostiska inställningar** för aktivitets loggen.
5. I fönstret **diagnostikinställningar** , under **diagnostikinställningar**, väljer du **+ Lägg till diagnostisk inställning**.
6. I fönstret Skapa **diagnostikinställningar** anger du **MyLBDiagnostics** i fältet **namn** .
7. Det finns tre alternativ för **diagnostikinställningar**.  Du kan välja en, två eller alla tre och konfigurera var och en för dina behov:
   * **Arkivera till ett lagrings konto**
   * **Strömma till en händelsehubb**
   * **Skicka till Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arkivera till ett lagringskonto
    Du behöver ett lagrings konto som redan har skapats för den här processen.  Information om hur du skapar ett lagrings konto finns i [skapa ett lagrings konto](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Markera kryss rutan bredvid **arkivera till ett lagrings konto**.
    2. Välj **Konfigurera** för att öppna fönstret **Välj ett lagrings konto** .
    3. Välj den **prenumeration** där ditt lagrings konto skapades i den nedrullningsbara rutan.
    4. Välj namnet på ditt lagrings konto under **lagrings konto** i den nedrullningsbara List rutan.
    5. Välj OK.

    ### <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
    Du behöver en Event Hub som redan har skapats för den här processen.  Information om hur du skapar en Event Hub finns i [snabb start: skapa en Event Hub med Azure Portal](../event-hubs/event-hubs-create.md)

    1. Markera kryss rutan bredvid **strömma till en Event Hub**
    2. Välj **Konfigurera** för att öppna fönstret **Välj händelsehubben** .
    3. Välj den **prenumeration** där händelsehubben skapades i den nedrullningsbara rutan.
    4. **Välj namn område för Event Hub** i den nedrullningsbara rutan.
    5. **Välj princip namn för Event Hub** i den nedrullningsbara rutan.
    6. Välj OK.

    ### <a name="send-to-log-analytics"></a>Skicka till Log Analytics
    Du behöver redan ha en Log Analytics-arbetsyta skapad och konfigurerad för den här processen.  Om du vill skapa en Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md)

    1. Markera kryss rutan bredvid **Skicka till Log Analytics**.
    2. Välj den **prenumeration** där Log Analytics arbets ytan finns i den nedrullningsbara rutan.
    3. Välj **arbets ytan Log Analytics** i list rutan.


8.  Under avsnittet **mått** i fönstret **diagnostikinställningar** väljer du kryss rutan bredvid: **AllMetrics**

9. Kontrol lera att allting ser korrekt ut och klicka sedan på **Spara** högst upp i fönstret Skapa **diagnostiska inställningar** .

## <a name="activity-log"></a>Aktivitetslogg

Aktivitets loggen skapas som standard. Den kan konfigureras så att den exporteras på en prenumerations nivå enligt [instruktionerna i den här artikeln](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Läs mer om dessa loggar genom att läsa artikeln [Visa aktivitets loggar för att övervaka åtgärder i resurser](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitets logg data med någon av följande metoder:

* **Azure-verktyg:** Hämta information från aktivitets loggen via Azure PowerShell, kommando rads gränssnittet för Azure (CLI), Azure-REST API eller Azure Portal. Stegvisa instruktioner för varje metod beskrivs i artikeln [Granska åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing) konto kan du prova det kostnads fritt. Med hjälp av [integreringen av Azures gransknings loggar för Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/)kan du analysera dina data med förkonfigurerade instrument paneler, eller så kan du anpassa vyer så att de passar dina behov.

## <a name="metrics-as-logs"></a>Mått som loggar
Genom att använda mått för att logga export funktioner som tillhandahålls av Azure Monitor kan du exportera Load Balancer mått. Dessa mått genererar en loggpost för varje samplings intervall på en minut.

Mått till logg export är aktiverat på per resurs nivå. Du kan aktivera dessa loggar genom att gå till bladet diagnostikinställningar, filtrera efter resurs grupp och välja Load Balancer som du vill aktivera mått export för. När sidan Load Balancer diagnostikinställningar är upp väljer du AllMetrics för att exportera kvalificerade mått som loggar.

Se avsnittet [begränsningar](#limitations) i den här artikeln för begränsning av mått export.

### <a name="view-and-analyze-metrics-as-logs"></a>Visa och analysera mått som loggar
När du har aktiverat AllMetrics i diagnostikinställningar för din Standard Load Balancer, om du använder en Event Hub-eller Log Analytics-arbetsyta, fylls dessa loggar i tabellen AzureMonitor. Om du exporterar till Storage ansluter du till ditt lagrings konto och hämtar JSON-logg poster för händelse-och hälso avsöknings loggar. När du har hämtat JSON-filerna kan du konvertera dem till CSV och Visa i Excel, Power BI eller något annat verktyg för data visualisering. 

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.

## <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb
När diagnostikinformation strömmas till en Event Hub, kan den användas för centraliserad logg analys i ett SIEM-verktyg från tredje part med Azure Monitor-integrering. Mer information finns i [Stream Azure Monitoring data to Event Hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Skicka till Log Analytics
Resurser i Azure kan få sin diagnostikinformation att skickas direkt till en Log Analytics arbets yta där komplexa frågor kan köras mot informationen för fel sökning och analys.  Mer information finns i [samla in Azure-resursposter i Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Begränsningar
Det finns för närvarande följande begränsningar när du använder måtten för att logga export funktionen för belastningsutjämnare:
* Mått visas för närvarande med interna namn vid export som loggar som du kan hitta mappningen i tabellen nedan
* Måttets dimensionalitet bevaras inte. Till exempel, med mått som en DipAvailability (status för hälso avsökning), kommer du inte att kunna dela eller Visa IP-adressen för Server delen
* Använda SNAT-portar och allokerade SNAT-portars mått är för närvarande inte tillgängliga för export som loggar

## <a name="next-steps"></a>Nästa steg
* [Granska tillgängliga mått för din Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Skapa och testa frågor som följer Azure Monitor-instruktioner](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Ge feedback om den här artikeln eller Load Balancer funktioner med hjälp av länkarna nedan
