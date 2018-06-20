---
title: Översikt över Azure Monitor
description: Azure Monitor samlar in statistik för aviseringar, webhooks, autoskala och automatisering. Artikel har också en lista över andra Microsoft-övervakningsalternativ.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: overview
ms.date: 03/28/2018
ms.author: robb
ms.custom: mvc
ms.component: ''
ms.openlocfilehash: a96991c424b4709002d46b6b7abe1e884c3605dd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264593"
---
# <a name="overview-of-azure-monitor"></a>Översikt över Azure Monitor
Den här artikeln ger en översikt över Azure Monitor Service i Microsoft Azure. Det beskriver vad Azure Monitor gör och innehåller länkar till ytterligare information om hur du använder Azure Monitor.  Om du föredrar en videointroduktion, se länkarna i Nästa steg längst ner i artikeln. 

## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure Monitor och Microsofts andra övervakningsprodukter
Azure Monitor ger grundläggande infrastruktursmått och loggfiler för de flesta tjänster i Microsoft Azure. Azure-tjänster som inte ännu placerar sina data på Azure Monitor på dem i framtiden.

Microsoft levererar ytterligare produkter och tjänster som erbjuder ytterligare övervakningsfunktioner för utvecklare, DevOps eller IT-Ops som också har lokala installationer. En översikt och förståelse av hur dessa olika produkter och tjänster samverkar finns i [övervakning i Microsoft Azure](monitoring-overview.md).

## <a name="portal-overview-page"></a>Portalöversiktssida

Azure Monitor har en landningssida som hjälper användarna att: 
- Förstå övervakningsfunktionerna som Azure erbjuder.
- Identifiera, konfigurera och kom igång med Azure-plattformen och premiumövervakningsfunktionerna.

Sidan är en startpunkt för navigering, inklusive hur du kommer igång. Det visar granskade anmärkningsvärda problem från olika tjänster och används för att navigera till dem i kontexten.
 
![Modellen för övervakning och diagnostik för icke-beräkningsresurser](./media/monitoring-overview-azure-monitor/monitor-overview-ux2.png)

När du öppnar sidan kan välja du bland de prenumerationer som du har läsbehörighet till. För en vald prenumeration kan du se:

- **Utlösta aviseringar och aviseringskällor** – den här tabellen visar en sammanfattning av inventeringar, varningskällor och hur många gånger aviseringar har aktiverats för den valda varaktigheten. Det gäller både äldre och nyare aviseringar. Läs mer om [nyare Azure aviseringar](monitoring-overview-unified-alerts.md). 
- **Aktivitetsloggfel** – om någon av dina logghändelser om Azure-resurser med allvarlighetsgraden felnivå. Du kan visa ett övergripande antal och klicka vidare till aktivitetsloggsidan för att utforska varje händelse.
- **Azure Service Health** – du kan se ett antal problem med tjänsten, planerade underhållshändelser och rekommendationer. Azure Service Health ger anpassad information när problem i Azure-infrastrukturen påverkar dina tjänster.  Se [Azure Service Health](../service-health/service-health-overview.md) för mer information.  
- **Application Insights** -Se KPI:er för varje AppInsights resurs i den aktuella prenumerationen. KPI:er som är optimerade för programmet för serversidans övervakning över ASP.NET-webbprogram, Java, Node och allmänna programtyper. KPI:er inkluderar mått för förfrågningar, varaktighet för svar, fel och tillgänglighet i procent. 

Om du inte har distribuerat Log Analytics eller Application Insights, eller om du inte har konfigurerat några Azure-aviseringar i den aktuella prenumerationen, innehåller sidan länkar om du vill påbörja distributionsprocessen.



## <a name="azure-monitor-sources---compute-subset"></a>Azure Monitor Sources - beräkningsdelmängd

![Modellen för övervakning och diagnostik för icke-beräkningsresurser](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)


Beräkningstjänsterna här imfattar 
- Cloud Services 
- Virtuella datorer 
- Skalningsuppsättningar för Virtual Machines 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Program - diagnostikloggar, programloggar och mått
Program kan köras ovanpå gästoperativsystemet i beräkningsmodellen. De genererar en egen uppsättning loggar och mått. Azure Monitor är beroende av tillägget Azure diagnostics (Windows eller Linux) för att samla in de flesta måtten och loggarna på programnivå. De innehåller

* Prestandaräknare
* Programloggar
* Windows-händelseloggar
* .NET-händelsekälla
* IIS-loggar
* Manifestbaserat ETW
* Kraschdumpar
* Kundfelloggar

Utan diagnostiktillägget finns bara några mått tillgängliga, till exempel CPU-användning. 

### <a name="host-and-guest-vm-metrics"></a>Måtten värd- och gäst-VM
Ovanstående beräkningsresurser har en dedikerad virtuell dator som värd och operativsystem som de nyttjar. Den virtuella värddatorn och gästoperativsystemet motsvarar den virtuella rotdatorn och den virtuella gästdatorn i Hyper-V-hypervisormodellen. Du kan samla in mått på båda. Du kan också samla in diagnostikloggar på gästoperativsystemet.   

### <a name="activity-log"></a>Aktivitetslogg
Du kan söka aktivitetsloggen (tidigare drift- eller granskningsloggar) för information om resursen som visas av Azure-infrastrukturen. Loggen innehåller information såsom tider när resurserna skapas eller förstörs.  Mer information finns i [Översikt över aktivitetsloggen](monitoring-overview-activity-logs.md). 

