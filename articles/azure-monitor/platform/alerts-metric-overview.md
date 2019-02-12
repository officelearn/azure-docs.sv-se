---
title: Förstå hur arbetet aviseringar i mått i Azure Monitor.
description: Få en översikt över vad du kan göra med måttaviseringar och hur de fungerar i Azure Monitor.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 3c7feda32bf162499888720ce56edac55197abe4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005530"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Förstå hur arbetet aviseringar i mått i Azure Monitor

Måttaviseringar i Azure Monitor ovanpå flerdimensionella mått. De här måtten kan vara plattform mått [anpassade mått](../../azure-monitor/platform/metrics-custom-overview.md), [populära loggar från Azure Monitor konverteras till mått](../../azure-monitor/platform/alerts-metric-logs.md), standardmått i Application Insights. Måttaviseringar utvärderar med jämna mellanrum för att kontrollera om villkoren på en eller flera mått time series-är SANT och meddelar dig när utvärderingarna är uppfyllda. Måttaviseringar är tillståndskänsliga, det vill säga de endast skicka ut meddelanden när tillståndet ändras.

## <a name="how-do-metric-alerts-work"></a>Hur fungerar måttaviseringar?

Du kan definiera en måttaviseringsregel genom att ange en målresurs som ska övervakas, Måttnamn, typ av villkor (statisk eller dynamisk), och villkoret (en operator och ett tröskelvärde/känslighet) och en åtgärdsgrupp att utlösas när aviseringsregeln utlöses. Villkorstyper påverkar tröskelvärden bestäms. [Mer information om tröskelvärden för dynamiska villkor typ och känslighet alternativ](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Aviseringsregel med statiska Villkorstyp

Anta att du har skapat en enkelt statiskt tröskelvärde måttaviseringsregel på följande sätt:

- Målresurs (Azure-resursen du vill övervaka): myVM
- Mått: Procent CPU
- Typ av villkor: Statisk
- Tidsmängd (statistik som körs över råa måttvärden. Stöds tid aggregeringar är Min, Max, Avg, totalt): Medel
- Period (titt tillbaka fönstret över vilka mått värden kontrolleras): Under de senaste 5 minuterna
- Frequency (frekvens som kontrollerar metrisk varning om villkoren uppfylls): 1 min
- Operator: Större än
- Tröskelvärde: 70

Från den tidpunkt som regeln har skapats körs var 1 min övervakaren och tittar på måttvärden under de senaste 5 minuterna och kontrollerar om medelvärdet för de här värdena överskrider 70. Om villkoret är uppfyllt det vill säga, den genomsnittliga CPU procent under de senaste 5 minuterna överskrider 70, aviseringsregeln utlöses ett meddelande om aktiverad. Om du har konfigurerat ett e-postmeddelande eller en web hook-åtgärd i åtgärdsgruppen som associeras med varningsregeln, får du ett meddelande som är aktiverad på båda.

### <a name="alert-rule-with-dynamic-condition-type"></a>Aviseringsregel med dynamiska Villkorstyp

Anta att du har skapat en enkel dynamiska tröskelvärden måttaviseringsregel på följande sätt:

- Målresurs (Azure-resursen du vill övervaka): myVM
- Mått: Procent CPU
- Typ av villkor: Dynamisk
- Tidsmängd (statistik som körs över råa måttvärden. Stöds tid aggregeringar är Min, Max, Avg, totalt): Medel
- Period (titt tillbaka fönstret över vilka mått värden kontrolleras): Under de senaste 5 minuterna
- Frequency (frekvens som kontrollerar metrisk varning om villkoren uppfylls): 1 min
- Operator: Större än
- Känslighet: Medel
- Titt tillbaka perioder: 4
- Antal överträdelser: 4

När regeln har skapats kan den dynamiska tröskelvärden maskininlärningsalgoritmen ska hämta historiska data som är tillgänglig, beräkna tröskelvärde som passar bäst för metriska serien beteendemönster och kommer kontinuerligt Läs baserat på nya data att göra det tröskelvärdet mer exakta.

Från den tid som regeln skapades övervakaren körs var 1 min och tittar på måttvärden under de senaste 20 minuterna som grupperas i 5 minuter perioder, och kontrollerar om medelvärdet för tröskelperiodvärden i var och en av de 4 punkter överskrider tröskelvärdet förväntade. Om villkoret är uppfyllt det vill säga, den genomsnittliga procent CPU under de senaste 20 minuterna (fyra 5 minuter perioder) avvikit från fyra gånger är förväntat, aviseringsregeln utlöses ett meddelande om aktiverad. Om du har konfigurerat ett e-postmeddelande eller en web hook-åtgärd i åtgärdsgruppen som associeras med varningsregeln, får du ett meddelande som är aktiverad på båda.

### <a name="view-and-resolution-of-fired-alerts"></a>Visa och upplösning aktiverade aviseringar

Ovanstående exempel på aviseringsregler aktiveringen kan även visas i Azure-portalen i den **alla aviseringar** bladet.

Anta att användningen på ”myVM” fortsätter att tröskelvärdet i efterföljande kontroller, aviseringsregeln utlöses inte igen förrän villkor som har åtgärdats.

Om en stund om användningen på ”myVM” kommer tillbaka ned till normal går det vill säga under tröskeln. Varningsregeln övervakar villkoret för två gånger, skicka ut en löst avisering. Varningsregeln skickar ut ett meddelande som löst/inaktiveras när aviseringstillståndet inte uppfylls för tre punkter att minska bruset vid växlar villkor.

Som lösts meddelandet skickas via webhooks eller e-postmeddelandet, anges status för aviseringsinstansen (kallas övervakningstillstånd) i Azure-portalen också till löst.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Övervakning i stor skala med måttaviseringar i Azure Monitor

### <a name="using-dimensions"></a>Med hjälp av dimensioner

Måttaviseringar i Azure Monitor stöder även övervakning av flera kombinationer av dimensioner värden med en regel. Låt oss se varför du kanske använder flera dimensionskombinationer av med hjälp av ett exempel.

Anta att du har en apptjänstplan för din webbplats. Du vill övervaka CPU-användningen på flera instanser som kör appen/webbplatsen. Du kan göra det med en måttaviseringsregel enligt följande:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Typ av villkor: Statisk
- Dimensioner
  - Instans = InstanceName1 InstanceName2
- Tidsmängd: Medel
- Period: Under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Tröskelvärde: 70

Som innan den här regeln övervakar om den genomsnittliga CPU-användningen under de senaste 5 minuterna överskrider 70%. Du kan dock övervaka två instanser som kör din webbplats med samma regel. Varje instans kommer få övervakas individuellt och du får meddelanden individuellt.

Anta att du har en webbapp som ser enorma begäran och du behöver du lägga till fler instanser. Ovanstående övervakar fortfarande bara två instanser. Du kan dock skapa en regel på följande sätt:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Typ av villkor: Statisk
- Dimensioner
  - Instans = *
- Tidsmängd: Medel
- Period: Under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Tröskelvärde: 70

Den här regeln kommer automatiskt att övervaka alla värden för instans-dvs Du kan övervaka dina instanser när de uppstår utan att behöva ändra dina måttaviseringsregel igen.

När du övervakar flera dimensioner, skräddarsydda dynamiska tröskelvärden för aviseringar regel kan skapa tröskelvärden för hundratals mått serien i taget. Dynamiska tröskelvärden resulterar i färre Varningsregler att hantera och mycket tid som sparas på hanterings- och skapande av regler för aviseringar.

Anta att du har en webbapp med många instanser och du inte vet vad som är mest lämpligt tröskelvärde. Av ovanstående använder alltid tröskelvärdet på 70%. Du kan dock skapa en regel på följande sätt:

- Målresurs: myAppServicePlan
- Mått: Procent CPU
- Typ av villkor: Dynamisk
- Dimensioner
  - Instans = *
- Tidsmängd: Medel
- Period: Under de senaste 5 minuterna
- Frekvens: 1 min
- Operator: GreaterThan
- Känslighet: Medel
- Titt tillbaka perioder: 1
- Antal överträdelser: 1

Den här regeln övervakar om den genomsnittliga CPU-användningen under de senaste 5 minuterna överskrider förväntat beteende för varje instans. Samma regel som du kan övervaka instanser när de uppstår utan att behöva ändra dina måttaviseringsregel igen. Varje instans ska få ett tröskelvärde som passar mått serien beteendemönster och kontinuerligt ska ändras baserat på nya data att göra det mer exakta tröskelvärdet. Som innan varje instans som ska övervakas individuellt och du får meddelanden individuellt.

Öka titt bak perioder och antal överträdelser kan det också filtrera aviseringarna till bara aviseringen på din definition av en stor avvikelse. [Mer information om dynamiska tröskelvärden avancerade alternativ](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Övervakning av flera resurser med hjälp av måttaviseringar

När du har sett i föregående avsnitt, är det möjligt att ha en enda måttaviseringsregel som övervakar varje dimensionskombination för enskilda (dvs.) en metrisk tidsserie). Dock tidigare var du fortfarande begränsad till det utförs på en resurs i taget. Azure Monitor stöder även övervakning av flera resurser med en måttaviseringsregel. Den här funktionen är för närvarande förhandsversionen och stöds bara på virtuella datorer. En enda metrisk varning kan också övervaka resurser i en Azure-region.

Du kan ange omfånget för övervakning av en enda metrisk varning på något av tre sätt:

- som en lista över virtuella datorer i en Azure-region inom en prenumeration
- alla virtuella datorer (inom en Azure-region) i en eller flera resursgrupper i en prenumeration
- alla virtuella datorer (i en Azure-region) i en prenumeration

Skapa måttvarningsregler som övervakar flera resurser stöds inte för närvarande via Azure-portalen. Du kan skapa dessa regler via [Azure Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Du får enskilda meddelanden för varje virtuell dator.

## <a name="typical-latency"></a>Typisk svarstid

För måttaviseringar, vanligtvis meddelas du inom 5 minuter om du ställer in frekvensen varningsregeln ska vara 1 min. Du kan se en längre svarstid i fall av hård belastning för meddelandesystem.

## <a name="supported-resource-types-for-metric-alerts"></a>Resurstyper som stöds för måttaviseringar

Du hittar en fullständig lista över resurstyper som stöds i den här [artikeln](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Om du använder klassiska måttaviseringar idag och vill ha mer information om måttaviseringar stöder alla resurstyperna du använder, i följande tabell visas resursen typer som stöds av klassiska måttaviseringar och om de stöds av måttaviseringar idag eller inte.

|Resurstyp som stöds av klassiska måttaviseringar | Stöds av måttaviseringar |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Ja |
| Microsoft.Batch/batchAccounts| Ja|
|Microsoft.Cache/redis| Ja
|Microsoft.ClassicCompute/virtualMachines | Nej |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nej|
|Microsoft.CognitiveServices/accounts | Nej |
|Microsoft.Compute/virtualMachines | Ja|
|Microsoft.Compute/virtualMachineScaleSets| Ja|
|Microsoft.ClassicStorage/storageAccounts| Nej |
|Microsoft.DataFactory/datafactories | Ja|
|Microsoft.DBforMySQL/servers| Ja|
|Microsoft.DBforPostgreSQL/servers| Ja|
|Microsoft.Devices/IotHubs | Nej|
|Microsoft.DocumentDB/databaseAccounts| Nej|
|Microsoft.EventHub/namespaces | Ja|
|Microsoft.Logic/workflows | Ja|
|Microsoft.Network/loadBalancers |Ja|
|Microsoft.Network/publicIPAddresses| Ja|
|Microsoft.Network/applicationGateways| Ja|
|Microsoft.Network/expressRouteCircuits| Ja|
|Microsoft.Network/trafficManagerProfiles | Ja|
|Microsoft.Search/searchServices | Nej|
|Microsoft.ServiceBus/namespaces| Nej|
|Microsoft.Storage/storageAccounts | Ja|
|Microsoft.StreamAnalytics/streamingjobs| Ja|
|Microsoft.TimeSeriesInsights/environments | Ja|
|Microsoft. Web/servergrupper | Ja |
|Microsoft. -/ Webbplatser (exklusive funktioner) | Ja|
|Microsoft. Web/hostingEnvironments/multiRolePools | Nej|
|Microsoft. Web/hostingEnvironments/workerPools| Nej
|Microsoft.SQL/Servers | Nej|

## <a name="next-steps"></a>Nästa steg

- [Lär dig att skapa, visa och hantera aviseringar för mått i Azure](alerts-metric.md)
- [Lär dig hur du distribuerar måttaviseringar med hjälp av Azure Resource Manager-mallar](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Läs mer om åtgärdsgrupper](action-groups.md)
- [Mer information om dynamiska tröskelvärden Villkorstyp](alerts-dynamic-thresholds.md)
