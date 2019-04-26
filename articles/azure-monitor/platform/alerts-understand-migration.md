---
title: Förstå hur frivillig Migreringsverktyget för aviseringar i Azure Monitor fungerar
description: Förstå hur aviseringar Migreringsverktyget fungerar och felsöka om du stöter på problem.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347608"
---
# <a name="understand-how-the-migration-tool-works"></a>Förstå hur Migreringsverktyget fungerar

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är dras tillbaka i juli 2019. Migreringsverktyget att utlösa migrering frivilligt är tillgänglig i Azure portal och inför för kunder som använder klassiska Varningsregler.

Den här artikeln vägleder dig genom hur verktyget frivillig migrering fungerar. Här beskrivs också reparation för några vanliga problem.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Vilka klassiska Varningsregler kan migreras?

Nästan alla klassiska Varningsregler kan migreras med hjälp av verktyget, finns men det vissa undantag. Följande Varningsregler inte migreras med hjälp av verktyget (eller vid automatisk migrering i juli 2019)

- Klassiska Varningsregler på VM-gästmått (både Windows och Linux). [Läs mer om hur du återskapar sådana aviseringsregler i nya måttaviseringar](#guest-metrics-on-virtual-machines)
- Klassiska Varningsregler för mått för klassisk lagring. [Läs mer om övervakning av din klassiska lagringskonton](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Klassiska Varningsregler på vissa mått för lagringskonto. [Informationen nedan](#storage-account-metrics)

Om din prenumeration har sådana klassiska regler, resten av reglerna kommer att migreras, men dessa regler måste migreras manuellt. Eftersom vi inte kan ge en automatisk migrering, fortsätter alla sådana befintliga klassiska måttaviseringar fungerar till juni 2020 ska ge tid att flytta över till nya aviseringar. Men kan inga nya klassiska aviseringar skapas efter juni 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Gästmått på virtuella datorer

Om du vill kunna skapa nya måttaviseringar på gästmått måste gästmått som ska skickas till arkivet för anpassade mått Azure Monitor. Följ anvisningarna nedan för att aktivera Azure Monitor-kanalmottagare i diagnostikinställningar.

- [Aktivera gästmått för Windows-datorer](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Aktivera gästmått för virtuella Linux-datorer](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

När stegen ovan är klar kan du skapa nya måttaviseringar på gästmått. När du har återskapat nya måttaviseringar kan klassiska aviseringar tas bort.

### <a name="storage-account-metrics"></a>Mått för lagringskonto

Alla klassiska aviseringar på storage-konton kan migreras utom de aviseringarna på följande mått:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Klassiska Varningsregler på procent mått måste migreras baserat på [mappningen mellan gamla och nya lagringsmått](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på rätt sätt eftersom det nya måttet som är tillgängliga är ett absolut.

Klassiska Varningsregler AnonymousThrottlingError och SASThrottlingError måste delas upp i två nya aviseringar som det är ingen kombinerade mått som fungerar på samma sätt. Tröskelvärden måste anpassas på rätt sätt.

## <a name="roll-out-phases"></a>Lansering faser

Migreringsverktyget är lanseras i faser för kunder som använder klassiska Varningsregler. **Prenumerationsägare** får ett e-postmeddelande när prenumerationen är redo att migreras med hjälp av verktyget.

> [!NOTE]
> Du kan se att de flesta av dina prenumerationer inte ännu redo att migrera som verktyget distribueras i olika faser i tidiga faser.

För närvarande en **delmängd** för prenumerationer, som **endast** har klassiska Varningsregler på följande resurs datatyper är markerade som klar för migrering. Stöd för fler resurstyper läggs i kommande faser.

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

Alla användare som har den inbyggda rollen av **övervakning deltagare** på prenumerations-nivå kommer att kunna utlösa migreringen. Användare med en anpassad roll med följande behörigheter kan även utlösa migreringen:

- * / läsa
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Vanliga problem och reparationer

När du [utlösa migreringen](alerts-using-migration-tool.md), vi använder e-postadresserna till att meddela dig om slutföra migreringen eller om det finns en åtgärd krävs från dig. I följande avsnitt beskrivs några vanliga problem och hur du kan åtgärda dem.

### <a name="validation-failed"></a>Verifiering misslyckades

Prenumerationen på grund av vissa ändringar i klassiska Varningsregler i din prenumeration kan inte migreras. Det här är ett tillfälligt problem. Du kan starta om migreringen när Migreringsstatus för flyttas tillbaka **klar för migrering** några dagar.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Principomfånget/lås som förhindrar oss från att migrera dina regler

Nya aviseringar för mått och nya åtgärdsgrupper kommer att skapas som en del av migreringen, och kommer att tas bort klassiska Varningsregler (när nya regler skapas). Det finns dock en princip eller ett omfång lås som förhindrar oss från att skapa resurser. Vissa eller alla regler gick inte att migrera beroende på låset princip eller ett omfång. Du kan lösa det här problemet genom att ta bort omfattningen/låsprincip tillfälligt och utlösa migreringen igen.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du Migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migrering](alerts-prepare-migration.md)