## <a name="azure-monitor-sources---everything-else"></a>Azure Monitor-källor - allt annat

![Modellen för övervakning och diagnostik för beräkningsresurser](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>Resurs - mätvärden och diagnostikloggar
Mått och diagnostikloggar som kan samlas in varierar baseras på resurstypen. Webbappar innehåller till exempel statistik på Disk-IO och CPU-användning i procent. Dessa mått finns inte för en Service Bus-kö som ger mått som köstorlekar och genomflöde av meddelanden. En lista över filmätvärden för varje resurs som är tillgänglig på [stödda mått](monitoring-supported-metrics.md). 

### <a name="host-and-guest-vm-metrics"></a>Måtten värd- och gäst-VM
Mappningen mellan din resurs och en viss virtuell värd- eller gästdator är kanske inte 1:1 så mått inte är tillgängliga.

### <a name="activity-log"></a>Aktivitetslogg
Aktivitetsloggen är samma som beräkningsresurser.  

## <a name="uses-for-monitoring-data"></a>Används för övervakning av data
När du samlar in data kan du göra följande med dem i Azure-Monitor.

### <a name="route"></a>Routa
Du kan strömma övervakningsdata till andra platser. 

Exempel:

- Skicka till Application Insights så att du kan använda dess utvecklade verktyg för visualisering och analys.
- Skicka dem till händelsehubbar så att du kan vidarebefordra dem till verktyg från tredje part. 

### <a name="store-and-archive"></a>Lagring och arkivering
Vissa övervakningsdata är redan lagrade och är tillgängliga i Azure-Monitor under en tidsperiod. 
- Mått lagras i 90 dagar. 
- Aktivitetsloggposter som lagras i 90 dagar. 
- Diagnostikloggar lagras inte alls. 

Om du vill lagra data som längre än de tider som anges ovan kan du använda en Azure-lagring. Övervakningsdata sparas i ditt lagringskonto baserat på en bevarandeprincip som du anger. Du måste betala för använt utrymme i Azure-lagring. 

Några sätt att använda dessa data:

- När dessa data har skrivits kan du låta andra verktyg i eller utanför Azure läsa eller bearbeta dem.
- Du kan häta data lokalt för ett lokalt arkiv eller ändra din bevarandeprincip i molnet för att skydda data under längre perioder.  
- Du kan lämna data i Azure-lagringen på obestämd tid. 

### <a name="query"></a>Fråga
Du kan använda Azure Monitor REST-API, kommandon i korsplattform-kommandoradsgränssnitt (CLI), PowerShell-cmdlets eller .NET SDK för att komma åt data i systemet eller Azure-lagring

Exempel:

* Hämta data för ett anpassat övervakningsprogram som du har skrivit
* Skapa egna frågor och skicka data till ett program från tredje part.

### <a name="visualize"></a>Visualisera
Visualisera dina övervakningsdata i grafik och diagram och hitta trender snabbare än att leta genom informationen.  

Visualiseringsmetoderna omfattar:

* Använda Azure-portalen
* Vidarebefordra data till Azure Application Insights
* Vidarebefordra data till Microsoft PowerBI
* Skicka data till tredje partsvisualiseringsverktyg med direktsänd strömning eller genom att läsa verktyget från ett arkiv i Azure-lagring


### <a name="automate"></a>Automatisera
> [!NOTE]
> Som en del av en pågående utveckling av aviseringar i Microsoft Azure, är en enhetlig miljö för aviseringar nu tillgänglig. Mer information om [nya Azure aviseringar](monitoring-overview-unified-alerts.md)

Du kan använda övervakningsdata för att utlösa aviseringar eller med hela processer i Azure-aviseringar. Exempel:

* Använd data för att autoskala beräkningsinstanser uppåt eller nedåt baserat på programbelastningen.
* Skicka e-postmeddelanden baserat på villkor som mått eller logg. 
* Anropa en URL (webhook) för att utföra en åtgärd i ett system utanför Azure
* Starta en runbook i Azure automation för att utföra en rad olika uppgifter

## <a name="methods-of-accessing-azure-monitor"></a>Metoder för att komma åt Azure Monitor
I allmänhet kan du ändra dataspårning, vidarebefordran och hämtning med någon av följande metoder. Inte alla metoder är tillgängliga för alla åtgärder eller datatyper.

* [Azure Portal](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Plattformsoberoende kommandoradsgränssnitt (CLI):](insights-cli-samples.md)
* [REST API](https://docs.microsoft.com/rest/api/monitor/)
* [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om
- En videogenomgång av bara Azure Monitor finns på  
[Kom igång med Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). 
- En video som förklarar ett scenario där du kan använda Azure Monitor finns på [Utforska Microsoft Azure-övervakning och -diagnostik](https://channel9.msdn.com/events/Ignite/2016/BRK2234) och [Azure Monitor i ett videoklipp från Ignite 2016](https://myignite.microsoft.com/videos/4977).
- Gå igenom Azure-Monitor-gränssnitt i [Komma igång med Azure-Monitor](monitoring-get-started.md)
- Ställ in tillägget [Azure Diagnostics](../azure-diagnostics.md) om du försöker att diagnostisera problem i din molntjänst, virtuella dator, skala för virtuell dator eller Service Fabric-programmet.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) om du försöker diagnostisera problem i din App Service Web app.
- [Felsöka Azure Storage](../storage/common/storage-e2e-troubleshooting.md) när du använder Storage-blobar, tabeller eller köer
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)
