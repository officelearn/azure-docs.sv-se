---
title: Förstå migreringsverktyg för Azure Monitor-aviseringar
description: Förstå hur migreringsverktyget för aviseringar fungerar och felsöka problem.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114313"
---
# <a name="understand-how-the-migration-tool-works"></a>Förstå hur migreringsverktyget fungerar

Som [tidigare meddelats](monitoring-classic-retirement.md)dras klassiska aviseringar i Azure Monitor tillbaka senast den 31 augusti 2019 (var ursprungligen den 30 juni 2019). Ett migreringsverktyg är tillgängligt i Azure-portalen för kunder som använder klassiska varningsregler och som vill utlösa migreringen själva.

I den här artikeln beskrivs hur det frivilliga migreringsverktyget fungerar. Den beskriver också åtgärder för några vanliga problem.

> [!NOTE]
> På grund av förseningar i utrullningen av migreringsverktyget har pensioneringsdatumet för klassisk [registreringsmigrering förlängts till den 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) från det ursprungligen meddelade datumet den 30 juni 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Klassiska aviseringsregler som inte kommer att migreras

> [!IMPORTANT]
> Aktivitetsloggaviseringar (inklusive hälsovarningar för tjänsten) och loggaviseringar påverkas inte av migreringen. Migreringen gäller endast klassiska varningsregler som beskrivs [här](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Även om verktyget kan migrera nästan alla [klassiska varningsregler](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)finns det några undantag. Följande varningsregler migreras inte med hjälp av verktyget (eller under den automatiska migreringen från september 2019):

- Klassiska varningsregler för gästmått för virtuella datorer (både Windows och Linux). Se [vägledningen för att återskapa sådana varningsregler i nya måttaviseringar](#guest-metrics-on-virtual-machines) senare i den här artikeln.
- Klassiska varningsregler för klassiska lagringsmått. Se [vägledningen för övervakning av dina klassiska lagringskonton](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klassiska varningsregler för vissa lagringskontomått. Se [mer information](#storage-account-metrics) senare i den här artikeln.
- Klassiska varningsregler för vissa Cosmos DB-mått. Se [mer information](#cosmos-db-metrics) senare i den här artikeln.
- Klassiska varningsregler för alla klassiska mätvärden för virtuella datorer och molntjänster (Microsoft.ClassicCompute/virtualMachines och Microsoft.ClassicCompute/domainNames/slots/roles). Se [mer information](#classic-compute-metrics) senare i den här artikeln.

Om din prenumeration har sådana klassiska regler måste du migrera dem manuellt. Eftersom vi inte kan tillhandahålla en automatisk migrering fortsätter alla befintliga, klassiska måttaviseringar av dessa typer att fungera fram till juni 2020. Det här tillägget ger dig tid att gå över till nya aviseringar. Du kan också fortsätta att skapa nya klassiska aviseringar på ovanstående undantag till juni 2020. Men för allt annat kan inga nya klassiska varningar skapas efter augusti 2019.

> [!NOTE]
> Förutom ovanstående undantag, om dina klassiska varningsregler är ogiltiga, dvs de är på [inaktuella mått](#classic-alert-rules-on-deprecated-metrics) eller resurser som har tagits bort, kommer de inte att migreras och kommer inte att vara tillgängliga när tjänsten har dragits tillbaka.

### <a name="guest-metrics-on-virtual-machines"></a>Gästmått på virtuella datorer

Innan du kan skapa nya måttaviseringar för gästmått måste gästmåtten skickas till Azure Monitor anpassade mått store. Följ dessa instruktioner för att aktivera Azure Monitor-diskhon i diagnostikinställningar:

- [Aktivera gästmått för virtuella Windows-datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Aktivera gästmått för virtuella Linux-datorer](collect-custom-metrics-linux-telegraf.md)

När de här stegen är klara kan du skapa nya måttaviseringar för gästmått. Och när du har skapat nya måttaviseringar kan du ta bort klassiska aviseringar.

### <a name="storage-account-metrics"></a>Mått för lagringskonto

Alla klassiska aviseringar på lagringskonton kan migreras förutom aviseringar om dessa mått:

- ProcentAuthorizationError
- ProcentKlientOtherError
- ProcentNätverkAreror
- ProcentServerOtherError
- Procentsug
- ProcentBegränsningserror
- ProcentTimeoutError
- AnonymThrottlingError
- SASThrottlingError
- ThrottlingError

Klassiska varningsregler för procentmått måste migreras baserat på [mappningen mellan gamla och nya lagringsmått](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på rätt sätt eftersom det nya tillgängliga måttet är absolut.

Klassiska aviseringsregler för AnonymousThrottlingError, SASThrottlingError och ThrottlingError måste delas upp i två nya aviseringar eftersom det inte finns något kombinerat mått som ger samma funktioner. Trösklarna måste anpassas på lämpligt sätt.

### <a name="cosmos-db-metrics"></a>Cosmos DB-mått

Alla klassiska aviseringar i Cosmos DB-mått kan migreras utom aviseringar om dessa mått:

- Genomsnittlig begäranden per sekund
- Konsekvensnivå
- Http 2xx (på andra sätt)
- Http 3xx (på)
- Http 400
- Http 401
- Internt serverfel
- Max RUPM förbrukas per minut
- Max RU per sekund
- Misslyckade begäranden om mongoantal
- Mongo Ta bort misslyckade begäranden
- Misslyckade begäranden för Mongo-infogning
- Mongo andra misslyckade begäranden
- Mongo annan begäran avgift
- Mongo annan begäran ränta
- Misslyckade begäranden i Mongo-frågan
- Misslyckade begäranden för Mongo-uppdatering
- Observerad lässvarstid
- Observerad skrivsvarstid
- Tjänstens tillgänglighet
- Lagringskapacitet
- Begränsade begäranden
- Totalt antal förfrågningar

Genomsnittlig begäranden per sekund, konsekvensnivå, max RUPM förbrukas per minut, max ru: er per sekund, observerade lässvarstid, observerad skrivfördröjning, lagringskapacitet är för närvarande inte tillgängliga i det [nya systemet](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Aviseringar om begärandens mått som Http 2xx, Http 3xx, Http 400, Http 401, Internt serverfel, tjänsttillgänglighet, begränsade begäranden och totala begäranden migreras inte eftersom hur begäranden räknas skiljer sig mellan klassiska mått och nya mått. Aviseringar om dessa måste återskapas manuellt med intrösklar justerade.

Aviseringar på mått för misslyckade begäranden i Mongo måste delas upp i flera aviseringar eftersom det inte finns något kombinerat mått som ger samma funktioner. Trösklarna måste anpassas på lämpligt sätt.

### <a name="classic-compute-metrics"></a>Klassiska beräkningsmått

Alla aviseringar om klassiska beräkningsmått migreras inte med migreringsverktyget eftersom klassiska beräkningsresurser ännu inte stöds med nya aviseringar. Stöd för nya aviseringar om dessa resurstyper kommer att läggas till i framtiden. När det är tillgängligt måste kunderna återskapa nya likvärdiga varningsregler baserat på deras klassiska varningsregler före juni 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassiska varningsregler för inaktuella mått

Det här är klassiska varningsregler för mått som tidigare stöddes men som så småningom var inaktuella. En liten andel av kunden kan ha ogiltiga klassiska varningsregler för sådana mått. Eftersom dessa varningsregler är ogiltiga migreras de inte.

| Resurstyp| Inaktuella mått |
|-------------|----------------- |
| Microsoft.DBforMySQL/servrar | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servrar | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAdresser | defaultddostriggerrate |
| Microsoft.SQL/servrar/databaser | service_level_objective, storage_limit, storage_used, strypning, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingMiljöer/multirolepools | genomsnittligt minne |
| Microsoft.Web/hostingMiljöer/arbetspooler | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hur motsvarande nya varningsregler och åtgärdsgrupper skapas

Migreringsverktyget konverterar dina klassiska varningsregler till motsvarande nya varningsregler och åtgärdsgrupper. För de flesta klassiska aviseringsregler finns motsvarande nya aviseringsregler `windowSize` `aggregationType`på samma mått med samma egenskaper som och . Det finns dock några klassiska varningsregler på mått som har ett annat, motsvarande mått i det nya systemet. Följande principer gäller för migrering av klassiska aviseringar om inte anges i avsnittet nedan:

- **Frekvens**: Definierar hur ofta en klassisk eller ny varningsregel söker efter villkoret. De `frequency` klassiska varningsreglerna i klassiska varslar inte av användaren och var alltid 5 minuter för alla resurstyper utom Application Insights-komponenter som den var 1 min för. Så frekvensen av motsvarande regler är också inställd på 5 min respektive 1 min.
- **Aggregeringstyp**: Definierar hur måttet aggregeras över intressefönstret. Det `aggregationType` är också samma sak mellan klassiska aviseringar och nya aviseringar för de flesta mått. I vissa fall, eftersom måttet skiljer sig mellan klassiska `aggregationType` aviseringar `primary Aggregation Type` och nya aviseringar, används motsvarande eller definierad för måttet.
- **Enheter**: Egenskap för det mått som aviseringen skapas på. Vissa motsvarande mått har olika enheter. Tröskelvärdet justeras på lämpligt sätt efter behov. Om det ursprungliga måttet till exempel har sekunder som enheter men motsvarande nytt mått har milliSekunder som enheter, multipliceras det ursprungliga tröskelvärdet med 1000 för att säkerställa samma beteende.
- **Fönsterstorlek**: Definierar fönstret över vilka måttdata som aggregeras för att jämföras med tröskelvärdet. För `windowSize` standardvärden som 5mins, 15mins, 30mins, 1hour, 3hours, 6 timmar, 12 timmar, 1 dag, det finns ingen förändring gjord för motsvarande ny varningsregel. För andra värden väljs det närmaste `windowSize` som ska användas. För de flesta kunder, Det finns ingen inverkan med denna förändring. För en liten andel av kunderna kan det finnas ett behov av att justera tröskeln för att få exakt samma beteende.

I följande avsnitt beskriver vi de mått som har ett annat, motsvarande mått i det nya systemet. Alla mått som förblir desamma för klassiska och nya varningsregler visas inte. Du hittar en lista över mått som stöds i det nya systemet [här](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageRedovisning/-tjänster

För lagringskontotjänster som blob, tabell, fil och kö mappas följande mått till motsvarande mått som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transaktionsmått med dimensionerna "ResponseType"="AuthorizationError" och "Authentication" = "Anonym"| |
| AnonymKlientOtherError | Transaktionsmått med dimensionerna "ResponseType"="ClientOtherError" och "Authentication" = "Anonym" | |
| AnonymClientTimeOutError| Transaktionsmått med dimensionerna "ResponseType"="ClientTimeOutError" och "Autentisering" = "Anonym" | |
| AnonymousNetworkError | Transaktionsmått med dimensionerna "ResponseType"="NetworkError" och "Authentication" = "Anonym" | |
| AnonymServerOtherError | Transaktionsmått med dimensionerna "ResponseType"="ServerOtherError" och "Autentisering" = "Anonym" | |
| AnonymServerTimeOutError | Mått på transaktioner med dimensionerna "ResponseType"="ServerTimeOutError" och "Autentisering" = "Anonym" | |
| AnonymSuccess | Transaktionsmått med dimensionerna "ResponseType"="Success" och "Autentisering" = "Anonym" | |
| AuthorizationError | Mått på transaktioner med dimensionerna "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | FramgångE2ELatency | |
| AverageServerLatency | Lycka Till Svar | |
| Kapacitet | BlobCapacity | Använd `aggregationType` "medelvärde" i stället för "sista". Måttet gäller endast Blob-tjänster |
| ClientOtherError | Transaktionsmått med dimensionerna "ResponseType"="ClientOtherError"  | |
| KlientTimeoutError | Mått på transaktioner med dimensionerna "ResponseType"="ClientTimeOutError" | |
| ContainerCount (ContainerCount) | ContainerCount (ContainerCount) | Använd `aggregationType` "medelvärde" i stället för "sista". Måttet gäller endast Blob-tjänster |
| NetworkError | Mått på transaktioner med dimensionerna "ResponseType"="NetworkError" | |
| ObjectCount (ObjektCount) | BlobCount (BlobCount)| Använd `aggregationType` "medelvärde" i stället för "sista". Måttet gäller endast Blob-tjänster |
| SASAuthorizationError | Transaktionsmått med dimensionerna "ResponseType"="AuthorizationError" och "Authentication" = "SAS" | |
| SASClientOtherError | Transaktionsmått med dimensionerna "ResponseType"="ClientOtherError" och "Authentication" = "SAS" | |
| SASClientTimeOutError | Mått på transaktioner med dimensionerna "ResponseType"="ClientTimeOutError" och "Authentication" = "SAS" | |
| SASNetworkError | Transaktionsmått med dimensionerna "ResponseType"="NetworkError" och "Authentication" = "SAS" | |
| SASServerOtherError | Transaktionsmått med dimensionerna "ResponseType"="ServerOtherError" och "Autentisering" = "SAS" | |
| SASServerTimeOutError | Mått på transaktioner med dimensionerna "ResponseType"="ServerTimeOutError" och "Autentisering" = "SAS" | |
| SASSuccess | Transaktionsmått med dimensionerna "ResponseType"="Success" och "Authentication" = "SAS" | |
| ServerOtherError | Mått på transaktioner med dimensionerna "ResponseType"="ServerOtherError" | |
| ServerTimeOutError | Mått på transaktioner med dimensionerna "ResponseType"="ServerTimeOutError"  | |
| Lyckades | Transaktionsmått med dimensionerna "ResponseType"="Lyckades" | |
| TotaltFakturerbara Begärande| Transaktioner | |
| TotalEgress | Utgående | |
| TotalIngress | Ingress | |
| TotalRequests | Transaktioner | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

För Application Insights är motsvarande mått som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | tillgänglighetResultat/tillgänglighetPercentage|   |
| availability.durationMetric.value | tillgänglighetResultat/varaktighet| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| basicExceptionBrowser.count | undantag/webbläsare|  Använd `aggregationType` "count" i stället för "summa". |
| basicExceptionServer.count | undantag/server| Använd `aggregationType` "count" i stället för "summa".  |
| clientPerformance.clientProcess.value | webbläsareTimings/bearbetningUnder behandling| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| clientPerformance.networkConnection.värde | webbläsareTimings/nätverkUnderydning|  Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder. |
| clientPerformance.receiveRequest.value | webbläsareTimings/mottagningUnder| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| clientPerformance.sendRequest.value | webbläsareTimings/sendDuration| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| clientPerformance.total.value | webbläsareTimings/totalUnderation| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| performanceCounter.available_bytes.värde | performanceCounters/memoryTillgängligaBytes|   |
| performanceCounter.io_data_bytes_per_sec.värde | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.värde | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.värde | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.värde | performanceCounters/processCpuPercentage| Tröskelvärdet måste ändras på lämpligt sätt eftersom det ursprungliga måttet var i alla kärnor och nya mått normaliseras till en kärna. Migreringsverktyget ändrar inte tröskelvärden.  |
| performanceCounter.percentage_processor_total.värde | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.värde | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.värde | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.värde | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.värde | performanceCounters/requestsPerSecond|   |
| request.duration | begäranden/varaktighet| Multiplicera den ursprungliga tröskeln med 1000 eftersom enheter för klassiskt mått är på några sekunder och för nya är i milliSekunder.  |
| request.rate (begär.ränta) | förfrågningar/ränta|   |
| requestFailed.count | begäranden/misslyckades| Använd `aggregationType` "count" i stället för "summa".   |
| visa.antal | pageVisningar/antal| Använd `aggregationType` "count" i stället för "summa".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databasKonton

För Cosmos DB är motsvarande mått som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| TillgängligtStora     |TillgängligtStora|   |
| Datastorlek | DataUpparage| |
| Antal dokument | DokumentCount||
| Indexstorlek | IndexUsage||
| Kostnad för begäran om mongoräkning| MongoRequestCharge med dimensionen "CommandName" = "count"||
| Begäransfrekvens för Mongo-antal | MongoRequestsCount med dimensionen "CommandName" = "count"||
| Betalning av begärandeavgift för Mongo | MongoRequestCharge med dimensionen "CommandName" = "delete"||
| Mångås ta bort begäranden | MongoRequestsCount med dimensionen "CommandName" = "delete"||
| Kostnad för begäran om mongo-infogning | MongoRequestCharge med dimensionen "CommandName" = "infoga"||
| Begärandehastighet för Mongo-infogning | MongoRequestsCount med dimensionen "CommandName" = "infoga"||
| Kostnad för begäran om mongofrågebegäran | MongoRequestCharge med dimensionen "CommandName" = "hitta"||
| Mångo-frågebegäranden | MongoRequestsCount med dimensionen "CommandName" = "hitta"||
| Kostnad för begäran om mongo-uppdatering | MongoRequestCharge med dimensionen "CommandName" = "uppdatering"||
| Tjänsten är inte tillgänglig| ServiceTillgänglighet||
| TotaltRequestUnits | TotaltRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Hur motsvarande åtgärdsgrupper skapas

Klassiska varningsregler hade e-post, webhook, logikapp och runbook-åtgärder knutna till själva varningsregeln. Nya varningsregler använder åtgärdsgrupper som kan återanvändas över flera varningsregler. Migreringsverktyget skapar en enda åtgärdsgrupp för samma åtgärder oavsett hur många varningsregler som använder åtgärden. Åtgärdsgrupper som skapas av migreringsverktyget använder namnformatet "Migrated_AG*".

> [!NOTE]
> Klassiska aviseringar skickade lokaliserade e-postmeddelanden baserat på lokalen för klassisk administratör när den används för att meddela klassiska administratörsroller. Nya varningsmeddelanden skickas via åtgärdsgrupper och är endast på engelska.

## <a name="rollout-phases"></a>Distributionsfaser

Migreringsverktyget lanseras i faser till kunder som använder klassiska varningsregler. Prenumerationsägare får ett e-postmeddelande när prenumerationen är klar att migreras med hjälp av verktyget.

> [!NOTE]
> Eftersom verktyget distribueras i faser kan du se att vissa av dina prenumerationer ännu inte är redo att migreras under de tidiga faserna.

De flesta prenumerationer är för närvarande markerade som klara för migreringen. Endast prenumerationer som har klassiska aviseringar om följande resurstyper är fortfarande inte redo för migrering.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Vem kan utlösa migreringen?

Alla användare som har den inbyggda rollen som övervakningsbidragsgivare på prenumerationsnivå kan utlösa migreringen. Användare som har en anpassad roll med följande behörigheter kan också utlösa migreringen:

- */läs
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Förutom att ha ovanstående behörigheter bör din prenumeration dessutom registreras hos Microsoft.AlertsManagement-resursprovidern. Detta krävs för att migrera felavvikelseaviseringar på Application Insights. 

## <a name="common-problems-and-remedies"></a>Vanliga problem och rättsmedel

När du [har utlöst migreringen](alerts-using-migration-tool.md)får du e-post på de adresser du angav för att meddela dig att migreringen är klar eller om någon åtgärd behövs från dig. I det här avsnittet beskrivs några vanliga problem och hur du hanterar dem.

### <a name="validation-failed"></a>Valideringen misslyckades

På grund av några nya ändringar av klassiska varningsregler i prenumerationen kan prenumerationen inte migreras. Detta problem är tillfälligt. Du kan starta om migreringen när migreringsstatusen flyttas tillbaka **Klar för migrering** om några dagar.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Scope lås hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya måttaviseringar och nya åtgärdsgrupper och sedan tas klassiska varningsregler bort. Ett scopelås kan dock hindra oss från att skapa eller ta bort resurser. Beroende på scopelåset gick det inte att migrera vissa eller alla regler. Du kan lösa problemet genom att ta bort scopelåset för prenumerationen, resursgruppen eller resursen, som visas i [migreringsverktyget,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)och utlösa migreringen igen. Scope-låset kan inte inaktiveras och måste tas bort under migreringens varaktighet. [Läs mer om hur du hanterar scopelås](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Policy med "Neka"-effekt som hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya måttaviseringar och nya åtgärdsgrupper och sedan tas klassiska varningsregler bort. En politik kan dock hindra oss från att skapa resurser. Beroende på principen kan vissa eller alla regler inte migreras. De principer som blockerar processen visas i [migreringsverktyget](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Lös problemet med antingen:

- Exklusive prenumerationer eller resursgrupper under hela migreringen från principtilldelningen. [Läs mer om hur du hanterar undantagsomfattningsomfattning av principer](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Ta bort eller ändra effekten till "revision" eller "lägg till" (som till exempel kan lösa problem som rör saknade taggar). [Läs mer om hur du hanterar principer](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Nästa steg

- [Använda migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migreringen](alerts-prepare-migration.md)
