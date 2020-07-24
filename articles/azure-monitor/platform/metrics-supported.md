---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 81e2abc1b4fd0c540b08f96e6b34c16fca3319f1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132024"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras i stort sett automatiskt från Azure Monitor mått REST API. Alla ändringar som görs i den här listan via GitHub kan skrivas över utan varning. Kontakta författaren till den här artikeln om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. 

Den här artikeln är en fullständig lista över alla plattformar (dvs. automatiskt insamlade) mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Listan senast uppdaterades mars 27 2020. Mått som har ändrats eller lagts till efter det här datumet får inte visas nedan. Om du vill fråga efter och komma åt listan över mått program mässigt kan du använda [2018-01-01 API-versionen](/rest/api/monitor/metricdefinitions). Andra mått som inte finns med i listan kan finnas tillgängliga i portalen eller med äldre API: er.

Måtten är ordnade efter resurs leverantörer och resurs typ. En lista över tjänster och resurs leverantörer som hör till dem finns i [Resource providers för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md). 

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportera plattforms mått till andra platser

Du kan exportera plattforms måtten från Azure Monitor-pipeline till andra platser på ett av två sätt.
1. Använd [måtten REST API](/rest/api/monitor/metrics/list)
2. Använd [diagnostikinställningar](diagnostic-settings.md) för att dirigera plattforms mått till 
    - Azure Storage
    - Azure Monitor loggar (och därmed Log Analytics)
    - Event Hub, som är hur du får dem till andra system än Microsoft 

Att använda diagnostikinställningar är det enklaste sättet att dirigera mått, men det finns vissa begränsningar: 

- Det går **inte att exportera** – alla mått kan exporteras med hjälp av REST API, men vissa kan inte exporteras med diagnostikinställningar på grund av erna i Azure Monitor-Dataservern. Kolumnen som *exporteras via diagnostikinställningar* i tabellerna nedan visar vilka mått som kan exporteras på det här sättet.  

- **Flerdimensionella mått** – att skicka flerdimensionella mått till andra platser via diagnostikinställningar stöds inte för närvarande. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden. *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.

## <a name="guest-os-and-host-os-metrics"></a>Mått för gäst operativ system och värd operativ system

> [!WARNING]
> Mått för gäst operativ systemet (gäst operativ system) som körs i Azure Virtual Machines, Service Fabric och Cloud Services visas **inte** här. Gäst operativ systemets mått måste samlas in via en eller flera agenter som körs på eller som en del av gäst operativ systemet.  Gäst operativ systemets mått inkluderar prestanda räknare som spårar gäst processor procent eller minnes användning, som båda används ofta för automatisk skalning eller avisering. 
>
> **Värd-OS-mått är tillgängliga och visas nedan.** De är inte samma. Värd-OS-måtten relaterar till Hyper-V-sessionen som är värd för din gäst operativ system session. 

> [!TIP]
> Bästa praxis är att använda och konfigurera [Azure-diagnostik-tillägget](diagnostics-extension-overview.md) för att skicka gäst operativ systemets prestanda mått till samma Azure Monitor mått databas där plattforms måtten lagras. Tillägget dirigerar gäst operativ systemets mått via [anpassade mått](metrics-custom-overview.md) -API: et. Sedan kan du Diagrama, Varna och annars använda gäst operativ systemets mått som plattforms mått. Alternativt kan du också använda Log Analytics agent för att skicka gäst operativ systemets mått till Azure Monitor loggar/Log Analytics. Du kan fråga efter dessa mått i kombination med icke-metriska data. 

Viktig ytterligare information finns i [Översikt över övervaknings agenter](agents-overview.md).    

## <a name="table-formatting"></a>Tabellformatering

