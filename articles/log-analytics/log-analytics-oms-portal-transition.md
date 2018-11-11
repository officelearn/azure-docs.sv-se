---
title: OMS-portalen som flyttar till Azure | Microsoft Docs
description: OMS-portalen är att sunsetted med alla funktioner som flyttas till Azure-portalen. Den här artikeln innehåller information om denna övergång.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8aec6ae3532acaaa5d039ee9db8745927c66957b
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288789"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portalen som flyttar till Azure

> [!NOTE]
> Den här artikeln gäller för både offentliga Azure-molnet och government-molnet utom om inget annat anges.

Azure-portalen är en hubb för alla Azure-tjänster och erbjuder en omfattande hanteringsupplevelse med funktioner som instrumentpaneler för att fästa resurser, intelligenta sökfunktioner för att hitta resurser och tagga för resurshantering. För att konsolidera och förenkla övervakning och hantering av arbetsflödet, börjar vi lägga till OMS portalfunktioner till Azure-portalen. Alla funktioner i OMS-portalen är nu en del av Azure-portalen. Några av de nya funktionerna, till exempel trafikanalys är i själva verket bara tillgängliga i Azure-portalen. Du kommer att kunna göra allt du gjorde i OMS-portalen med Azure-portalen och mycket mer. Om du inte redan gjort det, bör du börja använda Azure-portalen redan idag!

**OMS-portalen kommer officiellt dras tillbaka den 15 januari 2019.** Vi är glada över att flytta till Azure-portalen och räknar med övergången är enkelt att hantera. Men vi är medvetna om ändringarna är svåra och kan vara störande. Skicka frågor, feedback eller frågor till **LAUpgradeFeedback@microsoft.com**. Resten av den här artikeln går över viktiga scenarier och vägledning för den här ändringen.

## <a name="what-is-changing"></a>Vad ändras? 
Följande ändringar tillkännages med utfasningen av OMS-portalen. Var och en av dessa ändringar beskrivs mer ingående i avsnitten nedan.

