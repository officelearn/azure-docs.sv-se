---
title: OMS-portal flyttar till Azure | Microsoft-dokument
description: OMS-portalen är sunsetted med alla funktioner som flyttar till Azure-portalen. Den här artikeln innehåller information om den här övergången.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659262"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portal flyttar till Azure

> [!NOTE]
> Den här artikeln gäller både Det offentliga Azure-molnet och myndighetsmolnet om inget annat anges.

**OMS-portalen för det offentliga Azure-molnet har officiellt dragits tillbaka. OMS-portalen för Azure US Government-molnet drogs officiellt tillbaka den 15 maj 2019.** Vi är glada över att kunna flytta till Azure-portalen och förväntar oss att övergången blir enkel. Men vi förstår att förändringar är svåra och kan vara störande. Resten av den här artikeln går igenom de viktigaste scenarierna och färdplanen för den här övergången.

Azure-portalen är navet för alla Azure-tjänster och erbjuder en omfattande hanteringsupplevelse med funktioner som instrumentpaneler för att fästa resurser, intelligent sökning efter att hitta resurser och tagga för resurshantering. För att konsolidera och effektivisera arbetsflödet för övervakning och hantering började vi lägga till OMS-portalfunktionerna i Azure-portalen. Alla funktioner i OMS-portalen är nu en del av Azure-portalen. Faktum är att vissa av de nya funktionerna, till exempel Traffic Analytics, endast är tillgängliga i Azure-portalen. Du kommer att kunna utföra allt du gjorde i OMS-portalen med Azure-portalen med mera. Om du inte redan har gjort det bör du börja använda Azure-portalen idag!

## <a name="what-is-changing"></a>Vad förändras? 
Följande ändringar tillkännages i och med utfasningen av OMS-portalen. Var och en av dessa ändringar beskrivs mer i detalj i avsnitten nedan.

