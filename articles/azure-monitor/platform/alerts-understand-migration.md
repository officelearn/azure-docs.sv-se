---
title: Förstå hur verktyget frivillig migrering fungerar för Azure Monitor-aviseringar
description: Förstå hur Migreringsverktyget aviseringar fungerar och felsöka problem.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015597"
---
# <a name="understand-how-the-migration-tool-works"></a>Förstå hur Migreringsverktyget fungerar

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är att tas ur drift i September 2019 (var ursprungligen juli 2019). Migreringsverktyg finns i Azure portal för att kunder som använder klassiska Varningsregler och som vill aktivera migrering själva.

Den här artikeln förklarar hur du verktyget frivillig migrering. Här beskrivs också lösningar för några vanliga problem.

> [!NOTE]
> Fördröjning i lansering av migreringsverktyget slutdatum för klassiska aviseringar migrering har [utökats till den 31 augusti 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) ursprungligen presenterade efter den 30 juni 2019.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Vilka klassiska Varningsregler kan migreras?

Verktyget kan migrera nästan alla klassiska Varningsregler, finns men det vissa undantag. Följande Varningsregler migreras inte med hjälp av verktyget (eller vid automatisk migrering i September 2019):

- Klassiska Varningsregler på virtuella datorer gästmått (både Windows och Linux). Se den [vägledning för att återskapa sådana aviseringsregler i nya måttaviseringar](#guest-metrics-on-virtual-machines) senare i den här artikeln.
- Klassiska Varningsregler för mått för klassisk lagring. Se den [vägledning för övervakning av din klassiska lagringskonton](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Klassiska Varningsregler på vissa mått för lagringskonto. Se [information](#storage-account-metrics) senare i den här artikeln.

Om din prenumeration har sådana klassiska regler, måste du migrera dem manuellt. Eftersom vi inte kan ge en automatisk migrering, kommer alla befintliga, klassiska måttaviseringar olika typerna fortsätta att fungera fram till juni 2020. Det här tillägget ger dags att flytta till nya aviseringar. Inga nya klassiska aviseringar kan dock skapas efter augusti 2019.

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

Klassiska varningen regler på procent mått måste migreras [mappningen mellan gamla och nya lagringsmått](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på rätt sätt eftersom det nya måttet som är tillgängliga är ett absolut.

Klassiska Varningsregler på AnonymousThrottlingError och SASThrottlingError måste delas upp i två nya aviseringar eftersom det finns inga kombinerade mått som fungerar på samma sätt. Tröskelvärden måste anpassas på rätt sätt.

## <a name="rollout-phases"></a>Distributionsfaser

Migreringsverktyget är lanseras i faser för kunder som använder klassiska Varningsregler. Prenumeranter får ett e-postmeddelande när prenumerationen är redo att migreras med hjälp av verktyget.

> [!NOTE]
> Eftersom verktyget lanseras i faser, kan du se att de flesta av dina prenumerationer inte ännu redo att migreras under tidiga faser.

För närvarande har en delmängd av prenumerationer markerats som klar för migrering. Delmängden innehåller de prenumerationer som har klassiska Varningsregler endast på följande resurstyper. Stöd för fler resurstyper läggs i kommande faser.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.DataFactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.Logic/Workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.Network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Vem som kan utlösa migreringen?

Alla användare som har den inbyggda rollen som deltagare för övervakning på prenumerationsnivå kan utlösa migreringen. Användare som har en anpassad roll med följande behörigheter kan även utlösa migreringen:

- * / läsa
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Vanliga problem och lösningar

När du [utlösa migreringen](alerts-using-migration-tool.md), får du e-post till de adresser som du angav som meddelar att migreringen är klar eller om någon åtgärd krävs från dig. Det här avsnittet beskriver några vanliga problem och hur du hanterar dem.

### <a name="validation-failed"></a>Verifiering misslyckades

Prenumerationen på grund av vissa ändringar i klassiska Varningsregler i din prenumeration kan inte migreras. Det här problemet är tillfälligt. Du kan starta om migreringen när Migreringsstatus för flyttas tillbaka **klar för migrering** några dagar.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Principen eller ett omfång lås som förhindrar oss från att migrera dina regler

Nya aviseringar för mått och nya åtgärdsgrupper kommer att skapas som en del av migreringen, och sedan klassiska Varningsregler kommer att tas bort. Det finns dock en princip eller ett omfång lås som förhindrar oss från att skapa resurser. Vissa eller alla regler gick inte att migrera beroende på låset princip eller ett omfång. Du kan lösa problemet genom att ta bort lås för omfånget eller princip tillfälligt och utlösa migreringen igen.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du Migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migrering](alerts-prepare-migration.md)