- Du kan skapa en ny [arbetsytor endast](#new-workspaces) i Azure-portalen.
- Den nya upplevelsen för aviseringshantering [ersätter avisering hanteringslösningen](#changes-to-alerts).
- [Hantering av användare](#user-access-and-role-migration) görs nu i Azure-portalen med hjälp av rollbaserad åtkomstkontroll i Azure.
- Den [Application Insights-anslutningsprogram krävs inte längre](#application-insights-connector-and-solution) eftersom samma funktioner som aktiveras via över arbetsytor frågor.
- Den [OMS Mobile-appen](#oms-mobile-app) är inaktuell. 
- Den [NSG lösning ersätts](#azure-network-security-group-analytics) med förbättrade funktioner som är tillgängliga via Traffic Analytics-lösning.
- Nya anslutningar från System Center Operations Manager till Log Analytics kräver [uppdaterat hanteringspaket](#system-center-operations-manager).
- Se [migrera dina distributioner av OMS till Azure](../automation/migrate-oms-update-deployments.md) för information om ändringar [uppdateringshantering](../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>Vad gör jag nu?
De flesta funktioner kommer att fortsätta att fungera utan att utföra en migrering, behöver du utföra följande uppgifter:

- Du behöver [migrera din användarbehörigheter](#user-access-and-role-migration) till Azure-portalen.
- Se [migrera dina distributioner av OMS till Azure](../automation/migrate-oms-update-deployments.md) mer information om övergång lösningen för uppdateringshantering.

Referera till [vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics användare](../log-analytics/log-analytics-oms-portal-faq.md) information om hur du övergår till Azure-portalen. Skicka feedback, frågor eller funderingar till **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Åtkomst och rollen användarmigrering
Hantering av åtkomst i Azure portal är större och kraftfullare än åtkomsthantering i OMS-portalen. Se [hantera arbetsytor](log-analytics-manage-access.md#manage-accounts-and-users) för information om åtkomsthantering i Log Analytics.

> [!NOTE]
> Tidigare versioner av den här artikeln anges att behörigheterna som automatiskt skulle konverteras från OMS-portalen till Azure-portalen. Den här automatisk konvertering inte längre är avsiktlig och du måste utföra konverteringen själv.

Du kanske redan har lämplig åtkomst i Azure-portalen i vilket fall du inte behöver göra några ändringar. Det finns ett par olika fall där du inte kanske har lämplig åtkomst i vilket fall du administratören måste tilldela behörigheter.

- Du har behörigheter för skrivskyddad användare i OMS-portalen men inte i Azure-portalen. 
- Du har deltagarbehörighet i OMS-portalen men läsåtkomst i Azure-portalen.
 
I båda dessa fall måste administratören manuellt tilldela dig rätt roll i följande tabell. Vi rekommenderar att du tilldelar den här rollen på resursnivån grupp eller prenumeration.  Mer vägledning ges inom kort för båda dessa fall.

| OMS behörighet | Azure-roll |
|:---|:---|
| Skrivskyddad | Log Analytics Reader |
| Deltagare | Log Analytics Contributor |
| Administratör | Ägare | 
 

## <a name="new-workspaces"></a>Nya arbetsytor
Du är inte längre att kunna skapa nya arbetsytor med OMS-portalen. Följ riktlinjerna i [skapa en Log Analytics-arbetsyta i Azure-portalen](log-analytics-quick-create-workspace.md) att skapa en ny arbetsyta i Azure-portalen.

## <a name="changes-to-alerts"></a>Ändringar av aviseringar

### <a name="alert-extension"></a>Varning-tillägg  

> [!NOTE]
> Aviseringar har nu utökats helt i Azure-portalen för det offentliga molnet. Befintliga Varningsregler kan ses i OMS-portalen, men de kan bara hanteras i Azure-portalen. I oktober 2018 startar utökning av aviseringar i Azure-portalen för Azure government-molnet.

Aviseringar har [utökats till Azure-portalen](../monitoring-and-diagnostics/monitoring-alerts-extend.md). När processen är klar hanteringsåtgärder på aviseringar endast att vara tillgänglig i Azure portal. Befintliga aviseringar fortsätter att listas i OMS-portalen. Om du använder aviseringar programmässigt med hjälp av Log Analytics avisering REST API eller Log Analytics-avisering resursmall måste du använda åtgärdsgrupper istället för åtgärder i API-anrop, Azure Resource Manager-mallar och PowerShell-kommandon.

### <a name="alert-management-solution"></a>Lösning för aviseringshantering
I stället för den [lösning för aviseringshantering](log-analytics-solution-alert-management.md), du kan använda [Azure Monitor unified aviseringar gränssnittet](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att visualisera och hantera dina aviseringar. Den här nya upplevelsen aggregerar aviseringar från flera källor på Azure inklusive aviseringar från Log Analytics. Du kan se distributioner av dina aviseringar, dra nytta av automatisk gruppering av relaterade aviseringar via smart grupper och visa aviseringar över flera prenumerationer när de omfattande filtren har använts. Alla dessa funktioner är tillgängliga i förhandsversionen från och med 4 juni 2018. Lösning för aviseringshantering blir inte tillgänglig i Azure-portalen. 

Data som samlas in av lösning för aviseringshantering (poster med en typ av avisering) fortsätter att vara i Log Analytics som lösningen är installerad för arbetsytan. Från och med augusti 2018, kommer strömning av aviseringar från enhetlig aviseringar i arbetsytor att aktiveras, ersätter den här funktionen. Vissa ändringar av entitetsschemat förväntas och meddelas vid ett senare tillfälle.

## <a name="oms-mobile-app"></a>OMS-Mobilappen
OMS-mobilappen kommer att sunsetted tillsammans med OMS-portalen. I stället för OMS-mobilappen, för att komma åt information om din IT-infrastruktur, instrumentpaneler och sparade frågor du kan komma åt Azure-portalen direkt från din webbläsare i din mobila enhet. Om du vill få aviseringar, bör du konfigurera [Azure-åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) att ta emot meddelanden i form av SMS- eller en röstsamtal

## <a name="application-insights-connector-and-solution"></a>Application Insights-anslutningsprogram och lösning
[Application Insights-anslutningsprogram](log-analytics-app-insights-connector.md) gör det möjligt att ta med Application Insights-data i Log Analytics-arbetsytan. Den här datadeduplicering krävdes för att tillhandahålla synlighet mellan infrastruktur-och programdata.

Med hjälp av [mellan resurser frågor](log-analytics-cross-workspace-search.md), det finns inte längre behovet av att kopiera data. Därför måste gälla den befintliga Application Insights-lösningen upphör att. Från och oktober, kommer du inte att kunna länka ny Application Insights-resurser till Log Analytics-arbetsytor. Befintliga länkar och instrumentpaneler kommer fortsätta att fungera förrän den 15 januari 2019.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
Den [Azure Network Security Group Analytics-lösningen](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) kommer att ersättas med den nyligen lanserade [trafikanalys](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) som ger insyn i användar- och programaktiviteten i molnnätverk. Trafikanalys kan du granska din organisations nätverksaktivitet, säkra program och data, optimera prestanda för arbetsbelastningen och följ efterlevnad. 

Den här lösningen analyserar NSG-Flödesloggar loggar och ger insikter om följande.

- Trafiken flödar över ditt nätverk mellan Azure och Internet, regioner för offentliga moln, virtuella nätverk och undernät.
- Program och protokoll i nätverket, utan att behöva Sniffer-program eller dedikerade flow samling installationer.
- Toppkommunikatörer, trafikintensiva program, VM-konversationer i molnet, trafik hotspots.
- Källor och mål för trafik mellan virtuella nätverk, kommunikation mellan relationer mellan affärskritiska processer och program.
- Säkerhet, inklusive skadlig trafik, portar öppna till Internet, program eller virtuella datorer som försöker Internetåtkomst.
- Kapacitetsutnyttjande, vilket hjälper dig att eliminera problem med etablering eller underutnyttjande.

Du kan fortsätta att förlita dig på diagnostikinställningar att skicka NSG-loggar till Log Analytics så att dina befintliga sparade sökningar, aviseringar, instrumentpaneler fortsätter att fungera. Kunder som redan har installerat lösningen kan fortsätta att använda den tills vidare. Startar den 5 September, Network Security Group Analytics-lösning tas bort från marketplace och görs tillgängliga via communityn som en [Azure-Snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Om du har [ansluten Operations Manager-hanteringsgrupp till Log Analytics](log-analytics-om-agents.md), och sedan den fortsätter att fungera utan ändringar. Nya anslutningar men du måste följa riktlinjerna i [Microsoft System Center Operations Manager-hanteringspaket du konfigurerar Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Nästa steg
- Se [vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics användare](log-analytics-oms-portal-faq.md) anvisningar om hur du flyttar från OMS-portalen till Azure-portalen.
