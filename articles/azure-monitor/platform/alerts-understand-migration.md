---
title: Förstå hur verktyget frivillig migrering fungerar för Azure Monitor-aviseringar
description: Förstå hur Migreringsverktyget aviseringar fungerar och felsöka problem.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295531"
---
# <a name="understand-how-the-migration-tool-works"></a>Förstå hur Migreringsverktyget fungerar

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är att dras tillbaka den 31 augusti 2019 (var ursprungligen juni 30 2019). Migreringsverktyg finns i Azure portal för att kunder som använder klassiska Varningsregler och som vill aktivera migrering själva.

Den här artikeln förklarar hur du verktyget frivillig migrering. Här beskrivs också lösningar för några vanliga problem.

> [!NOTE]
> Fördröjning i lansering av migreringsverktyget slutdatum för klassiska aviseringar migrering har [utökats till den 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) ursprungligen presenterade efter den 30 juni 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klassiska Varningsregler som inte migreras

> [!IMPORTANT]
> Aktivitetsloggaviseringar (inklusive Service health aviseringar) och aviseringar som inte påverkas av migreringen. Migreringen gäller bara i klassiska Varningsregler beskrivs [här](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Även om verktyget kan migrera nästan alla [klassiska Varningsregler](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), det finns vissa undantag. Följande Varningsregler migreras inte med hjälp av verktyget (eller vid automatisk migrering från September 2019):

- Klassiska Varningsregler på virtuella datorer gästmått (både Windows och Linux). Se den [vägledning för att återskapa sådana aviseringsregler i nya måttaviseringar](#guest-metrics-on-virtual-machines) senare i den här artikeln.
- Klassiska Varningsregler för mått för klassisk lagring. Se den [vägledning för övervakning av din klassiska lagringskonton](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klassiska Varningsregler på vissa mått för lagringskonto. Se [information](#storage-account-metrics) senare i den här artikeln.
- Klassiska Varningsregler på vissa Cosmos DB-mått. Information läggs till i en kommande uppdatering.

Om din prenumeration har sådana klassiska regler, måste du migrera dem manuellt. Eftersom vi inte kan ge en automatisk migrering, kommer alla befintliga, klassiska måttaviseringar olika typerna fortsätta att fungera fram till juni 2020. Det här tillägget ger dags att flytta till nya aviseringar. Inga nya klassiska aviseringar kan dock skapas efter augusti 2019.

> [!NOTE]
> Förutom de ovan listade undantag är om din klassiska Varningsregler är ogiltiga dvs. de är på [föråldrade mått](#classic-alert-rules-on-deprecated-metrics) eller resurser som har tagits bort, de kommer inte att migreras under frivillig migrering. Sådana ogiltig klassiska Varningsregler tas bort när automatisk migrering sker.

### <a name="guest-metrics-on-virtual-machines"></a>Gästmått på virtuella datorer

Innan du kan skapa nya måttaviseringar på gästmått, måste gästmått skickas till arkivet för anpassade mått Azure Monitor. Följ dessa instruktioner för att aktivera Azure Monitor-kanalmottagare i diagnostikinställningar:

- [Aktivera gästmått för Windows-datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Aktivera gästmått för virtuella Linux-datorer](collect-custom-metrics-linux-telegraf.md)

När de här stegen är klar kan du skapa nya måttaviseringar på gästmått. Och när du har skapat nya måttaviseringar kan du ta bort klassiska aviseringar.

### <a name="storage-account-metrics"></a>Mått för lagringskonto

Alla klassiska aviseringar på storage-konton kan migreras utom aviseringar i dessa mått:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klassiska varningen regler på procent mått måste migreras [mappningen mellan gamla och nya lagringsmått](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på rätt sätt eftersom det nya måttet som är tillgängliga är ett absolut.

Klassiska Varningsregler på AnonymousThrottlingError, SASThrottlingError och ThrottlingError måste delas upp i två nya aviseringar eftersom det finns inga kombinerade mått som fungerar på samma sätt. Tröskelvärden måste anpassas på rätt sätt.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassiska Varningsregler på föråldrade mått

Det här är klassiska Varningsregler för mått som tidigare stöddes men så småningom tagits bort. En liten andel av kunden ha ogiltiga klassiska Varningsregler för mått. Eftersom dessa Varningsregler är ogiltiga, migreras de inte.

| Resurstyp| Föråldrad metric(s) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hur skapar motsvarande nya Varningsregler och åtgärdsgrupper

Migreringsverktyget konverterar klassiska notifieringsregler till motsvarande nya Varningsregler och åtgärdsgrupper. För de flesta klassiska Varningsregler motsvarande nya Varningsregler finns på samma mått med samma egenskaper som `windowSize` och `aggregationType`. Det finns dock vissa klassiska avisering innehåller inte mått som har ett annat, motsvarande mått i det nya systemet. Följande principer gäller för migrering av klassiska aviseringar om inget anges i avsnittet nedan:

- **Frekvens**: Definierar hur ofta en klassisk eller ny aviseringsregel kontrollerar för villkoret. Den `frequency` i klassiska Varningsregler var inte kan konfigureras av användaren och alltid 5 minuter för alla resurstyper utom Application Insights-komponenter som den var 1 min. Så är frekvensen av motsvarande regler också inställd på 5 minuter och 1 min respektive.
- **Sammansättningstyp**: Definierar hur måttet slås samman under fönstret av intresse. Den `aggregationType` också är samma mellan klassiska aviseringar och nya aviseringar för de flesta mått. I vissa fall, eftersom måttet skiljer sig mellan klassiska aviseringar och nya aviseringar, motsvarande `aggregationType` eller `primary Aggregation Type` definierats för måttet används.
- **Enheter**: Egenskapen för måttet där aviseringen skapades. Vissa motsvarande mått har olika enheter. Tröskelvärdet justeras korrekt efter behov. Om den ursprungliga måtten har sekunder som enheter men motsvarande nya mått har millisekunder som enheter, till exempel multipliceras ursprungliga tröskelvärdet med 1000 att se till att samma beteende.
- **Fönsterstorlek**: Definierar fönstret över vilka mått samlas data om du vill jämföra med tröskeln. För standard `windowSize` värden som 5 minuter, 15mins, 30 minuter, 1 timme, tre timmar, 6 timmar, 12 timmar, 1 dag ställs det finns inga ändringar gjorts för motsvarande ny aviseringsregel. För andra värden, den som ligger närmast `windowSize` väljs som ska användas. För de flesta kunder påverkas inte med den här ändringen. För en liten andel av kunder kan det finnas ett behov av att justera tröskelvärdet för att få exakt samma beteende.

I följande avsnitt förklarar vi vi de mått som har ett annat, motsvarande mått i det nya systemet. Vilka mått som är likadan i klassiska och nya Varningsregler visas inte. Du hittar en lista över mått som stöds i det nya systemet [här](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

För Storage-konto-tjänster som blob, tabell, fil och kön mappas följande mått till motsvarande mått som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentar|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transaktioner mått med dimensioner ”ResponseType” = ”AuthorizationError” och ”autentisering” = ”anonym”| |
| AnonymousClientOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ClientOtherError” och ”autentisering” = ”anonym” | |
| AnonymousClientTimeOutError| Transaktioner mått med dimensioner ”ResponseType” = ”ClientTimeOutError” och ”autentisering” = ”anonym” | |
| AnonymousNetworkError | Transaktioner mått med dimensioner ”ResponseType” = ”NetworkError” och ”autentisering” = ”anonym” | |
| AnonymousServerOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerOtherError” och ”autentisering” = ”anonym” | |
| AnonymousServerTimeOutError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerTimeOutError” och ”autentisering” = ”anonym” | |
| AnonymousSuccess | Transaktioner mått med dimensioner ”ResponseType” = ”lyckades” och ”autentisering” = ”anonym” | |
| AuthorizationError | Transaktioner mått med dimensioner ”ResponseType” = ”AuthorizationError” | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacitet | BlobCapacity | Använd `aggregationType` ”genomsnitt” i stället för ”senaste”. Mått gäller endast för Blob-tjänster |
| ClientOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ClientOtherError”  | |
| ClientTimeoutError | Transaktioner mått med dimensioner ”ResponseType” = ”ClientTimeOutError” | |
| ContainerCount | ContainerCount | Använd `aggregationType` ”genomsnitt” i stället för ”senaste”. Mått gäller endast för Blob-tjänster |
| NetworkError | Transaktioner mått med dimensioner ”ResponseType” = ”NetworkError” | |
| ObjectCount | BlobCount| Använd `aggregationType` ”genomsnitt” i stället för ”senaste”. Mått gäller endast för Blob-tjänster |
| SASAuthorizationError | Transaktioner mått med dimensioner ”ResponseType” = ”AuthorizationError” och ”autentisering” = ”SAS” | |
| SASClientOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ClientOtherError” och ”autentisering” = ”SAS” | |
| SASClientTimeOutError | Transaktioner mått med dimensioner ”ResponseType” = ”ClientTimeOutError” och ”autentisering” = ”SAS” | |
| SASNetworkError | Transaktioner mått med dimensioner ”ResponseType” = ”NetworkError” och ”autentisering” = ”SAS” | |
| SASServerOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerOtherError” och ”autentisering” = ”SAS” | |
| SASServerTimeOutError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerTimeOutError” och ”autentisering” = ”SAS” | |
| SASSuccess | Transaktioner mått med dimensioner ”ResponseType” = ”lyckades” och ”autentisering” = ”SAS” | |
| ServerOtherError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerOtherError” | |
| ServerTimeOutError | Transaktioner mått med dimensioner ”ResponseType” = ”ServerTimeOutError”  | |
| Klart | Transaktioner mått med dimensioner ”ResponseType” = ”klar” | |
| TotalBillableRequests| Transaktioner | |
| TotalEgress | Egress | |
| TotalIngress | Ingress | |
| TotalRequests | Transaktioner | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

För Application Insights är motsvarande mått som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentar|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/varaktighet| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| basicExceptionBrowser.count | undantag/webbläsare|  Använd `aggregationType` , count, i stället för ”sum”. |
| basicExceptionServer.count | undantag/server| Använd `aggregationType` , count, i stället för ”sum”.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Tröskelvärdet måste ändras på rätt sätt när ursprungliga mått har över alla kärnor och nya mått normaliseras till kärna. Migreringsverktyget ändras inte tröskelvärden.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | begäranden/varaktighet| Multiplicera ursprungliga tröskelvärdet med 1000 enheter för klassiska måttet anges i sekunder och för nya en anges i millisekunder.  |
| request.rate | begäranden/hastighet|   |
| requestFailed.count | begäranden/misslyckades| Använd `aggregationType` , count, i stället för ”sum”.   |
| View.Count | pageViews/count| Använd `aggregationType` , count, i stället för ”sum”.   |

### <a name="how-equivalent-action-groups-are-created"></a>Hur motsvarande åtgärdsgrupper skapas

Klassiska regler hade e-post, webhook, logic-appen och runbook-åtgärder som är kopplad till aviseringen aviseringsregeln själva. Ny Varningsregler använda åtgärdsgrupper som kan återanvändas i flera Varningsregler. Migreringsverktyget skapar enskild åtgärdsgrupp för samma åtgärder oavsett hur många Varningsregler använder åtgärden. Åtgärdsgrupper som skapats av migreringsverktyget använda namngivningsformat 'Migrated_AG * ”.

## <a name="rollout-phases"></a>Distributionsfaser

Migreringsverktyget är lanseras i faser för kunder som använder klassiska Varningsregler. Prenumeranter får ett e-postmeddelande när prenumerationen är redo att migreras med hjälp av verktyget.

> [!NOTE]
> Eftersom verktyget lanseras i faser, kan du se att vissa av dina prenumerationer inte ännu redo att migreras under tidiga faser.

De flesta av prenumerationerna som är för närvarande markerade som klar för migrering. Endast prenumerationer som har klassiska aviseringar om följande typer av resurser är fortfarande inte klar för migrering.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Vem som kan utlösa migreringen?

Alla användare som har den inbyggda rollen som deltagare för övervakning på prenumerationsnivå kan utlösa migreringen. Användare som har en anpassad roll med följande behörigheter kan även utlösa migreringen:

- \* / läsa
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Förutom ovan behörigheter registreras prenumerationen dessutom med Microsoft.AlertsManagement-resursprovidern. Detta är nödvändigt att migrera fel Avvikelseidentifiering aviseringar i Application Insights. 

## <a name="common-problems-and-remedies"></a>Vanliga problem och lösningar

När du [utlösa migreringen](alerts-using-migration-tool.md), får du e-post till de adresser som du angav som meddelar att migreringen är klar eller om någon åtgärd krävs från dig. Det här avsnittet beskriver några vanliga problem och hur du hanterar dem.

### <a name="validation-failed"></a>Verifieringen misslyckades

Prenumerationen på grund av vissa ändringar i klassiska Varningsregler i din prenumeration kan inte migreras. Det här problemet är tillfälligt. Du kan starta om migreringen när Migreringsstatus för flyttas tillbaka **klar för migrering** några dagar.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Principen eller ett omfång lås som förhindrar oss från att migrera dina regler

Nya aviseringar för mått och nya åtgärdsgrupper kommer att skapas som en del av migreringen, och sedan klassiska Varningsregler kommer att tas bort. Det finns dock en princip eller ett omfång lås som förhindrar oss från att skapa resurser. Vissa eller alla regler gick inte att migrera beroende på låset princip eller ett omfång. Du kan lösa problemet genom att ta bort lås för omfånget eller princip tillfälligt och utlösa migreringen igen.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du Migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migrering](alerts-prepare-migration.md)
