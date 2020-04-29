---
title: Förstå Migreringsverktyg för Azure Monitor aviseringar
description: Förstå hur Migreringsverktyget för aviseringar fungerar och Felsök problem.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114313"
---
# <a name="understand-how-the-migration-tool-works"></a>Förstå hur migreringsverktyget fungerar

Som [tidigare](monitoring-classic-retirement.md)meddelats kommer de klassiska aviseringarna i Azure monitor att dras tillbaka den 31 augusti 2019 (ursprungligen 30 juni 2019). Ett Migreringsverktyg är tillgängligt i Azure Portal till kunder som använder klassiska aviserings regler och som vill utlösa migrering själva.

I den här artikeln förklaras hur det frivilliga migrations verktyget fungerar. Det beskriver också lösningar för några vanliga problem.

> [!NOTE]
> På grund av fördröjning i uppsamlingen av migreringen har den senaste indragnings tiden för migrering av klassisk avisering [utökats till 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) från det ursprungligen presenterade datumet den 30 juni 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klassiska aviseringsregler som inte kommer att migreras

> [!IMPORTANT]
> Aktivitets logg aviseringar (inklusive tjänste hälso aviseringar) och logg aviseringar påverkas inte av migreringen. Migreringen gäller endast klassiska varnings regler som beskrivs [här](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Även om verktyget kan migrera nästan alla [klassiska varnings regler](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)finns det vissa undantag. Följande varnings regler migreras inte med hjälp av verktyget (eller under den automatiska migreringen från och med 2019 september):

- Klassiska aviserings regler för gäst mått på virtuella datorer (både Windows och Linux). Se [rikt linjerna för att återskapa varnings regler i nya mått aviseringar](#guest-metrics-on-virtual-machines) senare i den här artikeln.
- Klassiska aviserings regler för klassiska lagrings mått. Se [vägledningen för att övervaka dina klassiska lagrings konton](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klassiska aviserings regler för vissa lagrings konto mått. Se [informationen](#storage-account-metrics) längre fram i den här artikeln.
- Klassiska varnings regler för vissa Cosmos DB mått. Se [informationen](#cosmos-db-metrics) längre fram i den här artikeln.
- Klassiska varnings regler för alla klassiska virtuella datorer och Cloud Services-mått (Microsoft. ClassicCompute/virtualMachines och Microsoft. ClassicCompute/domän namn/platser/roller). Se [informationen](#classic-compute-metrics) längre fram i den här artikeln.

Om din prenumeration har några sådana klassiska regler måste du migrera dem manuellt. Eftersom vi inte kan tillhandahålla en automatisk migrering, kommer alla befintliga, klassiska mått varningar för dessa typer att fortsätta att fungera fram till 2020 juni. Med det här tillägget får du tid att gå vidare till nya aviseringar. Du kan också fortsätta att skapa nya klassiska aviseringar på ovanstående undantag till juni 2020. Men för allt annat kan inga nya klassiska aviseringar skapas efter 2019 augusti.

> [!NOTE]
> Förutom ovanstående undantag, om de klassiska varnings reglerna är ogiltiga, dvs. de finns i [inaktuella mått](#classic-alert-rules-on-deprecated-metrics) eller resurser som har tagits bort, kommer de inte att migreras och kommer inte att vara tillgängliga när tjänsten har dragits tillbaka.

### <a name="guest-metrics-on-virtual-machines"></a>Gäst mått på virtuella datorer

Innan du kan skapa nya mått aviseringar för gäst mått måste gäst måtten skickas till Azure Monitor anpassade mått lager. Följ dessa anvisningar för att aktivera Azure Monitor-mottagare i diagnostikinställningar:

- [Aktivera gäst mått för virtuella Windows-datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Aktivera gäst mått för virtuella Linux-datorer](collect-custom-metrics-linux-telegraf.md)

När de här stegen är klara kan du skapa nya mått aviseringar för gäst mått. När du har skapat nya mått aviseringar kan du ta bort klassiska aviseringar.

### <a name="storage-account-metrics"></a>Mått för lagringskonto

Alla klassiska aviseringar för lagrings konton kan migreras utom aviseringar för dessa mått:

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

Klassiska varnings regler för procent mått måste migreras baserat på [mappningen mellan gamla och nya lagrings mått](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på lämpligt sätt eftersom det nya måttet är ett absolut värde.

Klassiska varnings regler för AnonymousThrottlingError, SASThrottlingError och ThrottlingError måste delas upp i två nya aviseringar eftersom det inte finns något kombinerat mått som ger samma funktion. Tröskelvärden måste anpassas på lämpligt sätt.

### <a name="cosmos-db-metrics"></a>Cosmos DB-mått

Alla klassiska varningar om Cosmos DB mått kan migreras utom aviseringar för dessa mått:

- Genomsnittligt antal begär Anden per sekund
- Konsekvensnivå
- Http-2xx
- Http-3xx
- Http 400
- Http 401
- Internt Server fel
- Maximalt antal förbrukade RUPM per minut
- Max ru: er per sekund
- Antal misslyckade begär Anden om mongo
- Mongo ta bort misslyckade förfrågningar
- Mongo infogning av misslyckade begär Anden
- Mongo andra misslyckade förfrågningar
- Mongo annan begär ande avgift
- Mongo annan begär ande frekvens
- Misslyckade förfrågningar för mongo-fråga
- Misslyckade begär Anden om uppdatering av mongo
- Observerad Läs fördröjning
- Observerad Skriv fördröjning
- Tjänst tillgänglighet
- Lagringskapacitet
- Begränsade begär Anden
- Totalt antal förfrågningar

Genomsnittligt antal begär Anden per sekund, konsekvens nivå, Max RUPM förbrukat per minut, Max ru: er per sekund, observerad Läs fördröjning, observerad Skriv fördröjning, lagrings kapacitet är för närvarande inte tillgängligt i det [nya systemet](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Aviseringar om begär ande mått som http-2xx, http-3xx, http 400, HTTP 401, internt Server fel, tjänst tillgänglighet, begränsade begär Anden och totalt antal begär Anden migreras inte eftersom Hur begär Anden räknas ut skiljer sig mellan klassiska mått och nya mått. Aviseringar för dessa måste återskapas manuellt med tröskelvärdena justerade.

Aviseringar på Mongo misslyckade begär Anden mått måste delas upp i flera aviseringar eftersom det inte finns några kombinerade mått som ger samma funktion. Tröskelvärden måste anpassas på lämpligt sätt.

### <a name="classic-compute-metrics"></a>Klassiska beräknings mått

Det går inte att migrera några varningar om klassiska beräknings mått med hjälp av Migreringsverktyget eftersom de klassiska beräknings resurserna inte stöds ännu med nya aviseringar. Stöd för nya aviseringar för dessa resurs typer kommer att läggas till i framtiden. När den är tillgänglig måste kunderna återskapa nya motsvarande varnings regler baserat på deras klassiska varnings regler före 2020 juni.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassiska varnings regler för inaktuella mått

Dessa är klassiska varnings regler för mått som tidigare stöddes men som tidigare har varit inaktuella. En liten procent andel av kunden kan ha ogiltiga klassiska aviserings regler för sådana mått. Eftersom dessa varnings regler är ogiltiga migreras de inte.

| Resurstyp| Föråldrade mått |
|-------------|----------------- |
| Microsoft. DBforMySQL/servers | compute_consumption_percent compute_limit |
| Microsoft. DBforPostgreSQL/servers | compute_consumption_percent compute_limit |
| Microsoft. Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft. SQL/Servers/databaser | service_level_objective, storage_limit, storage_used, begränsning, dtu_consumption_percent, storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hur motsvarande nya varnings regler och åtgärds grupper skapas

Migreringsverktyget konverterar de klassiska varnings reglerna till motsvarande nya varnings regler och åtgärds grupper. För de flesta klassiska varnings regler finns motsvarande nya varnings regler på samma mått med samma egenskaper som `windowSize` och. `aggregationType` Det finns dock vissa klassiska varnings regler på mått som har ett annat, motsvarande mått i det nya systemet. Följande principer gäller för migrering av klassiska varningar, om inte anges i avsnittet nedan:

- **Frekvens**: anger hur ofta en klassisk eller ny varnings regel kontrollerar villkoret. I `frequency` de klassiska varnings reglerna kunde inte konfigureras av användaren och var alltid 5 minuter för alla resurs typer förutom Application Insights-komponenter som den var 1 min. Därför är frekvensen av motsvarande regler också inställd på 5 min respektive 1 min.
- **Sammansättnings typ**: definierar hur måttet ska aggregeras över intresse fönstret. `aggregationType` Är också detsamma mellan klassiska aviseringar och nya aviseringar för de flesta mått. I vissa fall, eftersom måttet skiljer sig mellan klassiska aviseringar och nya aviseringar `aggregationType` , används `primary Aggregation Type` motsvarande eller det definierade värdet för måttet.
- **Units (units**): egenskapen för måttet som aviseringen skapas för. Vissa motsvarande mått har olika enheter. Tröskelvärdet justeras på lämpligt sätt vid behov. Om till exempel det ursprungliga måttet har sekunder som enheter men motsvarande nya mått har millisekunder som enheter multipliceras det ursprungliga tröskelvärdet med 1000 för att säkerställa samma beteende.
- **Fönster storlek**: definierar fönstret över vilka mått data aggregeras för att jämföra mot tröskelvärdet. För `windowSize` standardvärden som 5mins, 15mins, 30mins, efter, 3hours, 6 timmar, 12 timmar, 1 dag, har inga ändringar gjorts för motsvarande nya aviserings regel. För andra värden väljs närmast `windowSize` som ska användas. För de flesta kunder påverkas inte den här ändringen. För en liten del av kunderna kan det vara nödvändigt att justera tröskelvärdet för att få exakt samma beteende.

I följande avsnitt beskriver vi de mått som har ett annat, motsvarande mått i det nya systemet. Alla mått som förblir desamma för klassiska och nya varnings regler visas inte. Du kan hitta en lista över mått som stöds i det nya systemet [här](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft. StorageAccounts/Services

För lagrings konto tjänster som BLOB, Table, File och Queue mappas följande mått till motsvarande mått enligt nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" och "Authentication" = "Anonymous"| |
| AnonymousClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError" och "Authentication" = "Anonymous" | |
| AnonymousClientTimeOutError| Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" och "Authentication" = "Anonymous" | |
| AnonymousNetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" och "Authentication" = "Anonymous" | |
| AnonymousServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" och "Authentication" = "Anonymous" | |
| AnonymousServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError" och "Authentication" = "Anonymous" | |
| AnonymousSuccess | Transaktions mått med dimensionerna "ResponseType" = "lyckades" och "autentisering" = "Anonym" | |
| AuthorizationError | Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacitet | BlobCapacity | Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| ClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| NetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| SASAuthorizationError | Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" och "Authentication" = "SAS" | |
| SASClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError" och "Authentication" = "SAS" | |
| SASClientTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" och "Authentication" = "SAS" | |
| SASNetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" och "Authentication" = "SAS" | |
| SASServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" och "Authentication" = "SAS" | |
| SASServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError" och "Authentication" = "SAS" | |
| SASSuccess | Transaktions mått med dimensionerna "ResponseType" = "lyckades" och "Authentication" = "SAS" | |
| ServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError"  | |
| Klart | Transaktions mått med dimensionerna "ResponseType" = "lyckades" | |
| TotalBillableRequests| Transaktioner | |
| TotalEgress | Utgående | |
| TotalIngress | Ingress | |
| TotalRequests | Transaktioner | |

### <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

För Application Insights är motsvarande mått det som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| tillgänglighet. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| tillgänglighet. durationMetric. Value | availabilityResults/varaktighet| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| basicExceptionBrowser. Count | undantag/webbläsare|  Använd `aggregationType` Count i stället för sum. |
| basicExceptionServer. Count | undantag/Server| Använd `aggregationType` Count i stället för sum.  |
| clientPerformance. clientProcess. Value | browserTimings/processingDuration| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| clientPerformance. networkConnection. Value | browserTimings/networkDuration|  Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder. |
| clientPerformance. receiveRequest. Value | browserTimings/receiveDuration| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| clientPerformance. sendRequest. Value | browserTimings/sendDuration| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| clientPerformance. total. Value | browserTimings/totalDuration| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| performanceCounter. available_bytes. Value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter. io_data_bytes_per_sec. Value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter. number_of_exceps_thrown_per_sec. Value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter. percentage_processor_time_normalized. Value | performanceCounters/processCpuPercentage|   |
| performanceCounter. percentage_processor_time. Value | performanceCounters/processCpuPercentage| Tröskelvärdet måste ändras på lämpligt sätt eftersom det ursprungliga måttet var över alla kärnor och det nya måttet normaliseras till en kärna. Migration Tool ändrar inte tröskelvärden.  |
| performanceCounter. percentage_processor_total. Value | performanceCounters/processorCpuPercentage|   |
| performanceCounter. process_private_bytes. Value | performanceCounters/processPrivateBytes|   |
| performanceCounter. request_execution_time. Value | performanceCounters/requestExecutionTime|   |
| performanceCounter. requests_in_application_queue. Value | performanceCounters/requestsInQueue|   |
| performanceCounter. requests_per_sec. Value | performanceCounters/requestsPerSecond|   |
| begäran. varaktighet | begär Anden/varaktighet| Multiplicera det ursprungliga tröskelvärdet med 1000 eftersom enheter för klassiskt mått är i sekunder och för det nya är i millisekunder.  |
| begäran. Rate | begär Anden/pris|   |
| requestFailed. Count | begär Anden/misslyckade| Använd `aggregationType` Count i stället för sum.   |
| Visa. Count | pageViews/antal| Använd `aggregationType` Count i stället för sum.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

För Cosmos DB är motsvarande mått det som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Data storlek | DataUsage| |
| Antal dokument | DocumentCount||
| Index storlek | IndexUsage||
| Avgift för mongo antal förfrågningar| MongoRequestCharge med dimension "CommandName" = "count"||
| Begär ande frekvens för mongo antal | MongoRequestsCount med dimension "CommandName" = "count"||
| Mongo ta bort begär ande avgift | MongoRequestCharge med dimensionen "CommandName" = "Delete"||
| Mongo ta bort begär ande frekvens | MongoRequestsCount med dimensionen "CommandName" = "Delete"||
| Mongo infoga begär ande avgift | MongoRequestCharge med dimension "CommandName" = "Infoga"||
| Mongo infoga begär ande frekvens | MongoRequestsCount med dimension "CommandName" = "Infoga"||
| Mongo för förfrågan | MongoRequestCharge med dimensionen "CommandName" = "Find"||
| Mongo för förfrågningar | MongoRequestsCount med dimensionen "CommandName" = "Find"||
| Avgift för mongo uppdaterings begär Anden | MongoRequestCharge med dimension "CommandName" = "uppdatera"||
| Tjänsten är inte tillgänglig| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Hur motsvarande åtgärds grupper skapas

De klassiska varnings reglerna hade e-post, webhook, Logic app och Runbook-åtgärder som är kopplade till själva varnings regeln. Nya varnings regler använder åtgärds grupper som kan återanvändas över flera aviserings regler. Migreringsverktyget skapar en enda åtgärds grupp för samma åtgärder, oavsett hur många varnings regler som använder åtgärden. Åtgärds grupper som skapats av Migreringsverktyget använder namngivnings formatet Migrated_AG *.

> [!NOTE]
> Klassiska varningar skickade lokaliserade e-postmeddelanden baserat på de nationella inställningarna för klassisk administratör när de används för att meddela klassiska administratörs roller. Nya e-postaviseringar skickas via åtgärds grupper och finns bara på engelska.

## <a name="rollout-phases"></a>Distributionsfaser

Migrations verktyget distribueras i faser till kunder som använder klassiska aviserings regler. Prenumerations ägare får ett e-postmeddelande när prenumerationen är redo att migreras med verktyget.

> [!NOTE]
> Eftersom verktyget lanseras i faser kan du se att några av dina prenumerationer ännu inte är redo att migreras under de tidiga faserna.

De flesta prenumerationer är för närvarande markerade som klara för migrering. Endast prenumerationer som har klassiska aviseringar på följande resurs typer är fortfarande inte klara för migrering.

- Microsoft. classicCompute/domän namn/platser/roller
- Microsoft. Insights/komponenter

## <a name="who-can-trigger-the-migration"></a>Vem kan utlösa migreringen?

Alla användare som har den inbyggda rollen som övervakar deltagare på prenumerations nivån kan utlösa migreringen. Användare som har en anpassad roll med följande behörigheter kan även utlösa migreringen:

- */read
- Microsoft. Insights/actiongroups/*
- Microsoft. Insights/AlertRules/*
- Microsoft. Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Förutom att ha över behörigheter bör din prenumeration Dessutom registreras med Microsoft. AlertsManagement Resource Provider. Detta krävs för att kunna migrera fel avvikelse aviseringar på Application Insights. 

## <a name="common-problems-and-remedies"></a>Vanliga problem och lösningar

När du har [utlöst migreringen](alerts-using-migration-tool.md)får du e-post till de adresser du angav för att meddela dig att migreringen är klar eller om någon åtgärd krävs från dig. I det här avsnittet beskrivs några vanliga problem och hur du hanterar dem.

### <a name="validation-failed"></a>Verifieringen misslyckades

På grund av några nya ändringar i de klassiska varnings reglerna i din prenumeration går det inte att migrera prenumerationen. Det här problemet är tillfälligt. Du kan starta om migreringen när migrerings statusen har flyttats tillbaka **för migrering** på några få dagar.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Områdes lås som hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya mått varningar och nya åtgärds grupper och sedan tas de klassiska varnings reglerna bort. Ett områdes lås kan dock hindra oss från att skapa eller ta bort resurser. Det gick inte att migrera vissa eller alla regler beroende på omfångs låset. Du kan lösa det här problemet genom att ta bort omfångs låset för prenumerationen, resurs gruppen eller resursen, som visas i [migreringsverktyget](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)och utlösa migreringen igen. Det går inte att inaktivera områdes låset och det måste tas bort under migreringsprocessen. [Läs mer om hur du hanterar områdes lås](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Princip med "Neka"-inverkan som hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya mått varningar och nya åtgärds grupper och sedan tas de klassiska varnings reglerna bort. En princip kan dock hindra oss från att skapa resurser. Vissa eller alla regler kan inte migreras beroende på principen. De principer som blockerar processen visas i listan i [migreringsverktyget](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Lös det här problemet genom att antingen:

- Exklusive prenumerationer eller resurs grupper för varaktigheten för migreringsprocessen från princip tilldelningen. [Lär dig mer om att hantera undantags omfång för principer](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Om du tar bort eller ändrar effekter till audit eller append (som till exempel kan lösa problem som är relaterade till taggar som saknas). [Lär dig mer om att hantera policys-effekter](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Nästa steg

- [Använda migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migreringen](alerts-prepare-migration.md)