> [!IMPORTANT] 
> Den här senaste uppdateringen lägger till en ny kolumn och sorterat om måtten som alfabetiska. Ytterligare information innebär att tabellerna nedan kan ha en vågrät rullnings List längst ned, beroende på webbläsarfönstrets bredd. Om du tror att du saknar information använder du rullnings listen för att visa hela tabellen.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Minne: aktuellt pris för renare|Antal|Medelvärde|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Minne: det går inte att krympa rengörings minnet|Byte|Medelvärde|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Minne: krympnings utrymme för rensning|Byte|Medelvärde|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Trådar: pool med upptagna kommando trådar|Antal|Medelvärde|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Trådar: inaktiva trådar för kommando pool|Antal|Medelvärde|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Kölängd för kommando bassäng|Antal|Medelvärde|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|CurrentConnections|Yes|Anslutning: aktuella anslutningar|Antal|Medelvärde|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuella användarsessioner|Antal|Medelvärde|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|LongParsingBusyThreads|Yes|Trådar: lång parsning av upptagna trådar|Antal|Medelvärde|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Yes|Trådar: inaktiva trådar för lång parsning|Antal|Medelvärde|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Trådar: lång parsning av jobb Kölängd|Antal|Medelvärde|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|mashup_engine_memory_metric|Yes|M motor minne|Byte|Medelvärde|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|M motor privata byte|Byte|Medelvärde|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|M-motor QPU|Antal|Medelvärde|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Virtuella byte i M-motor|Byte|Medelvärde|Virtuella byte som används av kombinations motor processer.|ServerResourceType|
|memory_metric|Yes|Minne|Byte|Medelvärde|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|memory_thrashing_metric|Yes|Minnesförslöing|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|ServerResourceType|
|MemoryLimitHard|Yes|Minne: minnes gräns hårt|Byte|Medelvärde|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Yes|Minne: minnes gräns hög|Byte|Medelvärde|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Yes|Minne: minnes gräns låg|Byte|Medelvärde|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Minne: minnes gräns VertiPaq|Byte|Medelvärde|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|MemoryUsage|Yes|Minne: minnes användning|Byte|Medelvärde|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|private_bytes_metric|Yes|Privata byte|Byte|Medelvärde|Privata byte.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Medelvärde|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Medelvärde|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Medelvärde|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Medelvärde|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Bearbetar Kölängd för poolen|Antal|Medelvärde|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|qpu_metric|Yes|QPU|Antal|Medelvärde|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Upptagna trådar för frågeprocessorn|Antal|Medelvärde|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Trådar: inaktiva trådar i lagringspoolen|Antal|Medelvärde|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Trådar: Kölängd för jobbkö|Antal|Medelvärde|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|Kvot|Yes|Minne: kvot|Byte|Medelvärde|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Yes|Minne: kvot blockerad|Antal|Medelvärde|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|RowsConvertedPerSec|Yes|Bearbetar: rader konverterade per sekund|CountPerSecond|Medelvärde|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsReadPerSec|Yes|Bearbetar: rader lästa per sekund|CountPerSecond|Medelvärde|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsWrittenPerSec|Yes|Bearbetar: rader skrivna per sekund|CountPerSecond|Medelvärde|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Trådar: kort parsning upptagna trådar|Antal|Medelvärde|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Trådar: kort parsning inaktiva trådar|Antal|Medelvärde|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Trådar: kort parsning av jobb Kölängd|Antal|Medelvärde|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Lyckade anslutningar per sekund|CountPerSecond|Medelvärde|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Yes|Totalt antal anslutnings problem|Antal|Medelvärde|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|TotalConnectionRequests|Yes|Totalt antal anslutnings begär Anden|Antal|Medelvärde|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|VertiPaqNonpaged|Yes|Minne: VertiPaq som inte är växlat|Byte|Medelvärde|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Yes|Minne: VertiPaq växlat|Byte|Medelvärde|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|virtual_bytes_metric|Yes|Virtuella byte|Byte|Medelvärde|Virtuella byte.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Varaktighet för backend-begäranden|Millisekunder|Medelvärde|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Yes|Kapacitet|Procent|Medelvärde|Användnings mått för API Management-tjänsten|Plats|
|Varaktighet|Yes|Total varaktighet för gateway-begäranden|Millisekunder|Medelvärde|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|EventHubDroppedEvents|Yes|Ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Plats|
|EventHubRejectedEvents|Yes|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Plats|
|EventHubSuccessfulEvents|Yes|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Plats|
|EventHubThrottledEvents|Yes|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Plats|
|EventHubTimedoutEvents|Yes|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Antal inaktuella EventHub-händelser|Plats|
|EventHubTotalBytesSent|Yes|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Plats|
|EventHubTotalEvents|Yes|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Plats|
|EventHubTotalFailedEvents|Yes|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Plats|
|FailedRequests|Yes|Misslyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|NetworkConnectivity|Yes|Status för resurs för nätverks anslutning (förhands granskning)|Antal|Medelvärde|Nätverks anslutnings status för beroende resurs typer från API Management-tjänsten|Plats, ResourceType|
|OtherRequests|Yes|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Begäranden|Yes|Begäranden|Antal|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Lyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|TotalRequests|Yes|Totalt antal Gateway-begäranden (inaktuella)|Antal|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Yes|Obehöriga Gateway-begäranden (inaktuella)|Antal|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|StatusCode, autentisering|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Antal|Medelvärde|Svars tid på en http-begäran.|StatusCode, autentisering|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Antal|Antal|Begränsade HTTP-begäranden.|Inga dimensioner|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|JVM. gc. live. data. size|Yes|JVM. gc. live. data. size|Byte|Medelvärde|Storlek på den gamla generationens lagringspool efter en fullständig GC|Distribution, AppName, Pod|
|JVM. gc. max. data storlek|Yes|JVM. gc. max. data storlek|Byte|Medelvärde|Max storlek på den gamla generationens minnesbuffert|Distribution, AppName, Pod|
|JVM. gc. Memory. allokerat|Yes|JVM. gc. Memory. allokerat|Byte|Maximal|Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till före nästa|Distribution, AppName, Pod|
|JVM. gc. Memory. upphöjt|Yes|JVM. gc. Memory. upphöjt|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnesbuffert innan GC till efter GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Count|Yes|JVM. gc. Pause. total. Count|Antal|Totalt|Antal pauser för GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Time|Yes|JVM. gc. Pause. total. Time|Millisekunder|Totalt|Total tid för GC-paus|Distribution, AppName, Pod|
|JVM. Memory. dedikerat|Yes|JVM. Memory. dedikerat|Byte|Medelvärde|Minne som tilldelats JVM i byte|Distribution, AppName, Pod|
|JVM. Memory. Max|Yes|JVM. Memory. Max|Byte|Maximal|Maximal mängd minne i byte som kan användas för minnes hantering|Distribution, AppName, Pod|
|JVM. Memory. används|Yes|JVM. Memory. används|Byte|Medelvärde|App-minne som används i byte|Distribution, AppName, Pod|
|process. CPU. Usage|Yes|process. CPU. Usage|Procent|Medelvärde|JVM CPU-användning i procent|Distribution, AppName, Pod|
|system. CPU. Usage|Yes|system. CPU. Usage|Procent|Medelvärde|Senaste CPU-användning för hela systemet|Distribution, AppName, Pod|
|tomcat. global. error|Yes|tomcat. global. error|Antal|Totalt|Tomcat globalt fel|Distribution, AppName, Pod|
|tomcat. global. mottagen|Yes|tomcat. global. mottagen|Byte|Totalt|Totalt antal mottagna byte i Tomcat|Distribution, AppName, Pod|
|tomcat. global. Request. Gmsn. Time|Yes|tomcat. global. Request. Gmsn. Time|Millisekunder|Medelvärde|Genomsnittlig tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. Max|Yes|tomcat. global. Request. Max|Millisekunder|Maximal|Max tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. total. Count|Yes|tomcat. global. Request. total. Count|Antal|Totalt|Totalt antal Tomcat-begäranden|Distribution, AppName, Pod|
|tomcat. global. Request. total. Time|Yes|tomcat. global. Request. total. Time|Millisekunder|Totalt|Total tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. skickat|Yes|tomcat. global. skickat|Byte|Totalt|Totalt antal skickade byte i Tomcat|Distribution, AppName, Pod|
|tomcat. sessions. Active. Current|Yes|tomcat. sessions. Active. Current|Antal|Totalt|Aktivt antal Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Active. Max|Yes|tomcat. sessions. Active. Max|Antal|Totalt|Antal aktiva Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Alive. Max|Yes|tomcat. sessions. Alive. Max|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|Distribution, AppName, Pod|
|tomcat. sessions. created|Yes|tomcat. sessions. created|Antal|Totalt|Antal skapade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. har gått ut|Yes|tomcat. sessions. har gått ut|Antal|Totalt|Antal utgångna Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. avvisad|Yes|tomcat. sessions. avvisad|Antal|Totalt|Antal nekade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.threads.config. Max|Yes|tomcat.threads.config. Max|Antal|Totalt|Antal trådar för Tomcat-konfiguration|Distribution, AppName, Pod|
|tomcat. threads. Current|Yes|tomcat. threads. Current|Antal|Totalt|Antal Tomcat aktuella trådar|Distribution, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentMachineRuns|Yes|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CoreCount|No|Dedikerat antal kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|CreatingNodeCount|No|Antal noder skapas|Antal|Totalt|Antal noder som skapas|Inga dimensioner|
|IdleNodeCount|No|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|JobDeleteCompleteEvent|Yes|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Yes|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Yes|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Yes|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|jobId|
|JobStartEvent|Yes|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Yes|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|jobId|
|JobTerminateStartEvent|Yes|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|
|LeavingPoolNodeCount|No|Antal noder för att lämna pooler|Antal|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|LowPriorityCoreCount|No|Antal LowPriority kärnor|Antal|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga dimensioner|
|OfflineNodeCount|No|Antal offline-noder|Antal|Totalt|Antal offline-noder|Inga dimensioner|
|PoolCreateEvent|Yes|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolDeleteCompleteEvent|Yes|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|poolId|
|PoolDeleteStartEvent|Yes|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|poolId|
|PoolResizeCompleteEvent|Yes|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|poolId|
|PoolResizeStartEvent|Yes|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|poolId|
|PreemptedNodeCount|No|Antal misslyckade noder|Antal|Totalt|Antal misslyckade noder|Inga dimensioner|
|RebootingNodeCount|No|Antalet noder startas om|Antal|Totalt|Antal omstarter av noder|Inga dimensioner|
|ReimagingNodeCount|No|Antal noder för avbildning|Antal|Totalt|Antal avbildnings noder|Inga dimensioner|
|RunningNodeCount|No|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inga dimensioner|
|StartingNodeCount|No|Antalet noder startas|Antal|Totalt|Antal noder som börjar|Inga dimensioner|
|StartTaskFailedNodeCount|No|Starta aktivitet antal misslyckade noder|Antal|Totalt|Antal noder där start aktiviteten misslyckades|Inga dimensioner|
|TaskCompleteEvent|Yes|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|poolId, jobId|
|TaskFailEvent|Yes|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|poolId, jobId|
|TaskStartEvent|Yes|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TotalLowPriorityNodeCount|No|Antal noder med låg prioritet|Antal|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga dimensioner|
|TotalNodeCount|No|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|UnusableNodeCount|No|Antal noder som inte kan användas|Antal|Totalt|Antal oanvändbara noder|Inga dimensioner|
|WaitingForStartTaskNodeCount|No|Väntar på att starta aktiviteter antal noder|Antal|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga dimensioner|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Yes|Aktiva noder|Antal|Medelvärde|Antal noder som körs|Scenario, kluster namn|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Medelvärde|Antal inaktiva noder|Scenario, kluster namn|
|Jobbet har slutförts|Yes|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Jobb skickat|Yes|Jobb skickat|Antal|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Lämnar kärnor|Yes|Lämnar kärnor|Antal|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Yes|Lämnar noder|Antal|Medelvärde|Antal lämnar noder|Scenario, kluster namn|
|Blockerade kärnor|Yes|Blockerade kärnor|Antal|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Yes|Misslyckade noder|Antal|Medelvärde|Antal misslyckade noder|Scenario, kluster namn|
|Kvot användning i procent|Yes|Kvot användning i procent|Antal|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Medelvärde|Antal totala noder|Scenario, kluster namn|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Medelvärde|Antal oanvändbara noder|Scenario, kluster namn|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|Antal bearbetade sändningar|Antal|Medelvärde|Antalet bearbetade transaktioner|Nod, kanal, typ, status|
|ConnectionAccepted|Yes|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Nod|
|ConnectionActive|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Nod|
|ConnectionHandled|Yes|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Nod|
|ConsensusEtcdraftCommittedBlockNumber|Yes|Konsensus Etcdraft dedikerat block Number|Antal|Medelvärde|Block numret för det senaste blocket som har allokerats|Nod, kanal|
|CpuUsagePercentageInDouble|Yes|Procent andel CPU-användning|Procent|Maximal|Procent andel CPU-användning|Nod|
|EndorserEndorsementFailures|Yes|Bekräftelse av påskrifts försök|Antal|Medelvärde|Antalet misslyckade attesteringar.|Node, Channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Yes|Gossip ledare för val av ledare|Antal|Totalt|Peer är ledare (1) eller följare (0)|Nod, kanal|
|GossipMembershipTotalPeersKnown|Yes|Gossip-medlemskap, totalt antal peer-datorer kända|Antal|Medelvärde|Totalt antal kända peer-datorer|Nod, kanal|
|GossipStateHeight|Yes|Gossip tillstånds höjd|Antal|Medelvärde|Aktuell höjd för redovisning|Nod, kanal|
|IOReadBytes|Yes|Lästa byte i IO|Byte|Totalt|Lästa byte i IO|Nod|
|IOWriteBytes|Yes|Skrivna byte i IO|Byte|Totalt|Skrivna byte i IO|Nod|
|LedgerTransactionCount|Yes|Antal redovisnings transaktioner|Antal|Medelvärde|Antal bearbetade transaktioner|Node, Channel, transaction_type, chaincode, validation_code|
|MemoryLimit|Yes|Minnes gräns|Byte|Medelvärde|Minnes gräns|Nod|
|MemoryUsage|Yes|Minnesanvändning|Byte|Medelvärde|Minnesanvändning|Nod|
|MemoryUsagePercentageInDouble|Yes|Minnes användnings procent|Procent|Medelvärde|Minnes användnings procent|Nod|
|PendingTransactions|Yes|Väntande transaktioner|Antal|Medelvärde|Väntande transaktioner|Nod|
|ProcessedBlocks|Yes|Bearbetade block|Antal|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Yes|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Nod|
|QueuedTransactions|Yes|Köade transaktioner|Antal|Medelvärde|Köade transaktioner|Nod|
|RequestHandled|Yes|Hanterade begär Anden|Antal|Totalt|Hanterade begär Anden|Nod|
|StorageUsage|Yes|Lagrings användning|Byte|Medelvärde|Lagrings användning|Nod|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|cachehits|Yes|Cacheträffar|Antal|Totalt||ShardId|
|cachehits0|Yes|Cacheträffar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachehits1|Yes|Cacheträffar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits2|Yes|Cacheträffar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachehits3|Yes|Cacheträffar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachehits4|Yes|Cacheträffar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachehits5|Yes|Cacheträffar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachehits6|Yes|Cacheträffar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachehits7|Yes|Cacheträffar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits8|Yes|Cacheträffar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachehits9|Yes|Cacheträffar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cacheLatency|Yes|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Medelvärde||ShardId|
|cachemisses|Yes|Cachemissar|Antal|Totalt||ShardId|
|cachemisses0|Yes|Cachemissar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Yes|Cachemissar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Yes|Cachemissar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Yes|Cachemissar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Yes|Cachemissar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Yes|Cachemissar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Yes|Cachemissar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Yes|Cachemissar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Yes|Cachemissar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Yes|Cachemissar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachemissrate|Yes|Missar i cache|Procent|cachemissrate||ShardId|
|cacheRead|Yes|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|cacheRead0|Yes|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Yes|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Yes|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Yes|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Yes|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Yes|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Yes|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Yes|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Yes|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Yes|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite|Yes|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheWrite0|Yes|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite1|Yes|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite2|Yes|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite3|Yes|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite4|Yes|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite5|Yes|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite6|Yes|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite7|Yes|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite8|Yes|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite9|Yes|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|connectedclients|Yes|Anslutna klienter|Antal|Maximal||ShardId|
|connectedclients0|Yes|Anslutna klienter (Shard 0)|Antal|Maximal||Inga dimensioner|
|connectedclients1|Yes|Anslutna klienter (Shard 1)|Antal|Maximal||Inga dimensioner|
|connectedclients2|Yes|Anslutna klienter (Shard 2)|Antal|Maximal||Inga dimensioner|
|connectedclients3|Yes|Anslutna klienter (Shard 3)|Antal|Maximal||Inga dimensioner|
|connectedclients4|Yes|Anslutna klienter (Shard 4)|Antal|Maximal||Inga dimensioner|
|connectedclients5|Yes|Anslutna klienter (Shard 5)|Antal|Maximal||Inga dimensioner|
|connectedclients6|Yes|Anslutna klienter (Shard 6)|Antal|Maximal||Inga dimensioner|
|connectedclients7|Yes|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|connectedclients8|Yes|Anslutna klienter (Shard 8)|Antal|Maximal||Inga dimensioner|
|connectedclients9|Yes|Anslutna klienter (Shard 9)|Antal|Maximal||Inga dimensioner|
|fel|Yes|Fel|Antal|Maximal||ShardId, ErrorType|
|evictedkeys|Yes|Avlägsnade nycklar|Antal|Totalt||ShardId|
|evictedkeys0|Yes|Avlägsnade nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Yes|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Yes|Avlägsnade nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Yes|Avlägsnade nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Yes|Avlägsnade nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Yes|Avlägsnade nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Yes|Avlägsnade nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Yes|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Yes|Avlägsnade nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Yes|Avlägsnade nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|expiredkeys|Yes|Utgångna nycklar|Antal|Totalt||ShardId|
|expiredkeys0|Yes|Utgångna nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|expiredkeys1|Yes|Utgångna nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|expiredkeys2|Yes|Utgångna nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|expiredkeys3|Yes|Utgångna nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|expiredkeys4|Yes|Utgångna nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|expiredkeys5|Yes|Utgångna nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|expiredkeys6|Yes|Utgångna nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|expiredkeys7|Yes|Utgångna nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|expiredkeys8|Yes|Utgångna nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|expiredkeys9|Yes|Utgångna nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|getcommands|Yes|Hämtningar|Antal|Totalt||ShardId|
|getcommands0|Yes|Hämtar (Shard 0)|Antal|Totalt||Inga dimensioner|
|getcommands1|Yes|Hämtar (Shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands2|Yes|Hämtar (Shard 2)|Antal|Totalt||Inga dimensioner|
|getcommands3|Yes|Hämtar (Shard 3)|Antal|Totalt||Inga dimensioner|
|getcommands4|Yes|Hämtar (Shard 4)|Antal|Totalt||Inga dimensioner|
|getcommands5|Yes|Hämtar (Shard 5)|Antal|Totalt||Inga dimensioner|
|getcommands6|Yes|Hämtar (Shard 6)|Antal|Totalt||Inga dimensioner|
|getcommands7|Yes|Hämtar (Shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands8|Yes|Hämtar (Shard 8)|Antal|Totalt||Inga dimensioner|
|getcommands9|Yes|Hämtar (Shard 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Yes|Åtgärder per sekund|Antal|Maximal||ShardId|
|operationsPerSecond0|Yes|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond1|Yes|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond2|Yes|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond3|Yes|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond4|Yes|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond5|Yes|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond6|Yes|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond7|Yes|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond8|Yes|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond9|Yes|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inga dimensioner|
|percentProcessorTime|Yes|Processor|Procent|Maximal||ShardId|
|percentProcessorTime0|Yes|PROCESSOR (Shard 0)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime1|Yes|PROCESSOR (Shard 1)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime2|Yes|PROCESSOR (Shard 2)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime3|Yes|PROCESSOR (Shard 3)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime4|Yes|PROCESSOR (Shard 4)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime5|Yes|PROCESSOR (Shard 5)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime6|Yes|PROCESSOR (Shard 6)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime7|Yes|PROCESSOR (Shard 7)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime8|Yes|PROCESSOR (Shard 8)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime9|Yes|PROCESSOR (Shard 9)|Procent|Maximal||Inga dimensioner|
|serverLoad|Yes|Serverbelastning|Procent|Maximal||ShardId|
|serverLoad0|Yes|Server belastning (Shard 0)|Procent|Maximal||Inga dimensioner|
|serverLoad1|Yes|Server belastning (Shard 1)|Procent|Maximal||Inga dimensioner|
|serverLoad2|Yes|Server belastning (Shard 2)|Procent|Maximal||Inga dimensioner|
|serverLoad3|Yes|Server belastning (Shard 3)|Procent|Maximal||Inga dimensioner|
|serverLoad4|Yes|Server belastning (Shard 4)|Procent|Maximal||Inga dimensioner|
|serverLoad5|Yes|Server belastning (Shard 5)|Procent|Maximal||Inga dimensioner|
|serverLoad6|Yes|Server belastning (Shard 6)|Procent|Maximal||Inga dimensioner|
|serverLoad7|Yes|Server belastning (Shard 7)|Procent|Maximal||Inga dimensioner|
|serverLoad8|Yes|Server belastning (Shard 8)|Procent|Maximal||Inga dimensioner|
|serverLoad9|Yes|Server belastning (Shard 9)|Procent|Maximal||Inga dimensioner|
|setcommands|Yes|Inställningar|Antal|Totalt||ShardId|
|setcommands0|Yes|Uppsättningar (Shard 0)|Antal|Totalt||Inga dimensioner|
|setcommands1|Yes|Uppsättningar (Shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands2|Yes|Uppsättningar (Shard 2)|Antal|Totalt||Inga dimensioner|
|setcommands3|Yes|Uppsättningar (Shard 3)|Antal|Totalt||Inga dimensioner|
|setcommands4|Yes|Uppsättningar (Shard 4)|Antal|Totalt||Inga dimensioner|
|setcommands5|Yes|Uppsättningar (Shard 5)|Antal|Totalt||Inga dimensioner|
|setcommands6|Yes|Uppsättningar (Shard 6)|Antal|Totalt||Inga dimensioner|
|setcommands7|Yes|Uppsättningar (Shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands8|Yes|Uppsättningar (Shard 8)|Antal|Totalt||Inga dimensioner|
|setcommands9|Yes|Uppsättningar (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed|Yes|Totalt antal åtgärder|Antal|Totalt||ShardId|
|totalcommandsprocessed0|Yes|Totalt antal åtgärder (Shard 0)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed1|Yes|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed2|Yes|Totalt antal åtgärder (Shard 2)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed3|Yes|Totalt antal åtgärder (Shard 3)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed4|Yes|Totalt antal åtgärder (Shard 4)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed5|Yes|Totalt antal åtgärder (Shard 5)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed6|Yes|Totalt antal åtgärder (Shard 6)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed7|Yes|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed8|Yes|Totalt antal åtgärder (Shard 8)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed9|Yes|Totalt antal åtgärder (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalkeys|Yes|Totalt antal nycklar|Antal|Maximal||ShardId|
|totalkeys0|Yes|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inga dimensioner|
|totalkeys1|Yes|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inga dimensioner|
|totalkeys2|Yes|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inga dimensioner|
|totalkeys3|Yes|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inga dimensioner|
|totalkeys4|Yes|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inga dimensioner|
|totalkeys5|Yes|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inga dimensioner|
|totalkeys6|Yes|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inga dimensioner|
|totalkeys7|Yes|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inga dimensioner|
|totalkeys8|Yes|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inga dimensioner|
|totalkeys9|Yes|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inga dimensioner|
|usedmemory|Yes|Använt minne|Byte|Maximal||ShardId|
|usedmemory0|Yes|Använt minne (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemory1|Yes|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemory2|Yes|Använt minne (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemory3|Yes|Använt minne (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemory4|Yes|Använt minne (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemory5|Yes|Använt minne (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemory6|Yes|Använt minne (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemory7|Yes|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemory8|Yes|Använt minne (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemory9|Yes|Använt minne (Shard 9)|Byte|Maximal||Inga dimensioner|
|usedmemorypercentage|Yes|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|Yes|RSS för använt minne|Byte|Maximal||ShardId|
|usedmemoryRss0|Yes|Använt minne RSS (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Yes|RSS för använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Yes|Använt minne RSS (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Yes|Använt minne RSS (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Yes|Använt minne RSS (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Yes|Använt minne RSS (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Yes|Använt minne RSS (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Yes|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Yes|Använt minne RSS (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Yes|Använt minne RSS (Shard 9)|Byte|Maximal||Inga dimensioner|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|No|Disk läsning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS.|RoleInstanceId|
|Disk-skrivna byte/s|No|Disk skrivning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk.|RoleInstanceId|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|No|Disk läsning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS.|Inga dimensioner|
|Disk-skrivna byte/s|No|Disk skrivning|BytesPerSecond|Medelvärde|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|No|Använd kapacitet|Byte|Medelvärde|Kapacitet som används av konto|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|No|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|No|BLOB-antal|Antal|Medelvärde|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Yes|Antal BLOB-behållare|Antal|Medelvärde|Antalet behållare i lagrings kontots Blob Service.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|No|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|No|Fil kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|No|Antal filer|Antal|Medelvärde|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|No|Antal fil resurser|Antal|Medelvärde|Antalet fil resurser i lagrings kontots fil tjänst.|Inga dimensioner|
|FileShareQuota|No|Fil resursens kvot storlek|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicks bilder av fil resurs|Antal|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|No|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Yes|Kös Kap kap.|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Medelvärde|Antalet köer i lagrings kontots Kötjänst.|Inga dimensioner|
|QueueMessageCount|Yes|Antal meddelanden i kö|Antal|Medelvärde|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Yes|Tabell kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Medelvärde|Antalet tabeller i lagrings kontots Table service.|Inga dimensioner|
|TableEntityCount|Yes|Antal tabell enheter|Antal|Medelvärde|Antalet tabell enheter i lagrings kontots Table service.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|CharactersTrained|Yes|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|CharactersTranslated|Yes|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|ClientErrors|Yes|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Yes|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Yes|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Yes|Svarstid|Millisekunder|Medelvärde|Svars tid i millisekunder.|ApiName, OperationName, region|
|ProcessedImages|Yes|Bearbetade bilder|Antal|Totalt| Antal transaktioner för bild bearbetning.|ApiName, FeatureName, UsageChannel, region|
|ServerErrors|Yes|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|SpeechSessionDuration|Yes|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|SuccessfulCalls|Yes|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalCalls|Yes|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|TotalErrors|Yes|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|TotalTokenCalls|Yes|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|
|TotalTransactions|Yes|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Ködjup för datadisk|Yes|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|Yes|OS-KÖDJUP (inaktuellt)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Yes|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Per disk KÖDJUP|Yes|KÖDJUP för data disk (inaktuellt)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Yes|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Yes|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Yes|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Yes|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inga dimensioner|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Ködjup för datadisk|Yes|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|VMName|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|VMName|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|KÖDJUP för operativ system per disk|Yes|OS-KÖDJUP (inaktuellt)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Yes|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Per disk KÖDJUP|Yes|KÖDJUP för data disk (inaktuellt)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Yes|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Yes|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Yes|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Yes|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|VMName|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. Compute/virtualMachineScaleSets/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Medelvärde|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Medelvärde|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Ködjup för datadisk|Yes|Data disk Queue djup (för hands version)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Medelvärde|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Medelvärde|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk (för hands version)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|Yes|OS-KÖDJUP (inaktuellt)|Antal|Medelvärde|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Yes|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Medelvärde|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Medelvärde|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Per disk KÖDJUP|Yes|KÖDJUP för data disk (inaktuellt)|Antal|Medelvärde|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Yes|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Yes|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Yes|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Yes|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Medelvärde|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache (för hands version)|Procent|Medelvärde|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data disk cache (för hands version)|Procent|Medelvärde|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen (för hands version)|Procent|Medelvärde|Read missar i Premium OS-diskcachen|Inga dimensioner|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Processoranvändning|Antal|Medelvärde|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Yes|Minnesanvändning|Byte|Medelvärde|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Mottagna nätverks byte per sekund|Byte|Medelvärde|Mottagna nätverks byte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Yes|Överförda nätverks byte per sekund|Byte|Medelvärde|Överförda nätverks byte per sekund.|Inga dimensioner|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|Agentpoolegenskap CPU-tid|Sekunder|Totalt|Agentpoolegenskap CPU-tid i sekunder|Inga dimensioner|
|RunDuration|Yes|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga dimensioner|
|SuccessfulPullCount|Yes|Antal lyckade pull-överföringar|Antal|Medelvärde|Antal lyckade bild hämtningar|Inga dimensioner|
|SuccessfulPushCount|Yes|Antal lyckade push-meddelanden|Antal|Medelvärde|Antal lyckade avbildnings push-meddelanden|Inga dimensioner|
|TotalPullCount|Yes|Totalt antal hämtningar|Antal|Medelvärde|Totalt antal bild hämtningar|Inga dimensioner|
|TotalPushCount|Yes|Totalt antal push-meddelanden|Antal|Medelvärde|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|No|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Medelvärde|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|No|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Medelvärde|Total mängd tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_node_status_condition|No|Status för olika nod villkor|Antal|Medelvärde|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|No|Antal poddar per fas|Antal|Medelvärde|Antal poddar per fas|fas, namnrymd, Pod|
|kube_pod_status_ready|No|Antal poddar i klart läge|Antal|Medelvärde|Antal poddar i klart läge|namnrymd, Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Slutförda förfrågningar|Antal|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Yes|Tillgänglig kapacitet|Byte|Medelvärde|Tillgänglig kapacitet i byte under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloud|Yes|Överförda moln byte (enhet)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloudPerShare|Yes|Överförda moln byte (resurs)|Byte|Medelvärde|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|CloudReadThroughput|Yes|Data flöde för moln hämtning|BytesPerSecond|Medelvärde|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudReadThroughputPerShare|Yes|Data flöde för moln hämtning (resurs)|BytesPerSecond|Medelvärde|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughput|Yes|Moln överförings data flöde|BytesPerSecond|Medelvärde|Moln överförings data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudUploadThroughputPerShare|Yes|Moln överförings data flöde (resurs)|BytesPerSecond|Medelvärde|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|HyperVMemoryUtilization|Yes|Edge Compute-minnes användning|Procent|Medelvärde|Mängden RAM-minne som används|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge Compute-procent CPU|Procent|Medelvärde|CPU-användning i procent|InstanceName|
|NICReadThroughput|Yes|Läs data flöde (nätverk)|BytesPerSecond|Medelvärde|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Yes|Skriv data flöde (nätverk)|BytesPerSecond|Medelvärde|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|Enhet|Yes|Total kapacitet|Byte|Medelvärde|Total kapacitet|Inga dimensioner|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Yes|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Avbrutna aktiviteter kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Yes|Misslyckad aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Yes|Genomförd aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Total fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|IntegrationRuntimeAvailableMemory|Yes|Tillgängligt minne för integration runtime|Byte|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableNodeNumber|Yes|Antal tillgängliga noder för integration runtime|Antal|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Varaktighet för integration runtime-kö|Sekunder|Medelvärde||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|PROCESSOR användning för integration runtime|Procent|Medelvärde||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeQueueLength|Yes|Kölängd för integration runtime|Antal|Medelvärde||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|MaxAllowedResourceCount|Yes|Maximalt antal tillåtna entiteter|Antal|Maximal||Inga dimensioner|
|PipelineCancelledRuns|Yes|Avbruten pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineFailedRuns|Yes|Misslyckad pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineSucceededRuns|Yes|Slutförd pipeline kör mått|Antal|Totalt||FailureType, namn|
|ResourceCount|Yes|Totalt antal entiteter|Antal|Maximal||Inga dimensioner|
|TriggerCancelledRuns|Yes|Avbrutna utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerFailedRuns|Yes|Misslyckad utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Yes|Lyckade utlösare kör mått|Antal|Totalt||Namn, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DataRead|Yes|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|DataWritten|Yes|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|ReadRequests|Yes|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inga dimensioner|
|TotalStorage|Yes|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inga dimensioner|
|WriteRequests|Yes|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inga dimensioner|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Medelvärde|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Medelvärde|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Medelvärde|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Medelvärde|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Medelvärde|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Yes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Yes|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Medelvärde|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Medelvärde|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|IOPS|Yes|IOPS|Antal|Medelvärde|I/o-åtgärder per sekund|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Medelvärde|Aktiva anslutningar|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connections_succeeded|Yes|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|IOPS|Yes|IOPS|Antal|Medelvärde|I/o-åtgärder per sekund|Inga dimensioner|
|maximum_used_transactionIDs|Yes|Högsta antal använda transaktions-ID: n|Antal|Medelvärde|Högsta antal använda transaktions-ID: n|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Medelvärde|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Medelvärde|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. commands. utgående. Abandon. lyckades|Yes|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Yes|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inga dimensioner|
|C2D. commands. rekasta. Success|Yes|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|C2D. Methods. Failure|Yes|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Yes|Begär ande storlek för direkta metod anrop|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Yes|Svars storlek för direkta metod anrop|Byte|Medelvärde|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D. Methods. Success|Yes|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. delad.|Yes|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D., delad. storlek|Yes|Svars storlek för dubbla läsningar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D. delad. lyckades|Yes|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|C2D. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från Server delen|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|C2D. delad. Update. lyckades|Yes|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2DMessagesExpired|Yes|C2D meddelanden har förfallit (förhands granskning)|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inga dimensioner|
|konfigurationer|Yes|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|
|connectedDeviceCount|No|Anslutna enheter (förhands granskning)|Antal|Medelvärde|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C. endpoints. utgående. Builtity. events|Yes|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. utgående. eventHubs|Yes|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusQueues|Yes|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusTopics|Yes|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Yes|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. blob|Yes|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Yes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. Builtin. events|Yes|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. latens. eventHubs|Yes|Routning: meddelande fördröjning för Event Hub|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusQueues|Yes|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusTopics|Yes|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C. endpoints. svars tid. Storage|Yes|Routning: meddelande fördröjning för lagring|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. telemetri. utgående.|Yes|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C. telemetri. utgående. fallback|Yes|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C. telemetri. utgående. ogiltig|Yes|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C. telemetri. utgående. överblivna|Yes|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C. telemetri. utgående. lyckades|Yes|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C. telemetri. ingress. allProtocol|Yes|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. ingress. sendThrottle|Yes|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|D2C. telemetri. ingress. lyckades|Yes|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|D2C. delad.|Yes|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C., delad. storlek|Yes|Svars storlek för dubbla läsningar från enheter|Byte|Medelvärde|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C. delad. lyckades|Yes|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från enheter|Byte|Medelvärde|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. lyckades|Yes|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|dailyMessageQuotaUsed|Yes|Totalt antal meddelanden som används|Antal|Maximal|Antal sammanlagt antal meddelanden som använts idag|Inga dimensioner|
|deviceDataUsage|Yes|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Yes|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|Devices. connectedDevices. allProtocol|Yes|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|enheter. totalDevices|Yes|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Yes|Event Grid leveranser (för hands version)|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|Resultat, EventType|
|EventGridLatency|Yes|Event Grid svars tid (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ|
|Jobs. cancelJob. Failure|Yes|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Yes|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb. slutfört|Yes|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. Failure|Yes|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|Jobs. createDirectMethodJob. lyckades|Yes|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. Failure|Yes|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. lyckades|Yes|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb. misslyckades|Yes|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Yes|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. lyckades|Yes|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Yes|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Yes|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Yes|Meddelande storlek för routning i byte (för hands version)|Byte|Totalt|Den totala storleken i byte på meddelanden levererade av IoT Hub till en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slut punkter. Mått värdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Dirigera leveranser (för hands version)|Antal|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Leverans svars tid för routning (för hands version)|Millisekunder|Medelvärde|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Totalt antal enheter (förhands granskning)|Antal|Medelvärde|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|twinQueries. Failure|Yes|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Yes|Resultat storlek för dubbla frågor|Byte|Medelvärde|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. lyckades|Yes|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|
|DeviceAssignments|Yes|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Region tillagt|Antal|Antal|Region tillagt|Region|
|AutoscaleMaxThroughput|No|Skala maximalt data flöde|Antal|Maximal|Skala maximalt data flöde|DatabaseName, samlings namn|
|AvailableStorage|No|föråldrad Tillgängligt lagrings utrymme|Byte|Totalt|"Tillgängligt lagrings utrymme" tas bort från Azure Monitor i slutet av september 2020. Lagrings storleken för Cosmos DB samlingen är nu obegränsad. Den enda begränsningen är att lagrings storleken för varje logisk partitionsnyckel är 20. Du kan aktivera PartitionKeyStatistics i diagnostisk logg för att känna till lagrings förbrukningen för Top partition-nycklar. Om du vill ha mer information om Cosmos DB lagrings kvot kontrollerar du det här dokumentet https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Efter utfasningen inaktive ras de återstående aviserings reglerna som fortfarande definierats för det inaktuella måttet automatiskt efter utgångs datumet.|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|No|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Genomsnittlig ReplicationLatency för Cassandra Connector|Millisekunder|Medelvärde|Genomsnittlig ReplicationLatency för Cassandra Connector|Inga dimensioner|
|CassandraConnectorReplicationHealthStatus|No|Hälso status för Cassandra Connector-replikering|Antal|Antal|Hälso status för Cassandra Connector-replikering|NotStarted, ReplicationInProgress, fel|
|CassandraKeyspaceCreate|No|Cassandra-tecken avstånd har skapats|Antal|Antal|Cassandra-tecken avstånd har skapats|ResourceName |
|CassandraKeyspaceDelete|No|Cassandra-tecken utrymme borttaget|Antal|Antal|Cassandra-tecken utrymme borttaget|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Cassandra-dataflöde har uppdaterats|Antal|Antal|Cassandra-dataflöde har uppdaterats|ResourceName |
|CassandraKeyspaceUpdate|No|Cassandra-disk utrymme uppdaterat|Antal|Antal|Cassandra-disk utrymme uppdaterat|ResourceName |
|CassandraRequestCharges|No|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|DatabaseName, samlings namn, region, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|DatabaseName, samlings namn, region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra-tabellen har skapats|Antal|Antal|Cassandra-tabellen har skapats|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tabellen har tagits bort|Antal|Antal|Cassandra-tabellen har tagits bort|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Cassandra tabell data flöde har uppdaterats|Antal|Antal|Cassandra tabell data flöde har uppdaterats|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tabellen har uppdaterats|Antal|Antal|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Kontot har skapats|Antal|Antal|Kontot har skapats|Inga dimensioner|
|DataUsage|No|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Yes|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inga dimensioner|
|DocumentCount|No|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|No|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|GremlinDatabaseCreate|No|Gremlin-databas har skapats|Antal|Antal|Gremlin-databas har skapats|ResourceName |
|GremlinDatabaseDelete|No|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Gremlin Database-genomflöde har uppdaterats|Antal|Antal|Gremlin Database-genomflöde har uppdaterats|ResourceName |
|GremlinDatabaseUpdate|No|Gremlin-databasen har uppdaterats|Antal|Antal|Gremlin-databasen har uppdaterats|ResourceName |
|GremlinGraphCreate|No|Gremlin graf har skapats|Antal|Antal|Gremlin graf har skapats|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph borttagen|Antal|Antal|Gremlin Graph borttagen|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin Graph-genomflöde har uppdaterats|Antal|Antal|Gremlin Graph-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin-diagrammet har uppdaterats|Antal|Antal|Gremlin-diagrammet har uppdaterats|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexanvändning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|No|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, |
|MongoCollectionCreate|No|Mongo-samling har skapats|Antal|Antal|Mongo-samling har skapats|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-samlingen har tagits bort|Antal|Antal|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Mongo Collection-genomflöde har uppdaterats|Antal|Antal|Mongo Collection-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-samlingen har uppdaterats|Antal|Antal|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName |
|MongoDatabaseThroughputUpdate|No|Mongo Database-genomflöde har uppdaterats|Antal|Antal|Mongo Database-genomflöde har uppdaterats|ResourceName |
|MongoDBDatabaseCreate|No|Mongo-databas har skapats|Antal|Antal|Mongo-databas har skapats|ResourceName |
|MongoDBDatabaseUpdate|No|Mongo-databasen har uppdaterats|Antal|Antal|Mongo-databasen har uppdaterats|ResourceName |
|MongoRequestCharge|Yes|Mongo begär ande avgift|Antal|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequests|Yes|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequestsCount|No|Mongo begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo begär Anden per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsDelete|No|Mongo ta bort begär ande frekvens|CountPerSecond|Medelvärde|Mongo ta bort begäran per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsInsert|No|Mongo infoga begär ande frekvens|CountPerSecond|Medelvärde|Antal Mongo infogningar per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsQuery|No|Mongo för förfrågningar|CountPerSecond|Medelvärde|Mongo-förfrågan per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsUpdate|No|Frekvens för mongo uppdaterings begär Anden|CountPerSecond|Medelvärde|Mongo för begär ande uppdatering per sekund|DatabaseName, samlings region, ErrorCode|
|NormalizedRUConsumption|No|Normaliserad RU-förbrukning|Procent|Maximal|Max procent per minut för RU-förbrukning|Samlings namn, DatabaseName, region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Yes|Regionen har redundansväxlats|Antal|Antal|Regionen har redundansväxlats|Inga dimensioner|
|RemoveRegion|Yes|Region borttagen|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Yes|Fördröjning för P99-replikering|Millisekunder|Medelvärde|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServerSideLatency|No|Svars tid på Server Sidan|Millisekunder|Medelvärde|Svars tid på Server Sidan|DatabaseName, samlings namn, region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Tjänst tillgänglighet|Procent|Medelvärde|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga dimensioner|
|SqlContainerCreate|No|SQL-behållare har skapats|Antal|Antal|SQL-behållare har skapats|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|SQL-behållare borttagen|Antal|Antal|SQL-behållare borttagen|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|SQL container data flöde uppdaterat|Antal|Antal|SQL container data flöde uppdaterat|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-behållare har uppdaterats|Antal|Antal|SQL-behållare har uppdaterats|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|SQL-databas har skapats|Antal|Antal|SQL-databas har skapats|ResourceName |
|SqlDatabaseDelete|No|SQL-databas borttagen|Antal|Antal|SQL-databas borttagen|ResourceName |
|SqlDatabaseThroughputUpdate|No|SQL Database-dataflöde uppdaterat|Antal|Antal|SQL Database-dataflöde uppdaterat|ResourceName |
|SqlDatabaseUpdate|No|SQL-databas uppdaterad|Antal|Antal|SQL-databas uppdaterad|ResourceName |
|TableTableCreate|No|AzureTable-tabellen har skapats|Antal|Antal|AzureTable-tabellen har skapats|ResourceName |
|TableTableDelete|No|AzureTable-tabellen har tagits bort|Antal|Antal|AzureTable-tabellen har tagits bort|ResourceName |
|TableTableThroughputUpdate|No|AzureTable tabell data flöde har uppdaterats|Antal|Antal|AzureTable tabell data flöde har uppdaterats|ResourceName |
|TableTableUpdate|No|AzureTable-tabellen har uppdaterats|Antal|Antal|AzureTable-tabellen har uppdaterats|ResourceName |
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|TotalRequestUnits|Yes|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Yes|Konto nycklar har uppdaterats|Antal|Antal|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Yes|Kontots nätverks inställningar har uppdaterats|Antal|Antal|Kontots nätverks inställningar har uppdaterats|Inga dimensioner|
|UpdateAccountReplicationSettings|Yes|Kontots replikeringsinställningar har uppdaterats|Antal|Antal|Kontots replikeringsinställningar har uppdaterats|Inga dimensioner|
|UpdateDiagnosticsSettings|No|Inställningarna för konto diagnostik har uppdaterats|Antal|Antal|Inställningarna för konto diagnostik har uppdaterats|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Medelvärde|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga dimensioner|
|AvailableMemory|No|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Roll|
|CaptureBacklog|No|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Inga dimensioner|
|CapturedBytes|No|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Inga dimensioner|
|CapturedMessages|No|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Inga dimensioner|
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Inga dimensioner|
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Inga dimensioner|
|Processor|No|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Inga dimensioner|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Inga dimensioner|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Inga dimensioner|
|OutgoingBytes|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Inga dimensioner|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Inga dimensioner|
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|Inga dimensioner|
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|Inga dimensioner|
|Storlek|No|Storlek|Byte|Medelvärde|Storlek på en EventHub i byte.|Roll|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|Inga dimensioner|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|Inga dimensioner|
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|Inga dimensioner|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Medelvärde|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga dimensioner|
|CaptureBacklog|No|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|No|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|No|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|EHABL|Yes|Arkivera efter släpning meddelanden (inaktuellt)|Antal|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inga dimensioner|
|EHAMBS|Yes|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHAMSGS|Yes|Arkivera meddelanden (inaktuellt)|Antal|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHINBYTES|Yes|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHINMBS|Yes|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inga dimensioner|
|EHINMSGS|Yes|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTBYTES|Yes|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTMBS|Yes|Utgående byte (inaktuell) (inaktuell)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTMSGS|Yes|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|FAILREQ|Yes|Misslyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|INMSGS|Yes|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inga dimensioner|
|INREQS|Yes|Inkommande begär Anden (inaktuellt)|Antal|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|MELLAN|Yes|Interna Server fel (inaktuellt)|Antal|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inga dimensioner|
|MISCERR|Yes|Andra fel (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|OutgoingBytes|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OUTMSGS|Yes|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inga dimensioner|
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|Entitetsnamnet |
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|Entitetsnamnet |
|Storlek|No|Storlek|Byte|Medelvärde|Storlek på en EventHub i byte.|Entitetsnamnet|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|SUCCREQ|Yes|Lyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SVRBSY|Yes|Serverns upptaget fel (inaktuellt)|Antal|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inga dimensioner|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|Entitetsnamnet |


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|HttpStatus|
|NumActiveWorkers|Yes|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Mätnings tröskel|Antal|Medelvärde|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Yes|Observerad kapacitet|Antal|Medelvärde|Kapaciteten som rapporteras till autoskalning när den kördes.|Inga dimensioner|
|ObservedMetricValue|Yes|Observerat mått värde|Antal|Medelvärde|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Tillgänglighet|Procent|Medelvärde|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|No|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Yes|Tillgänglighets testets varaktighet|Millisekunder|Medelvärde|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Yes|Nätverks anslutnings tid för sid inläsning|Millisekunder|Medelvärde|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Yes|Klient bearbetnings tid|Millisekunder|Medelvärde|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Yes|Tar emot svars tid|Millisekunder|Medelvärde|Tiden mellan den första och sista byten, eller till från koppling.|Inga dimensioner|
|browserTimings/sendDuration|Yes|Tid för att skicka begäran|Millisekunder|Medelvärde|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Yes|Sid inläsnings tid för webbläsare|Millisekunder|Medelvärde|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|No|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Yes|Beroende varaktighet|Millisekunder|Medelvärde|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|No|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/mål, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|undantag/webbläsare|No|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|Cloud/roleName|
|undantag/antal|Yes|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/Server|No|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|Cloud/roleName, Cloud/roleInstance|
|pageViews/antal|Yes|Sid visningar|Antal|Antal|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Yes|Inläsnings tid för sid visning|Millisekunder|Medelvärde|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Undantags frekvens|CountPerSecond|Medelvärde|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Tillgängligt minne|Byte|Medelvärde|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processor-CPU|Procent|Medelvärde|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Process-IO-hastighet|BytesPerSecond|Medelvärde|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processor tid|Procent|Medelvärde|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Privata byte för process|Byte|Medelvärde|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|Körnings tid för HTTP-begäran|Millisekunder|Medelvärde|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-begäranden i program kön|Antal|Medelvärde|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|Hastighet för HTTP-begäran|CountPerSecond|Medelvärde|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|begär Anden/antal|No|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/varaktighet|Yes|Server svars tid|Millisekunder|Medelvärde|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|No|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod >= 400 och inte lika med 401.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|No|Server begär ande frekvens|CountPerSecond|Medelvärde|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|spårning/antal|Yes|Spårningar|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Övergripande valv tillgänglighet|Procent|Medelvärde|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Övergripande valv beläggning|Procent|Medelvärde|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Övergripande service API-latens|Millisekunder|Medelvärde|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Batch-BLOB-antal|Antal|Medelvärde|Antalet data källor i en aggregerad batch för inmatning.|Databas|
|BatchDuration|Yes|Batch-varaktighet|Sekunder|Medelvärde|Varaktigheten för agg regerings fasen i inmatnings flödet.|Databas|
|BatchesProcessed|Yes|Bearbetade batchar|Antal|Medelvärde|Antal sammanställda batchar för inmatning. Orsak till slut för ande av batch: om batchen har nått tids gränsen för batchbearbetning, data storlek eller antal filer som angetts av batch-principen|Databas, SealReason|
|BatchSize|Yes|Batchstorlek|Byte|Medelvärde|Okomprimerad förväntad data storlek i en aggregerad batch för inmatning.|Databas|
|CacheUtilization|Yes|Användning av cache|Procent|Medelvärde|Användnings nivå i kluster omfånget|Inga dimensioner|
|ContinuousExportMaxLatenessMinutes|Yes|Kontinuerlig export, maximal försening|Antal|Maximal|Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret|Inga dimensioner|
|ContinuousExportNumOfRecordsExported|Yes|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|ContinuousExportName, databas|
|ContinuousExportPendingCount|Yes|Antal väntande pågående export|Antal|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inga dimensioner|
|ContinuousExportResult|Yes|Resultat av kontinuerlig export|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|Processor|Yes|Processor|Procent|Medelvärde|PROCESSOR användnings nivå|Inga dimensioner|
|EventsProcessedForEventHubs|Yes|Bearbetade händelser (för Event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|ExportUtilization|Yes|Exportanvändning|Procent|Maximal|Exportera användning|Inga dimensioner|
|IngestionLatencyInSeconds|Yes|Inmatnings svars tid (i sekunder)|Sekunder|Medelvärde|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inga dimensioner|
|IngestionResult|Yes|Inmatnings resultat|Antal|Antal|Antal inmatnings åtgärder|IngestionResultDetails|
|IngestionUtilization|Yes|Förbruknings användning|Procent|Medelvärde|Förhållandet mellan använda inmatnings platser i klustret|Inga dimensioner|
|IngestionVolumeInMB|Yes|Inmatnings volym (i MB)|Antal|Totalt|Total mängd inmatade data till klustret (i MB)|Inga dimensioner|
|InstanceCount|Yes|Antal instanser|Antal|Medelvärde|Totalt antal instanser|Inga dimensioner|
|KeepAlive|Yes|Behåll Alive|Antal|Medelvärde|Sanity check anger att klustret svarar på frågor|Inga dimensioner|
|QueryDuration|Yes|Frågans varaktighet|Millisekunder|Medelvärde|Frågornas varaktighet i sekunder|QueryStatus|
|SteamingIngestRequestRate|Yes|Begär ande frekvens för strömning|Antal|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inga dimensioner|
|StreamingIngestDataRate|Yes|Data hastighet för strömnings intag|Antal|Medelvärde|Strömnings data hastighet (MB per sekund)|Inga dimensioner|
|StreamingIngestDuration|Yes|Hämtnings tid för strömning|Millisekunder|Medelvärde|Hämtnings tid för strömning i millisekunder|Inga dimensioner|
|StreamingIngestResults|Yes|Resultat av strömnings inmatning|Antal|Medelvärde|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Yes|Totalt antal samtidiga frågor|Antal|Totalt|Totalt antal samtidiga frågor|Inga dimensioner|
|TotalNumberOfExtents|Yes|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inga dimensioner|
|TotalNumberOfThrottledCommands|Yes|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfThrottledQueries|Yes|Totalt antal begränsade frågor|Antal|Totalt|Totalt antal begränsade frågor|Inga dimensioner|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Yes|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Yes|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Yes|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Anslutnings minnes användning för Integration Service Environment|Procent|Medelvärde|Anslutnings minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Anslutnings processor användning för Integration Service Environment|Procent|Medelvärde|Anslutnings processor användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Minnes användning för arbets flöde för Integration Service Environment|Procent|Medelvärde|Arbets flödets minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Användning av arbets flödes processor för Integration Service Environment|Procent|Medelvärde|Användning av arbets flödes processor för integrerings tjänst miljö.|Inga dimensioner|
|RunFailurePercentage|Yes|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Yes|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Yes|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Yes|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Yes|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Yes|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Yes|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Yes|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TriggerFireLatency|Yes|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Yes|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Yes|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Yes|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Yes|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Yes|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Yes|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Yes|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärds svars tid |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Yes|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Yes|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svars tid för åtgärd |Sekunder|Medelvärde|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Yes|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|BillableActionExecutions|Yes|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Yes|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Yes|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Yes|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Yes|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga dimensioner|
|RunFailurePercentage|Yes|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Yes|Körnings fördröjning|Sekunder|Medelvärde|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Yes|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Yes|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Yes|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Yes|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Yes|Kör svars tid|Sekunder|Medelvärde|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Yes|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TotalBillableExecutions|Yes|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inga dimensioner|
|TriggerFireLatency|Yes|Utlös brand fördröjning |Sekunder|Medelvärde|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Yes|Utlös fördröjning |Sekunder|Medelvärde|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Yes|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Yes|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Yes|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Yes|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Yes|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Yes|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Utlös svars tid för lyckade |Sekunder|Medelvärde|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Medelvärde|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Yes|Aktiva noder|Antal|Medelvärde|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Avbryt begärda körningar|Yes|Avbryt begärda körningar|Antal|Totalt|Antal körningar där Cancel begärdes för den här arbets ytan. Antalet uppdateras när en avbrottsbegäran tas emot för en körning.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Avbrutna körningar|Yes|Avbrutna körningar|Antal|Totalt|Antalet körningar som avbrutits för den här arbets ytan. Antalet uppdateras när en körning har avbrutits.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutförda körningar|Yes|Slutförda körningar|Antal|Totalt|Antalet körningar har slutförts för den här arbets ytan. Antalet uppdateras när en körning har slutförts och utdata har samlats in.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|Yes|CpuUtilization|Antal|Medelvärde|Procent andel minnes användning på en processor nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, kluster namn|
|Fel|Yes|Fel|Antal|Totalt|Antal körnings fel i den här arbets ytan. Antalet uppdateras när körningen påträffar ett fel.|Scenario|
|Misslyckade körningar|Yes|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan. Antalet uppdateras när en körning Miss lyckas.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Slutför körningar|Yes|Slutför körningar|Antal|Totalt|Antal körningar som har angetts i slutfört tillstånd för den här arbets ytan. Antalet uppdateras när en körning har slutförts men insamlingen fortfarande pågår.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|Yes|GpuUtilization|Antal|Medelvärde|Procent andel minnes användning på en GPU-nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, DeviceId, kluster namn|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Medelvärde|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Medelvärde|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Lämnar kärnor|Yes|Lämnar kärnor|Antal|Medelvärde|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Yes|Lämnar noder|Antal|Medelvärde|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Modelldistribution misslyckades|Yes|Modelldistribution misslyckades|Antal|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Yes|Modelldistribution Startad|Antal|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Yes|Modelldistribution lyckades|Antal|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Yes|Modell registreringen misslyckades|Antal|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modell registreringen har slutförts|Yes|Modell registreringen har slutförts|Antal|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Svarar inte körningar|Yes|Svarar inte körningar|Antal|Totalt|Antal körningar som inte svarar på den här arbets ytan. Antalet uppdateras när en körning inte övergår i tillstånd.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Inte startade körningar|Yes|Inte startade körningar|Antal|Totalt|Antal körningar i inte start läge för den här arbets ytan. Antalet uppdateras när en begäran tas emot för att skapa en körning men körnings information ännu inte har fyllts i. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Blockerade kärnor|Yes|Blockerade kärnor|Antal|Medelvärde|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Yes|Misslyckade noder|Antal|Medelvärde|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Förbereder körningar|Yes|Förbereder körningar|Antal|Totalt|Antal körningar som förbereds för den här arbets ytan. Antalet uppdateras när en körning går in i förberedelse tillstånd medan körnings miljön förbereds.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Etablerings körningar|Yes|Etablerings körningar|Antal|Totalt|Antal körningar som håller på att etablering för den här arbets ytan. Antalet uppdateras när en körning väntar på skapande eller etablering av beräknings mål.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Köade körningar|Yes|Köade körningar|Antal|Totalt|Antal körningar som har placerats i kö för den här arbets ytan. Antalet uppdateras när en körning placeras i kö i beräknings mål. Kan inträffa när nödvändiga datornoder ska vara klara.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kvot användning i procent|Yes|Kvot användning i procent|Antal|Medelvärde|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Startade körningar|Yes|Startade körningar|Antal|Totalt|Antal körningar som körs för den här arbets ytan. Antalet uppdateras när körningen börjar köras på nödvändiga resurser.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Startar körningar|Yes|Startar körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan. Antalet uppdateras när begäran om att skapa körnings-och körnings information, till exempel körnings-ID, har fyllts i|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Medelvärde|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Medelvärde|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Medelvärde|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Medelvärde|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Varningar|Yes|Varningar|Antal|Totalt|Antal körnings varningar i den här arbets ytan. Antalet uppdateras när en körning påträffar en varning.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Tillgänglighet för API: erna|ApiCategory, ApiName|
|Användning|No|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Antal till gångar|Antal|Medelvärde|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|AssetQuota|Yes|Till gångs kvot|Antal|Medelvärde|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|AssetQuotaUsedPercentage|Yes|Använd procent andel till till gångs kvot|Procent|Medelvärde|Procent andel till gång som används i det aktuella medie tjänst kontot|Inga dimensioner|
|ContentKeyPolicyCount|Yes|Antal nyckel principer för innehåll|Antal|Medelvärde|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|ContentKeyPolicyQuota|Yes|Kvot för innehålls nyckel princip|Antal|Medelvärde|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Procent andel av nyckel princip för innehåll|Procent|Medelvärde|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inga dimensioner|
|StreamingPolicyCount|Yes|Antal strömmande principer|Antal|Medelvärde|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|StreamingPolicyQuota|Yes|Kvot för strömmande princip|Antal|Medelvärde|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|StreamingPolicyQuotaUsedPercentage|Yes|Använd procent andel av princip för strömning|Procent|Medelvärde|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inga dimensioner|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Medelvärde|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Allokerad storlek för pool|Byte|Medelvärde|Allokerad storlek för poolen|Inga dimensioner|
|VolumePoolAllocatedUsed|Yes|Pool allokerad till volym storlek|Byte|Medelvärde|Allokerad använt storlek på poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Yes|Förbrukad pool storlek|Byte|Medelvärde|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Yes|Total ögonblicks bild storlek för poolen|Byte|Medelvärde|Summan av ögonblicks bild storleken för alla volymer i poolen|Inga dimensioner|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Genomsnittlig Läs fördröjning|Millisekunder|Medelvärde|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Yes|Genomsnittlig Skriv fördröjning|Millisekunder|Medelvärde|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga dimensioner|
|CbsVolumeBackupActive|Yes|Aktivt tillstånd för säkerhets kopiering av volym|Antal|Medelvärde|Är säkerhets kopian inaktive rad för volymen.|Inga dimensioner|
|CbsVolumeLogicalBackupBytes|Yes|Logiska byte som har säkerhetskopierats|Byte|Medelvärde|Toatl-komprimerade/icke-krypterade byte säkerhets kopie ras för den här volymen.|Inga dimensioner|
|CbsVolumeOperationComplete|Yes|Åtgärds tillstånd|Antal|Medelvärde|Har den senaste säkerhets kopieringen/återställnings åtgärden slutförts.|Inga dimensioner|
|CbsVolumeOperationTransferredBytes|Yes|Överförda byte för åtgärd|Byte|Medelvärde|Totalt antal byte som överförts för den senaste säkerhets kopieringen/återställningen.|Inga dimensioner|
|CbsVolumeProtected|Yes|Volymens skyddade tillstånd|Antal|Medelvärde|Är volym som skyddas av Cloud backup service.|Inga dimensioner|
|ReadIops|Yes|Läs IOPS|CountPerSecond|Medelvärde|Läs-/ut-åtgärder per sekund|Inga dimensioner|
|VolumeAllocatedSize|Yes|Allokerad volym storlek|Byte|Medelvärde|En volyms allokerade storlek|Inga dimensioner|
|VolumeLogicalSize|Yes|Storlek på förbrukad volym|Byte|Medelvärde|Den logiska storleken på volymen (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Yes|Storlek på volym ögonblicks bild|Byte|Medelvärde|Storlek på alla ögonblicks bilder i volymen|Inga dimensioner|
|WriteIops|Yes|Skriv IOPS|CountPerSecond|Medelvärde|Skriv in/ut-åtgärder per sekund|Inga dimensioner|
|XregionReplicationHealthy|Yes|Är Volume Replication-status felfri|Antal|Medelvärde|Villkor för relationen, 1 eller 0.|Inga dimensioner|
|XregionReplicationLagTime|Yes|Fördröjning för Volume Replication|Sekunder|Medelvärde|Hur lång tid i sekunder som data på speglingen lags bakom källan.|Inga dimensioner|
|XregionReplicationLastTransferDuration|Yes|Varaktighet för senaste överföring av Volume Replication|Sekunder|Medelvärde|Hur lång tid i sekunder det tog för den senaste överföringen att slutföras.|Inga dimensioner|
|XregionReplicationLastTransferSize|Yes|Senaste överförings storlek för Volume Replication|Byte|Medelvärde|Det totala antalet byte som överförs som en del av den senaste överföringen.|Inga dimensioner|
|XregionReplicationRelationshipProgress|Yes|Diskens replikerings förlopp|Byte|Medelvärde|Total mängd data som överförts för den aktuella överförings åtgärden.|Inga dimensioner|
|XregionReplicationRelationshipTransferring|Yes|Överför volym replikering|Antal|Medelvärde|Anger om status för Volume Replication är ' transfer '.|Inga dimensioner|
|XregionReplicationTotalTransferBytes|Yes|Överföring av Volume Replication totalt|Byte|Medelvärde|Ackumulerade byte som överförts för relationen.|Inga dimensioner|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway total tid|Millisekunder|Medelvärde|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|AvgRequestCountPerHealthyHost|No|Begär Anden per minut per felfri värd|Antal|Medelvärde|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|BackendConnectTime|No|Server dels anslutnings tid|Millisekunder|Medelvärde|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Svars tid för första byte för Server del|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Svars tid för senaste byte för Server delen|Millisekunder|Medelvärde|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Svars status för Server del|Antal|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Webb program brand vägg blockerade begär Anden regel distribution|Antal|Totalt|Webb program brand vägg blockerade begär Anden regel distribution|RuleGroup, RuleId|
|BlockedReqCount|Yes|Antal blockerade förfrågningar för webb program brand vägg|Antal|Totalt|Antal blockerade förfrågningar för webb program brand vägg|Inga dimensioner|
|BytesReceived|Yes|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|Bytes sent|Yes|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|CapacityUnits|No|Aktuella kapacitets enheter|Antal|Medelvärde|Förbrukade kapacitets enheter|Inga dimensioner|
|ClientRtt|No|Klient-/klient|Millisekunder|Medelvärde|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ComputeUnits|No|Aktuella beräknings enheter|Antal|Medelvärde|Förbrukade beräknings enheter|Inga dimensioner|
|CpuUtilization|No|CPU-användning|Procent|Medelvärde|Aktuell processor användning för Application Gateway|Inga dimensioner|
|CurrentConnections|Yes|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|EstimatedBilledCapacityUnits|No|Uppskattade enheter för fakturerings kapacitet|Antal|Medelvärde|Uppskattade kapacitets enheter som ska debiteras|Inga dimensioner|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fasta fakturerbara kapacitetsenheter|Antal|Medelvärde|Lägsta kapacitets enhet som ska debiteras|Inga dimensioner|
|HealthyHostCount|Yes|Antal felfria värdar|Antal|Medelvärde|Antal felfria Server dels värdar|BackendSettingsPool|
|MatchedCount|Yes|Brand vägg för total regel distribution i webb program|Antal|Totalt|Brand vägg för webb program, total regel distribution för inkommande trafik|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Nya anslutningar per sekund|CountPerSecond|Medelvärde|Nya anslutningar per sekund som upprättats med Application Gateway|Inga dimensioner|
|ResponseStatus|Yes|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|Dataflöde|No|Dataflöde|BytesPerSecond|Medelvärde|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|TlsProtocol|Yes|Klientens TLS-protokoll|Antal|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|UnhealthyHostCount|Yes|Antal felaktiga värdar|Antal|Medelvärde|Antal värdar för skadade Server delar|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|DataProcessed|Yes|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av den här brand väggen|Inga dimensioner|
|FirewallHealth|Yes|Hälso tillstånd för brand vägg|Procent|Medelvärde|Anger den övergripande hälso tillståndet för den här brand väggen|Status, orsak|
|NetworkRuleHit|Yes|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|SNATPortUtilization|Yes|SNAT-port användning|Procent|Medelvärde|Procent andel utgående SNAT-portar som används för närvarande|Protokoll|
|Dataflöde|No|Dataflöde|BitsPerSecond|Medelvärde|Genomflöde som bearbetas av den här brand väggen|Inga dimensioner|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|No|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga dimensioner|
|RecordSetCount|Yes|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|ARP-tillgänglighet|Procent|Medelvärde|ARP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BgpAvailability|Yes|BGP-tillgänglighet|Procent|Medelvärde|BGP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BitsInPerSecond|No|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|PeeringType|
|BitsOutPerSecond|No|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|PeeringType|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|PeeredCircuitSKey|
|QosDropBitsInPerSecond|No|DroppedInBitsPerSecond|CountPerSecond|Medelvärde|Ingress BITS av data som ignoreras per sekund|Inga dimensioner|
|QosDropBitsOutPerSecond|No|DroppedOutBitsPerSecond|CountPerSecond|Medelvärde|Utgående bitar med data som ignoreras per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Antal|Medelvärde|Administratörs tillstånd för porten|Länk|
|LineProtocol|Yes|LineProtocol|Antal|Medelvärde|Status för linje protokoll för porten|Länk|
|PortBitsInPerSecond|Yes|BitsInPerSecond|CountPerSecond|Medelvärde|Inkommande bitar till Azure per sekund|Länk|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|CountPerSecond|Medelvärde|Utgående bitar till Azure per sekund|Länk|
|RxLightLevel|Yes|RxLightLevel|Antal|Medelvärde|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|Yes|TxLightLevel|Antal|Medelvärde|TX-ljusnivå i dBm|Länk, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Server delens hälso procent|Procent|Medelvärde|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|BackendRequestCount|Yes|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Yes|Svars tid för Server del|Millisekunder|Medelvärde|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|BillableResponseSize|Yes|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Total svars tid|Millisekunder|Medelvärde|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Allokerade SNAT-portar|Antal|Medelvärde|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Yes|Status för hälsoavsökningen|Antal|Medelvärde|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Använda SNAT-portar|Antal|Medelvärde|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Medelvärde|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga dimensioner|
|BytesSentRate|Yes|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga dimensioner|
|PacketsReceivedRate|Yes|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inga dimensioner|
|PacketsSentRate|Yes|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga dimensioner|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Medelvärde|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga dimensioner|
|ChecksFailedPercent|Yes|Misslyckade kontroller i procent (för hands version)|Procent|Medelvärde|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Yes|% Avsökningar misslyckades|Procent|Medelvärde|% av anslutnings övervaknings avsökningarna misslyckades|Inga dimensioner|
|RoundTripTimeMs|Yes|Tur och retur tid (MS) (för hands version)|Millisekunder|Medelvärde|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Antal byte|Antal|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|BytesDroppedDDoS|Yes|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inga dimensioner|
|BytesForwardedDDoS|Yes|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inga dimensioner|
|BytesInDDoS|Yes|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inga dimensioner|
|DDoSTriggerSYNPackets|Yes|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerTCPPackets|Yes|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerUDPPackets|Yes|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|IfUnderDDoSAttack|Yes|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|PacketsDroppedDDoS|Yes|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Yes|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inga dimensioner|
|PacketsInDDoS|Yes|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inga dimensioner|
|SynCount|Yes|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|
|TCPBytesDroppedDDoS|Yes|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Yes|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inga dimensioner|
|TCPBytesInDDoS|Yes|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inga dimensioner|
|TCPPacketsDroppedDDoS|Yes|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Yes|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Yes|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inga dimensioner|
|UDPBytesDroppedDDoS|Yes|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Yes|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inga dimensioner|
|UDPBytesInDDoS|Yes|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inga dimensioner|
|UDPPacketsDroppedDDoS|Yes|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Yes|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Yes|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inga dimensioner|
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Medelvärde|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|
|QpsByEndpoint|Yes|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gatewayens S2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|Yes|Gateway P2S-bandbredd|BytesPerSecond|Medelvärde|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Yes|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Yes|Tunnelbandbredd|BytesPerSecond|Medelvärde|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Fördröjning för ping till en virtuell dator|Millisekunder|Medelvärde|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Misslyckade pingar till en virtuell dator|Procent|Medelvärde|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|inkommande|Yes|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inga dimensioner|
|inkommande. alla. failedrequests|Yes|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. alla. förfrågningar|Yes|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. schemalagd|Yes|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|inkommande. schemalagd. Avbryt|Yes|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|installation. alla|Yes|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inga dimensioner|
|installation. Delete|Yes|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inga dimensioner|
|installation. get|Yes|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inga dimensioner|
|installation. patch|Yes|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inga dimensioner|
|installation. upsert|Yes|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inga dimensioner|
|notificationhub. push-meddelanden|Yes|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inga dimensioner|
|utgående. allpns. badorexpiredchannel|Yes|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inga dimensioner|
|utgående. allpns. channelerror|Yes|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inga dimensioner|
|utgående. allpns. invalidpayload|Yes|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inga dimensioner|
|utgående. allpns. pnserror|Yes|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inga dimensioner|
|utgående. allpns. lyckades|Yes|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. APN. badchannel|Yes|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inga dimensioner|
|utgående. APN. expiredchannel|Yes|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inga dimensioner|
|utgående. APN. invalidcredentials|Yes|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. APN. invalidnotificationsize|Yes|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inga dimensioner|
|utgående. APN. pnserror|Yes|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inga dimensioner|
|utgående. APN. lyckades|Yes|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. authenticationerror|Yes|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inga dimensioner|
|utgående. GCM. badchannel|Yes|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|utgående. GCM. expiredchannel|Yes|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|utgående. GCM. invalidcredentials|Yes|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. GCM. invalidnotificationformat|Yes|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|utgående. GCM. invalidnotificationsize|Yes|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|utgående. GCM. pnserror|Yes|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inga dimensioner|
|utgående. GCM. lyckades|Yes|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. begränsad|Yes|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inga dimensioner|
|utgående. GCM. wrongchannel|Yes|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inga dimensioner|
|utgående. MPNS. authenticationerror|Yes|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. badchannel|Yes|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. MPNS. channeldisconnected|Yes|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga dimensioner|
|utgående. MPNS. släppt|Yes|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inga dimensioner|
|utgående. MPNS. invalidcredentials|Yes|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. invalidnotificationformat|Yes|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inga dimensioner|
|utgående. MPNS. pnserror|Yes|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inga dimensioner|
|utgående. MPNS. lyckades|Yes|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. MPNS. begränsad|Yes|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. authenticationerror|Yes|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inga dimensioner|
|utgående. WNS. badchannel|Yes|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. WNS. channeldisconnected|Yes|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. channelthrottled|Yes|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inga dimensioner|
|utgående. WNS. släppt|Yes|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. expiredchannel|Yes|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga dimensioner|
|utgående. WNS. invalidcredentials|Yes|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|utgående. WNS. invalidnotificationformat|Yes|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga dimensioner|
|utgående. WNS. invalidnotificationsize|Yes|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga dimensioner|
|utgående. WNS. invalidtoken|Yes|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inga dimensioner|
|utgående. WNS. pnserror|Yes|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inga dimensioner|
|utgående. WNS. lyckades|Yes|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. WNS. begränsad|Yes|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. tokenproviderunreachable|Yes|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inga dimensioner|
|utgående. WNS. wrongtoken|Yes|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga dimensioner|
|registrering. alla|Yes|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inga dimensioner|
|registrering. skapa|Yes|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inga dimensioner|
|registrering. Delete|Yes|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inga dimensioner|
|registrering. Hämta|Yes|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inga dimensioner|
|registrering. Update|Yes|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inga dimensioner|
|schemalagt. väntar|Yes|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Average_% tillgängligt minne|Yes|Tillgängligt minne i procent|Antal|Medelvärde|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Yes|Tillgängligt växlings utrymme i procent|Antal|Medelvärde|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|Yes|% Allokerade byte som används|Antal|Medelvärde|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|Yes|DPC-tid i procent|Antal|Medelvärde|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ kostnads fri noder i procent|Yes|Kostnads fri noder i procent|Antal|Medelvärde|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Yes|Ledigt utrymme i procent|Antal|Medelvärde|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Yes|Inaktivitetstid i procent|Antal|Medelvärde|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|Yes|% Avbrotts tid|Antal|Medelvärde|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|Yes|% I/o-vänte tid|Antal|Medelvärde|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|Yes|% Trevligt tid|Antal|Medelvärde|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Yes|Privilegie rad tid i procent|Antal|Medelvärde|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Yes|% processortid|Antal|Medelvärde|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|Yes|% Använda noder i procent|Antal|Medelvärde|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Yes|Använt minne i procent|Antal|Medelvärde|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Yes|Använt utrymme i procent|Antal|Medelvärde|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Yes|Använt växlings utrymme i procent|Antal|Medelvärde|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Yes|Användar tid i procent|Antal|Medelvärde|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Yes|Tillgängliga megabyte|Antal|Medelvärde|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Yes|Tillgängligt minne i megabyte|Antal|Medelvärde|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Yes|Tillgängliga megabyte växlings utrymme|Antal|Medelvärde|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Yes|Medel s/disk läsning|Antal|Medelvärde|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Yes|Medel s/disk överföring|Antal|Medelvärde|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Yes|Medel s/disk skrivning|Antal|Medelvärde|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Yes|Mottagna byte/SEK|Antal|Medelvärde|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Yes|Skickade byte/SEK|Antal|Medelvärde|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Yes|Totalt antal byte/s|Antal|Medelvärde|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Yes|Aktuell diskkölängd|Antal|Medelvärde|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Yes|Disk-lästa byte/s|Antal|Medelvärde|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Yes|Disk läsningar/SEK|Antal|Medelvärde|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Yes|Disk överföringar/SEK|Antal|Medelvärde|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Yes|Disk-skrivna byte/s|Antal|Medelvärde|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Yes|Disk skrivningar/SEK|Antal|Medelvärde|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Yes|Lediga megabyte|Antal|Medelvärde|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Yes|Ledigt fysiskt minne|Antal|Medelvärde|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Yes|Ledigt utrymme i växlingsfiler|Antal|Medelvärde|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Yes|Ledigt virtuellt minne|Antal|Medelvärde|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Yes|Logisk disk byte/SEK|Antal|Medelvärde|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Yes|Sid läsningar/s|Antal|Medelvärde|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Yes|Sid skrivningar/SEK|Antal|Medelvärde|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Yes|Sidor/s|Antal|Medelvärde|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|Yes|PCT privilegie rad tid|Antal|Medelvärde|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|Yes|PCT-användar tid|Antal|Medelvärde|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Yes|Fysisk disk-byte/SEK|Antal|Medelvärde|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Processer|Antal|Medelvärde|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Yes|Kölängd för processor|Antal|Medelvärde|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Yes|Storlek lagrad i växlingsfiler|Antal|Medelvärde|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Yes|Totalt antal byte|Antal|Medelvärde|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Yes|Totalt antal mottagna byte|Antal|Medelvärde|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Yes|Totalt antal överförda byte|Antal|Medelvärde|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Yes|Totalt antal kollisioner|Antal|Medelvärde|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Yes|Totalt antal mottagna paket|Antal|Medelvärde|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Yes|Totalt antal överförda paket|Antal|Medelvärde|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Yes|Totalt antal mottagna mottagnings fel|Antal|Medelvärde|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Yes|Totalt antal TX-fel|Antal|Medelvärde|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Drift tid|Antal|Medelvärde|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Yes|Använt megabyte växlings utrymme|Antal|Medelvärde|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Yes|Använt minne i KB|Antal|Medelvärde|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Yes|Använt minne i MB|Antal|Medelvärde|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Användare|Antal|Medelvärde|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Yes|Virtuellt delat minne|Antal|Medelvärde|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Händelse|Yes|Händelse|Antal|Medelvärde|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsslag|Yes|Pulsslag|Antal|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatera|Yes|Uppdatera|Antal|Medelvärde|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Utgående trafik hastighet|BitsPerSecond|Medelvärde|Utgående trafik hastighet i bitar per sekund|ConnectionId|
|IngressTrafficRate|Yes|Inkommande trafik hastighet|BitsPerSecond|Medelvärde|Inkommande trafik hastighet i bitar per sekund|ConnectionId|
|SessionAvailabilityV4|Yes|Tillgänglighets-v4 för session|Procent|Medelvärde|Tillgänglighet för v4-sessionen|ConnectionId|
|SessionAvailabilityV6|Yes|V6 för sessions tillgänglighet|Procent|Medelvärde|Tillgängligheten för V6-sessionen|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Prefix svars tid|Millisekunder|Medelvärde|Prefixlängd för median|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Minne|Byte|Medelvärde|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Yes|Nedskräpning för minne (data uppsättningar)|Procent|Medelvärde|Genomsnittligt minne nedskräpning.|Inga dimensioner|
|qpu_high_utilization_metric|Yes|Hög användning av QPU|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|QueryDuration|Yes|Frågans varaktighet (data uppsättningar)|Millisekunder|Medelvärde|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Yes|Kölängd för jobbkö (data uppsättningar)|Antal|Medelvärde|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|No|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|Yes|BytesTransferred|Antal|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections – ClientError|No|ListenerConnections – ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections – ServerError|No|ListenerConnections – ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections – lyckad|No|ListenerConnections – lyckad|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections – TotalRequests|No|ListenerConnections – TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|No|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections – ClientError|No|SenderConnections – ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections – ServerError|No|SenderConnections – ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections – lyckad|No|SenderConnections – lyckad|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections – TotalRequests|No|SenderConnections – TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|No|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Sök svars tid|Sekunder|Medelvärde|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Yes|Sök frågor per sekund|CountPerSecond|Medelvärde|Sök efter frågor per sekund för Sök tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Yes|Begränsade Sök frågor i procent|Procent|Medelvärde|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga dimensioner|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.|Inga dimensioner|
|ActiveMessages|No|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal aktiva meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Medelvärde|Stängda anslutningar för Microsoft. Service Bus.|Entitetsnamnet|
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Medelvärde|Anslutningar som öppnats för Microsoft. Service Bus.|Entitetsnamnet|
|CPUXNS|No|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Inga dimensioner|
|DeadletteredMessages|No|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Entitetsnamnet|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|Entitetsnamnet|
|Meddelanden|No|Antal meddelanden i kö/ämne.|Antal|Medelvärde|Antal meddelanden i kö/ämne.|Entitetsnamnet|
|NamespaceCpuUsage|No|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Inga dimensioner|
|NamespaceMemoryUsage|No|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Inga dimensioner|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|ScheduledMessages|No|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Medelvärde|Antal schemalagda meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. Service Bus.|Entitetsnamnet |
|Storlek|No|Storlek|Byte|Medelvärde|Storlek på en kö/ett ämne i byte.|Entitetsnamnet|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begär Anden för ett namn område|Entitetsnamnet |
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|Entitetsnamnet |
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. Service Bus.|Entitetsnamnet |
|WSXNS|No|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Inga dimensioner|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Antal|Medelvärde|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Byte|Medelvärde|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Antal|Medelvärde|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Byte|Medelvärde|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Antal|Medelvärde|Status för Service Fabric nätprogram|ApplicationName, status|
|Container status|No|Container status|Antal|Medelvärde|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|CpuUtilization|No|CpuUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Procent|Medelvärde|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Antal|Medelvärde|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Antal|Medelvärde|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Antal|Medelvärde|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|InboundTraffic|Yes|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inga dimensioner|
|MessageCount|Yes|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|OutboundTraffic|Yes|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|SystemErrors|Yes|Systemfel|Procent|Maximal|Procent andelen system fel|Inga dimensioner|
|UserErrors|Yes|Användar fel|Procent|Maximal|Procent andel användar fel|Inga dimensioner|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Genomsnittlig CPU-procent|Procent|Medelvärde|Genomsnittlig CPU-procent|Inga dimensioner|
|io_bytes_read|Yes|Lästa IO-byte|Byte|Medelvärde|Lästa IO-byte|Inga dimensioner|
|io_bytes_written|Yes|Skrivna IO-byte|Byte|Medelvärde|Skrivna IO-byte|Inga dimensioner|
|io_requests|Yes|Antal IO-begäranden|Antal|Medelvärde|Antal IO-begäranden|Inga dimensioner|
|reserved_storage_mb|Yes|Reserverat lagrings utrymme|Antal|Medelvärde|Reserverat lagrings utrymme|Inga dimensioner|
|storage_space_used_mb|Yes|Använt lagrings utrymme|Antal|Medelvärde|Använt lagrings utrymme|Inga dimensioner|
|virtual_core_count|Yes|Antal virtuella kärnor|Antal|Medelvärde|Antal virtuella kärnor|Inga dimensioner|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktiva frågor|Antal|Totalt|Aktiva frågor för alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|allocated_data_storage|Yes|Allokerat data utrymme|Byte|Medelvärde|Allokerad data lagring. Ej tillämpligt för data lager.|Inga dimensioner|
|app_cpu_billed|Yes|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inga dimensioner|
|app_cpu_percent|Yes|CPU-procent för app|Procent|Medelvärde|CPU-procent för app. Gäller databaser utan server.|Inga dimensioner|
|app_memory_percent|Yes|Minnes procent för appar|Procent|Medelvärde|Minnes procent för appar. Gäller databaser utan server.|Inga dimensioner|
|base_blob_size_bytes|Yes|Bas för Blob Storage-storlek|Byte|Maximal|Bas för Blob Storage-storlek. Gäller för storskaliga databaser.|Inga dimensioner|
|blocked_by_firewall|Yes|Blockerad av brand väggen|Antal|Totalt|Blockerad av brand väggen|Inga dimensioner|
|cache_hit_percent|Yes|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inga dimensioner|
|cache_used_percent|Yes|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inga dimensioner|
|connection_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connection_successful|Yes|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_limit|Yes|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Använd CPU|Antal|Medelvärde|Använd CPU. Gäller för vCore-baserade databaser.|Inga dimensioner|
|hamn|Yes|Låsningar|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inga dimensioner|
|diff_backup_size_bytes|Yes|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_limit|Yes|DTU-gräns|Antal|Medelvärde|DTU-gräns. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_used|Yes|Använt DTU|Antal|Medelvärde|DTU används. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dw_backup_size_gb|Yes|Data lagrings storlek (GB)|Antal|Totalt|Data lagrings storleken består av storleken på dina data och transaktions loggen. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inga dimensioner|
|dw_geosnapshot_size_gb|Yes|Lagrings storlek för haveri beredskap (GB)|Antal|Totalt|Lagrings storleken för haveri beredskap visas som "haveri beredskaps lagring" på din faktura. Gäller endast för data lager.|Inga dimensioner|
|dw_snapshot_size_gb|Yes|Storlek på ögonblicks bild lagring (GB)|Antal|Totalt|Storlek för ögonblicks bild lagring är storleken på de stegvisa ändringar som fångas av ögonblicks bilder för att skapa användardefinierade och automatiska återställnings punkter. Måttet räknas till lagrings delen av din faktura. Gäller endast för data lager.|Inga dimensioner|
|dwu_consumption_percent|Yes|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inga dimensioner|
|dwu_limit|Yes|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inga dimensioner|
|dwu_used|Yes|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inga dimensioner|
|full_backup_size_bytes|Yes|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|local_tempdb_usage_percent|Yes|Lokal tempdb-procent|Procent|Medelvärde|Lokal tempdb-procent. Gäller endast för data lager.|Inga dimensioner|
|log_backup_size_bytes|Yes|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade och storskaliga databaser.|Inga dimensioner|
|log_write_percent|Yes|Logg IO-procent|Procent|Medelvärde|Logg IO-procent. Ej tillämpligt för data lager.|Inga dimensioner|
|memory_usage_percent|Yes|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inga dimensioner|
|queued_queries|Yes|Köade frågor|Antal|Totalt|Köade frågor över alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|sessions_percent|Yes|Sessioner i procent|Procent|Medelvärde|Sessioner i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|snapshot_backup_size_bytes|Yes|Lagrings storlek för ögonblicks bild säkerhets kopia|Byte|Maximal|Lagrings storlek för kumulativ ögonblicks bild. Gäller för storskaliga databaser.|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|storage|Yes|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inga dimensioner|
|storage_percent|Yes|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inga dimensioner|
|tempdb_data_size|Yes|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek för tempdb i KB. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_size|Yes|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_used_percent|Yes|Procent använt tempdb-logg|Procent|Maximal|TempDB procent logg används. Ej tillämpligt för data lager.|Inga dimensioner|
|wlg_active_queries|Yes|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor inom arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor som har nått tids gränsen för arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till hela systemet per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Allokering av arbets belastnings grupp per Cap-resurs procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till de angivna Cap-resurserna per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Effektiv tak resurs procent|Procent|Maximal|En hård gräns för procent andelen av resurser som tillåts för arbets belastnings gruppen, med hänsyn till den lägsta resurs procent som allokerats för andra arbets belastnings grupper. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effektiv minsta resurs procent|Procent|Maximal|Lägsta procent andel av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta Service nivån. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Köade frågor i arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Arbetare i procent|Procent|Medelvärde|Arbetare i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|xtp_storage_percent|Yes|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent. Ej tillämpligt för data lager.|Inga dimensioner|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|Inga dimensioner|
|allocated_data_storage_percent|Yes|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inga dimensioner|
|cpu_limit|Yes|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Använd CPU|Antal|Medelvärde|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|database_allocated_data_storage|No|Allokerat data utrymme|Byte|Medelvärde|Allokerat data utrymme|DatabaseResourceId|
|database_cpu_limit|No|PROCESSOR gräns|Antal|Medelvärde|PROCESSOR gräns|DatabaseResourceId|
|database_cpu_percent|No|CPU-procent|Procent|Medelvärde|CPU-procent|DatabaseResourceId|
|database_cpu_used|No|Använd CPU|Antal|Medelvärde|Använd CPU|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-procent|Procent|Medelvärde|DTU-procent|DatabaseResourceId|
|database_eDTU_used|No|eDTU använt|Antal|Medelvärde|eDTU använt|DatabaseResourceId|
|database_log_write_percent|No|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|DatabaseResourceId|
|database_physical_data_read_percent|No|Data IO-procent|Procent|Medelvärde|Data IO-procent|DatabaseResourceId|
|database_sessions_percent|No|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|DatabaseResourceId|
|database_storage_used|No|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|DatabaseResourceId|
|database_workers_percent|No|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Medelvärde|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_limit|Yes|eDTU-gräns|Antal|Medelvärde|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_used|Yes|eDTU använt|Antal|Medelvärde|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|log_write_percent|Yes|Logg IO-procent|Procent|Medelvärde|Logg IO-procent|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Medelvärde|Data IO-procent|Inga dimensioner|
|sessions_percent|Yes|Sessioner i procent|Procent|Medelvärde|Sessioner i procent|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|storage_limit|Yes|Max storlek för data|Byte|Medelvärde|Max storlek för data|Inga dimensioner|
|storage_percent|Yes|Använt data utrymme i procent|Procent|Medelvärde|Använt data utrymme i procent|Inga dimensioner|
|storage_used|Yes|Använt data utrymme|Byte|Medelvärde|Använt data utrymme|Inga dimensioner|
|tempdb_data_size|Yes|Data fil storlek i tempdb i KB|Antal|Maximal|Data fil storlek i tempdb i KB|Inga dimensioner|
|tempdb_log_size|Yes|TempDB-logg fils storlek kilobyte|Antal|Maximal|TempDB-logg fils storlek kilobyte|Inga dimensioner|
|tempdb_log_used_percent|Yes|Procent använt tempdb-logg|Procent|Maximal|Procent använt tempdb-logg|Inga dimensioner|
|workers_percent|Yes|Arbetare i procent|Procent|Medelvärde|Arbetare i procent|Inga dimensioner|
|xtp_storage_percent|Yes|Minnes intern OLTP-lagring i procent|Procent|Medelvärde|Minnes intern OLTP-lagring i procent|Inga dimensioner|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|No|Använd kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium Storage-konton och Blob Storage-konton är det samma som BlobCapacity eller FileCapacity.|Inga dimensioner|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|No|BLOB-kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|No|BLOB-antal|Antal|Medelvärde|Antalet BLOB-objekt som lagras i lagrings kontot.|BlobType,-nivå|
|BlobProvisionedSize|No|BLOB-allokerad storlek|Byte|Medelvärde|Mängden lagrings utrymme som har allokerats i lagrings kontots Blob Service i byte.|BlobType,-nivå|
|ContainerCount|Yes|Antal BLOB-behållare|Antal|Medelvärde|Antalet behållare i lagrings kontot.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|No|Index kapacitet|Byte|Medelvärde|Mängden lagrings utrymme som används av Azure Data Lake Storage Gen2 hierarkiskt index.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|No|Fil kapacitet|Byte|Medelvärde|Mängden fil lagring som används av lagrings kontot.|FileShare|
|FileCount|No|Antal filer|Antal|Medelvärde|Antalet filer i lagrings kontot.|FileShare|
|FileShareCapacityQuota|No|Kapacitets kvot för fil resurs|Byte|Medelvärde|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareCount|No|Antal fil resurser|Antal|Medelvärde|Antalet fil resurser i lagrings kontot.|Inga dimensioner|
|FileShareProvisionedIOPS|No|Allokerad IOPS för fil resurs|Byte|Medelvärde|Bas linje numret för etablerade IOPS för Premium-filresursen i Premium-filernas lagrings konto. Antalet beräknas baserat på resurs kapacitetens tilldelade storlek (kvot).|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicks bilder av fil resurs|Antal|Medelvärde|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|No|Storlek på fil resursens ögonblicks bild|Byte|Medelvärde|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Yes|Kös Kap kap.|Byte|Medelvärde|Mängden Queue Storage som används av lagrings kontot.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Medelvärde|Antalet köer i lagrings kontot.|Inga dimensioner|
|QueueMessageCount|Yes|Antal meddelanden i kö|Antal|Medelvärde|Antalet meddelanden som inte har förfallit i lagrings kontot.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Medelvärde|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Medelvärde|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Medelvärde|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Yes|Tabell kapacitet|Byte|Medelvärde|Mängden tabell lagring som används av lagrings kontot.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Medelvärde|Antalet tabeller i lagrings kontot.|Inga dimensioner|
|TableEntityCount|Yes|Antal tabell enheter|Antal|Medelvärde|Antalet tabell enheter i lagrings kontot.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Resultat av Sync-session|Antal|Medelvärde|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Återkalla data flöde för moln nivå|BytesPerSecond|Medelvärde|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Yes|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Misslyckade funktions begär Anden|Antal|Totalt|Misslyckade funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Funktions händelser|Antal|Totalt|Funktions händelser|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Funktions begär Anden|Antal|Totalt|Funktions begär Anden|LogicalName, PartitionId|
|ConversionErrors|Yes|Data konverterings fel|Antal|Totalt|Data konverterings fel|LogicalName, PartitionId|
|DeserializationError|Yes|Fel vid deserialisering av indataport|Antal|Totalt|Fel vid deserialisering av indataport|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Händelser som inte är i ordning|Antal|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Tidiga ingångs händelser|Antal|Totalt|Tidiga ingångs händelser|LogicalName, PartitionId|
|Fel|Yes|Körnings fel|Antal|Totalt|Körnings fel|LogicalName, PartitionId|
|InputEventBytes|Yes|Inkommande händelse-byte|Byte|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
|InputEvents|Yes|Inmatade händelser|Antal|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Eftersläpande inloggade ingångs händelser|Antal|Maximal|Eftersläpande inloggade ingångs händelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Mottagna inmatade källor|Antal|Totalt|Mottagna inmatade källor|LogicalName, PartitionId|
|LateInputEvents|Yes|Sena ingångs händelser|Antal|Totalt|Sena ingångs händelser|LogicalName, PartitionId|
|OutputEvents|Yes|Utgående händelser|Antal|Totalt|Utgående händelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|ResourceUtilization|Yes|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|No|Aktivitets körningar avslutad|Antal|Totalt|Antalet Orchestration-aktiviteter som lyckades, misslyckades eller avbröts|Resultat, FailureType, aktivitet, ActivityType, pipeline|
|OrchestrationPipelineRunsEnded|No|Pipelinen körs avslutad|Antal|Totalt|Antal Dirigerings pipelines som lyckades, misslyckades eller avbröts|Resultat, FailureType, pipeline|
|OrchestrationTriggersEnded|No|Slutförda utlösare|Antal|Totalt|Antal Dirigerings utlösare som lyckades, misslyckades eller avbröts|Resultat, FailureType, utlösare|
|SQLOnDemandLoginAttempts|No|Inloggnings försök|Antal|Totalt|Antal inloggnings försök som distribuerats eller misslyckats|Resultat|
|SQLOnDemandQueriesEnded|No|Avslutade frågor|Antal|Totalt|Antal frågor som har lyckats, misslyckats eller avbrutits|Resultat|
|SQLOnDemandQueryProcessedBytes|No|Bearbetade data|Byte|Totalt|Mängden data som bearbetas av frågor|Inga dimensioner|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CoresCapacity|No|Kärnor kapacitet|Antal|Maximal|Kärnor kapacitet|Inga dimensioner|
|MemoryCapacityGB|No|Minnes kapacitet (GB)|Antal|Maximal|Minnes kapacitet (GB)|Inga dimensioner|
|SparkJobsEnded|Yes|Avslutade program|Antal|Totalt|Antal avslutade program|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|No|Procentuellt antal träffar i adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|AdaptiveCacheUsedPercent|No|Procentuell användning av adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|Anslutningar|Yes|Anslutningar|Antal|Totalt|Totalt antal inloggningar till SQL-poolen|Resultat|
|ConnectionsBlockedByFirewall|No|Anslutningar blockerade av brand väggen|Antal|Totalt|Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt|Inga dimensioner|
|DWULimit|No|DWU-gräns|Antal|Maximal|SQL-poolens service nivå mål|Inga dimensioner|
|DWUUsed|No|DWU som används|Antal|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent|Inga dimensioner|
|DWUUsedPercent|No|Procent Använd DWU|Procent|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent|Inga dimensioner|
|LocalTempDBUsedPercent|No|Lokal tempdb Använd procent|Procent|Maximal|Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut|Inga dimensioner|
|MemoryUsedPercent|No|Använt minne i procent|Procent|Maximal|Minnes användning för alla noder i SQL-poolen|Inga dimensioner|
|wlg_effective_min_resource_percent|Yes|Effektiv minsta resurs procent|Procent|Minimum|Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|No|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|No|Allokering av arbets belastnings grupper efter max resurs procent|Procent|Maximal|Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Procent beläggningen av resurser i förhållande till hela systemet|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Yes|Effektiv tak resurs procent|Procent|Maximal|Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessages|Yes|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessages|Yes|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Medelvärde|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Medelvärde|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskReadBytesPerSecond|Yes|Disk-lästa byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|DiskReadLatency|Yes|Läs fördröjning för disk|Millisekunder|Medelvärde|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga dimensioner|
|DiskReadOperations|Yes|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskWriteBytesPerSecond|Yes|Disk-skrivna byte/s|BytesPerSecond|Medelvärde|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|DiskWriteLatency|Yes|Skriv fördröjning för disk|Millisekunder|Medelvärde|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga dimensioner|
|DiskWriteOperations|Yes|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|MemoryActive|Yes|Aktivt minne|Byte|Medelvärde|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga dimensioner|
|MemoryGranted|Yes|Beviljat minne|Byte|Medelvärde|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga dimensioner|
|MemoryUsed|Yes|Använt minne|Byte|Medelvärde|Mängden dator minne som används av den virtuella datorn.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga dimensioner|
|NetworkInBytesPerSecond|Yes|Nätverk i byte/s|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Yes|Nätverks-out-byte/SEK|BytesPerSecond|Medelvärde|Genomsnittligt nätverks data flöde för överförd trafik.|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Medelvärde|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga dimensioner|
|PercentageCpuReady|Yes|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktiva begär Anden|Antal|Totalt|Aktiva begär Anden|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Data ut|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Medelvärde|Diskkölängd|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Yes|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|HttpQueueLength|Yes|Längd på http-kö|Antal|Medelvärde|Längd på http-kö|Instans|
|LargeAppServicePlanInstances|Yes|Stora App Service planera arbetare|Antal|Medelvärde|Stora App Service planera arbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Yes|Medel App Service planera arbetare|Antal|Medelvärde|Medel App Service planera arbetare|Inga dimensioner|
|MemoryPercentage|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begäranden|Instans|
|SmallAppServicePlanInstances|Yes|Små App Service planera arbetare|Antal|Medelvärde|Små App Service planera arbetare|Inga dimensioner|
|TotalFrontEnds|Yes|Totalt antal klient delar|Antal|Medelvärde|Totalt antal klient delar|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|MemoryPercentage|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|WorkersAvailable|Yes|Tillgängliga arbetare|Antal|Medelvärde|Tillgängliga arbetare|Inga dimensioner|
|WorkersTotal|Yes|Totalt antal arbetare|Antal|Medelvärde|Totalt antal arbetare|Inga dimensioner|
|WorkersUsed|Yes|Använda arbetare|Antal|Medelvärde|Använda arbetare|Inga dimensioner|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Data ut|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Medelvärde|CPU-procent|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Medelvärde|Diskkölängd|Instans|
|HttpQueueLength|Yes|Längd på http-kö|Antal|Medelvärde|Längd på http-kö|Instans|
|MemoryPercentage|Yes|Minnes procent|Procent|Medelvärde|Minnes procent|Instans|
|SocketInboundAll|Yes|SocketInboundAll|Antal|Medelvärde|SocketInboundAll|Instans|
|SocketLoopback|Yes|SocketLoopback|Antal|Medelvärde|SocketLoopback|Instans|
|SocketOutboundAll|Yes|SocketOutboundAll|Antal|Medelvärde|SocketOutboundAll|Instans|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Antal|Medelvärde|SocketOutboundEstablished|Instans|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Antal|Medelvärde|SocketOutboundTimeWait|Instans|
|TcpCloseWait|Yes|Väntan på TCP-stängning|Antal|Medelvärde|Väntan på TCP-stängning|Instans|
|TcpClosing|Yes|TCP-stängning|Antal|Medelvärde|TCP-stängning|Instans|
|TcpEstablished|Yes|TCP upprättad|Antal|Medelvärde|TCP upprättad|Instans|
|TcpFinWait1|Yes|TCP räntetrans wait 1|Antal|Medelvärde|TCP räntetrans wait 1|Instans|
|TcpFinWait2|Yes|TCP räntetrans wait 2|Antal|Medelvärde|TCP räntetrans wait 2|Instans|
|TcpLastAck|Yes|TCP-senaste ack|Antal|Medelvärde|TCP-senaste ack|Instans|
|TcpSynReceived|Yes|TCP-syn mottagen|Antal|Medelvärde|TCP-syn mottagen|Instans|
|TcpSynSent|Yes|TCP-syn har skickats|Antal|Medelvärde|TCP-syn har skickats|Instans|
|TcpTimeWait|Yes|Väntan på TCP-tid|Antal|Medelvärde|Väntan på TCP-tid|Instans|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (exklusive funktioner) 

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

> [!IMPORTANT]
> Den **genomsnittliga svars tiden** blir föråldrad för att undvika förvirring med mått agg regeringar. Använd **svars tid** som ersättning.

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Medelvärde|Anslutningar|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid **(inaktuell)**|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Data ut|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|FileSystemUsage|Yes|Fil system användning|Byte|Medelvärde|Fil system användning|Inga dimensioner|
|Gen0Collections|Yes|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Yes|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Yes|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|Konsolindataobjekt|Yes|Antal referenser|Antal|Medelvärde|Antal referenser|Instans|
|HealthCheckStatus|Yes|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|HttpResponseTime|Yes|Svars tid|Sekunder|Medelvärde|Svars tid|Instans|
|IoOtherBytesPerSecond|Yes|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoOtherOperationsPerSecond|Yes|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|IoReadBytesPerSecond|Yes|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoReadOperationsPerSecond|Yes|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteBytesPerSecond|Yes|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoWriteOperationsPerSecond|Yes|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|MemoryWorkingSet|Yes|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begäranden|Instans|
|RequestsInApplicationQueue|Yes|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|Konversation|Yes|Antal trådar|Antal|Medelvärde|Antal trådar|Instans|
|TotalAppDomains|Yes|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (Functions)

> [!NOTE]
> **Användning av fil system** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har varit vit listas för privat för hands version.

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Data ut|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|FileSystemUsage|Yes|Fil system användning|Byte|Medelvärde|Fil system användning|Inga dimensioner|
|FunctionExecutionCount|Yes|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Yes|Funktions körnings enheter|Antal|Totalt|[Funktions körnings enheter](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instans|
|Gen0Collections|Yes|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Yes|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Yes|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|HealthCheckStatus|Yes|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|IoOtherBytesPerSecond|Yes|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoOtherOperationsPerSecond|Yes|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|IoReadBytesPerSecond|Yes|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoReadOperationsPerSecond|Yes|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteBytesPerSecond|Yes|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoWriteOperationsPerSecond|Yes|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|MemoryWorkingSet|Yes|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|RequestsInApplicationQueue|Yes|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|TotalAppDomains|Yes|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Medelvärde|Anslutningar|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbets mängd för minne|Byte|Medelvärde|Genomsnittlig arbets mängd för minne|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid|Sekunder|Medelvärde|Genomsnittlig svars tid|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Data i|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Data ut|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|CPU-tid|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Medelvärde|Aktuella sammansättningar|Instans|
|FileSystemUsage|Yes|Fil system användning|Byte|Medelvärde|Fil system användning|Inga dimensioner|
|FunctionExecutionCount|Yes|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Yes|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|Gen0Collections|Yes|Skräp insamling för gen 0|Antal|Totalt|Skräp insamling för gen 0|Instans|
|Gen1Collections|Yes|Skräp insamling för gen 1|Antal|Totalt|Skräp insamling för gen 1|Instans|
|Gen2Collections|Yes|Gen 2 skräp insamling|Antal|Totalt|Gen 2 skräp insamling|Instans|
|Konsolindataobjekt|Yes|Antal referenser|Antal|Medelvärde|Antal referenser|Instans|
|HealthCheckStatus|Yes|Hälso kontroll status|Antal|Medelvärde|Hälso kontroll status|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Http 101|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Http-2xx|Instans|
|Http3xx|Yes|Http-3xx|Antal|Totalt|Http-3xx|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Http 401|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Http 403|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Http 404|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Http 406|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Http-4xx|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Http-serverfel|Instans|
|HttpResponseTime|Yes|Svars tid|Sekunder|Medelvärde|Svars tid|Instans|
|IoOtherBytesPerSecond|Yes|I/o andra byte per sekund|BytesPerSecond|Totalt|I/o andra byte per sekund|Instans|
|IoOtherOperationsPerSecond|Yes|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Andra i/o-åtgärder per sekund|Instans|
|IoReadBytesPerSecond|Yes|IO-lästa byte per sekund|BytesPerSecond|Totalt|IO-lästa byte per sekund|Instans|
|IoReadOperationsPerSecond|Yes|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|IO-Läs åtgärder per sekund|Instans|
|IoWriteBytesPerSecond|Yes|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Skrivna byte i i/o per sekund|Instans|
|IoWriteOperationsPerSecond|Yes|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|I/o-Skriv åtgärder per sekund|Instans|
|MemoryWorkingSet|Yes|Minnes arbets mängd|Byte|Medelvärde|Minnes arbets mängd|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Medelvärde|Privata byte|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begäranden|Instans|
|RequestsInApplicationQueue|Yes|Begär anden i program kön|Antal|Medelvärde|Begär anden i program kön|Instans|
|Konversation|Yes|Antal trådar|Antal|Medelvärde|Antal trådar|Instans|
|TotalAppDomains|Yes|Totalt antal app-domäner|Antal|Medelvärde|Totalt antal app-domäner|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal app-domäner som har inaktiverats|Antal|Medelvärde|Totalt antal app-domäner som har inaktiverats|Instans|


## <a name="next-steps"></a>Nästa steg
* [Läs om mått i Azure Monitor](data-platform.md)
* [Skapa aviseringar för mått](alerts-overview.md)
* [Exportera mått till lagring, Event Hub eller Log Analytics](platform-logs-overview.md)