- Du kan bara skapa nya [arbetsytor](#new-workspaces) i Azure-portalen.
- Den nya [varningshanteringsupplevelsen ersätter alerthanteringslösningen](#changes-to-alerts).
- [Hantering av användaråtkomst](#user-access-and-role-migration) görs nu i Azure-portalen med hjälp av Azure-rollbaserad åtkomstkontroll.
- [Application Insights Connector krävs inte längre](#application-insights-connector-and-solution) eftersom samma funktioner är aktiverade via frågor över flera arbetsytor.
- [OMS Mobile App](#oms-mobile-app) håller på att inaktuell. 
- [NSG-lösningen ersätts](#azure-network-security-group-analytics) med förbättrade funktioner som är tillgängliga via Traffic Analytics-lösning.
- Nya anslutningar från System Center Operations Manager till Log Analytics kräver [uppdaterade hanteringspaket](#system-center-operations-manager).
- Mer information om ändringar i [uppdateringshantering](../../automation/automation-update-management.md)finns i [Migrera dina OMS-uppdateringsdistributioner till Azure.](../../automation/migrate-oms-update-deployments.md)


## <a name="what-should-i-do-now"></a>Vad ska jag göra nu?
De flesta funktioner fortsätter att fungera utan att utföra någon migrering, men du måste utföra följande uppgifter:

- Du måste [migrera dina användarbehörigheter](#user-access-and-role-migration) till Azure-portalen.
- Mer information om hur du överför lösningshanteringslösningen finns i [Migrera dina OMS-uppdateringsdistributioner till Azure.](../../automation/migrate-oms-update-deployments.md)

Mer information om hur du går över till Azure-portalen [för Log Analytics-användare finns i Vanliga frågor för övergång från OMS-portalen till Azure-portalen för Log Analytics-användare.](oms-portal-faq.md) 

## <a name="user-access-and-role-migration"></a>Migrering av användares åtkomst och roll
Hantering av Azure Portal-åtkomst är rikare och kraftfullare än åtkomsthanteringen i OMS-portalen. Mer information om åtkomsthantering finns i [Designa arbetsytan för Azure Monitor Logs.](design-logs-deployment.md)

> [!NOTE]
> Tidigare versioner av den här artikeln uppgav att behörigheterna automatiskt skulle konverteras från OMS-portalen till Azure-portalen. Den här automatiska konverteringen är inte längre planerad och du måste utföra konverteringen själv.

Du kanske redan har lämplig åtkomst i Azure-portalen, i vilket fall du inte behöver göra några ändringar. Det finns ett par fall där du kanske inte har lämplig åtkomst i vilket fall administratören måste tilldela dig behörigheter.

- Du har ReadOnly-användarbehörigheter i OMS-portalen men inga behörigheter i Azure-portalen. 
- Du har deltagarbehörigheter i OMS-portalen men endast Reader-åtkomst i Azure-portalen.
 
I båda dessa fall måste administratören manuellt tilldela dig rätt roll från följande tabell. Vi rekommenderar att du tilldelar den här rollen på resursgrupps- eller prenumerationsnivå.  Mer föreskrivande vägledning kommer inom kort att ges för båda dessa fall.

| TILLSTÅND för OMS-portal | Azure-roll |
|:---|:---|
| ReadOnly | Log Analytics Reader |
| Deltagare | Log Analytics Contributor |
| Administratör | Ägare | 
 

## <a name="new-workspaces"></a>Nya arbetsytor
Du kan inte längre skapa nya arbetsytor med OMS-portalen. Följ anvisningarna i [Skapa en log analytics-arbetsyta i Azure-portalen](../learn/quick-create-workspace.md) för att skapa en ny arbetsyta i Azure-portalen.

## <a name="changes-to-alerts"></a>Ändringar av aviseringar

### <a name="alert-extension"></a>Varningstillägg  

Aviseringar har [utökats till Azure-portalen](alerts-extend.md) Befintliga aviseringar kommer att fortsätta att visas i OMS-portalen, men du kan bara hantera dem i Azure-portalen. Om du kommer åt aviseringar programmässigt med hjälp av LOG Analytics Alert REST API eller Log Analytics Alert Resource Template måste du använda åtgärdsgrupper i stället för åtgärder i dina API-anrop, Azure Resource Manager-mallar och PowerShell-kommandon.

### <a name="alert-management-solution"></a>Lösning för varningshantering
Som en ändring från ett tidigare meddelande fortsätter [aviseringshanteringslösningen](alert-management-solution.md) att vara tillgänglig och stöds fullt ut i Azure-portalen. Du kan fortsätta att installera lösningen från Azure Marketplace.

Medan alerthanteringslösningen fortsätter att vara tillgänglig rekommenderar vi att du använder [Azure Monitors enhetliga varningsgränssnitt](alerts-overview.md) för att visualisera och hantera alla aviseringar i Azure. Den här nya upplevelsen sammanställer inbyggda aviseringar från flera källor i Azure, inklusive loggaviseringar från Log Analytics. Om du använder Azure Monitors enhetliga varningsgränssnitt krävs endast varningshanteringslösningen för att aktivera integrering av aviseringar från System Center Operation Manager till Azure. I Azure Monitors enhetliga varningsgränssnitt kan du se distributioner av dina aviseringar, dra nytta av automatisk gruppering av relaterade aviseringar via smarta grupper och visa aviseringar över flera prenumerationer samtidigt som du använder avancerade filter. Framtida framsteg inom alert management kommer främst att vara tillgängliga från denna nya erfarenhet. 

De data som samlas in av varningshanteringslösningen (poster med en typ av avisering) fortsätter att finnas i Logganalys så länge lösningen är installerad för arbetsytan. 

## <a name="oms-mobile-app"></a>OMS mobilapp
OMS mobilapp kommer att placeras tillsammans med OMS-portalen. I stället för OMS-mobilappen, för att komma åt information om din IT-infrastruktur, instrumentpaneler och sparade frågor, kan du komma åt Azure-portalen direkt från din webbläsare i din mobila enhet. För att få aviseringar bör du konfigurera [Azure Action Groups](action-groups.md) för att ta emot meddelanden i form av SMS eller ett röstsamtal

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector och lösning
[Application Insights Connector](app-insights-connector.md) är ett sätt att inkludera Application Insights-data i en Log Analytics-arbetsyta. Denna dataduplicering krävdes för att aktivera synlighet över infrastruktur- och programdata. Med Application Insights utökade stöd för datalagring i mars, 2019 och möjligheten att utföra [resursövergripande frågor](../log-query/cross-workspace-query.md) förutom att kunna [visa flera Azure Monitor Application Insights-resurser](../log-query/unify-app-resource-data.md)behöver du inte duplicera data från dina Application Insights-resurser och skicka dem till Log Analytics. Dessutom skickar kopplingen en delmängd av programegenskaperna till Log Analytics, medan frågor om flera resurser ger dig ökad flexibilitet.  

Application Insights Connector har därför tagits bort från Azure Marketplace tillsammans med OMS-portalens utfasning den 30 mars 2019. Befintliga anslutningar kommer att fortsätta att fungera fram till den 30 juni 2019. Med OMS-portalens utfasning finns det inget sätt att konfigurera och ta bort befintliga anslutningar från portalen. Detta kommer att stödjas med rest-API:et som kommer att göras tillgängligt i januari 2019 och ett meddelande kommer att publiceras på [Azure-uppdateringar](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Analys av Azure Network Security Group
[Azure Network Security Group Analytics-lösningen](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) ersätts med den nyligen lanserade Traffic [Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) som ger insyn i användar- och programaktivitet i molnnätverk. Traffic Analytics hjälper dig att granska organisationens nätverksaktivitet, säkra program och data, optimera arbetsbelastningsprestanda och hålla dig kompatibel. 

Den här lösningen analyserar NSG Flow-loggar och ger insikter i följande.

- Trafikflöden över dina nätverk mellan Azure och Internet, offentliga molnregioner, virtuella nätverk och undernät.
- Program och protokoll i nätverket, utan behov av sniffers eller dedikerade flödesinsamlingsapparater.
- Top talare, pratsamma program, VM-konversationer i molnet, trafik hotspots.
- Källor och trafikdestinationer över VNETs, relationer mellan kritiska affärstjänster och applikationer.
- Säkerhet inklusive skadlig trafik, portar som är öppna för Internet, program eller virtuella datorer som försöker komma åt Internet.
- Kapacitetsutnyttjande, vilket hjälper dig att eliminera problem med över etablering eller underutnyttjande.

Du kan fortsätta att förlita dig på diagnostikinställningar för att skicka NSG-loggar till Log Analytics så att dina befintliga sparade sökningar, aviseringar, instrumentpaneler fortsätter att fungera. Kunder som redan har installerat lösningen kan fortsätta att använda den tills vidare. Från och med den 5 september tas Analytics-lösningen för Network Security Group bort från marknadsplatsen och göras tillgänglig via communityn som en [Azure QuickStart-mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Om du har [anslutit hanteringsgruppen för Operations Manager till Log Analytics](om-agents.md)fortsätter den att fungera utan ändringar. För nya anslutningar måste du dock följa anvisningarna i [Microsoft System Center Operations Manager Management Pack för att konfigurera Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Nästa steg
- Se [Vanliga frågor för övergång från OMS-portal till Azure-portal för Log Analytics-användare](oms-portal-faq.md) för vägledning om hur du flyttar från OMS-portalen till Azure-portalen.
