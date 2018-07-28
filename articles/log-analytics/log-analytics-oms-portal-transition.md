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
ms.date: 07/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 1a8ccc818cafac4867cb533c83f297af61a21836
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309110"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portalen som flyttar till Azure
En typ av feedback hört upprepade gånger från Log Analytics-kunder är behovet av en enkel användarupplevelse att övervaka och hantera både lokala och Azure-arbetsbelastningar. Du vet förmodligen Azure-portalen är en hubb för alla Azure-tjänster och erbjuder en omfattande hanteringsupplevelse med funktioner som instrumentpaneler för att fästa resurser, intelligenta sökfunktioner för att hitta resurser och tagga för resurshantering. För att konsolidera och förenkla övervakning och hantering av arbetsflödet, börjar vi lägga till OMS portalfunktioner till Azure-portalen. Vi har glädjen att meddela de flesta av funktionerna i OMS-portalen är nu en del av Azure-portalen. Några av de nya funktionerna, till exempel trafikanalys är i själva verket bara tillgängliga i Azure-portalen. Det finns bara ett par luckor återstående inklusive några lösningar som är fortfarande i processen för att flyttas till Azure-portalen. Om du inte använder dessa funktioner, kommer du att kunna göra allt du gjorde i OMS-portalen med Azure-portalen och mycket mer. Om du inte redan gjort det, rekommenderar vi att du börjar använda Azure-portalen redan idag! 

Vi räknar med att Stäng ner de återstående glapp mellan de två portalerna augusti 2018. Vi kommer baserat på feedback från kunder, för att informera tidslinjen för sunsetting OMS-portalen. Vi är glada över att flytta till Azure-portalen och räknar med övergången är enkelt att hantera. Men vi är medvetna om ändringarna är svåra och kan vara störande. Skicka frågor, feedback eller frågor till **LAUpgradeFeedback@microsoft.com**. Resten av den här artikeln går över viktiga scenarier och de aktuella luckorna översikt för den här ändringen. 

## <a name="progress"></a>Förlopp
Följande är uppdateringar som har utförts sedan tidigare versioner av den här artikeln.

### <a name="july-27"></a>27 juli

- [DNS-analys](log-analytics-dns.md) är nu helt funktionella i Azure-portalen.
- [Hantering av uppdateringar](../automation/automation-update-management.md) har uppdaterats så att den fungerar i Azure-portalen. Se [migrera dina distributioner av OMS till Azure](../automation/migrate-oms-update-deployments.md) mer information.
- [Aviseringar](#changes-to-alerts) har nu fullständigt har utökats till Azure-portalen.
- [Anpassade loggar Förhandsgranska funktionen](log-analytics-data-sources-custom-logs.md) nu aktiveras automatiskt för alla arbetsytor.

## <a name="what-will-change"></a>Vad kommer att ändras? 
Följande ändringar tillkännages med utfasningen av OMS-portalen. Var och en av dessa ändringar beskrivs mer ingående i avsnitten nedan.

- Du kommer att kunna skapa nya arbetsytor endast i Azure-portalen.
- Den nya upplevelsen för aviseringshantering ersätts lösning för aviseringshantering.
- Hantering av användare kommer att göras i Azure-portalen med hjälp av rollbaserad åtkomstkontroll i Azure.
- Application Insights-anslutningsprogram krävs inte längre eftersom samma funktioner som kan aktiveras via över arbetsytor frågor.
- OMS Mobile-appen upphör att gälla. 
- NSG-lösningen ersätts med utökad funktionalitet som är tillgängliga via Traffic Analytics-lösning.
- Nya anslutningar från System Center Operations Manager till Log Analytics kräver uppdaterade management packs.


## <a name="current-known-gaps"></a>Aktuella kända luckor 
Det finns vissa funktioner luckor som kräver att du fortfarande använda OMS-portalen. Dessa mellanrum tas och det här dokumentet kommer att uppdateras på rätt sätt. Du bör också läsa [Azure uppdaterar](https://azure.microsoft.com/updates/?product=log-analytics) för pågående meddelanden om tillägg och ändringar.

- Följande lösningar är ännu inte fungerar i Azure-portalen. Du bör fortsätta att använda dessa lösningar i den klassiska portalen förrän de har uppdaterats.

    - Windows Analytics-lösningar ([uppgraderingsberedskap](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [enhetens hälsotillstånd](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), och [Uppdateringsefterlevnad](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Om du vill få åtkomst till Log Analytics-resurs i Azure måste användaren beviljas åtkomst via [Azure rollbaserad åtkomst](#user-access-and-role-migration).


## <a name="what-should-i-do-now"></a>Vad gör jag nu?  
Du bör använda [vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics användare](../log-analytics/log-analytics-oms-portal-faq.md) information om hur du övergår till Azure-portalen. Om den [luckor som beskrivs ovan](#current-known-gaps) gäller inte för din miljö, och sedan bör du starta med hjälp av Azure-portalen som din primära upplevelse. Skicka feedback, frågor eller funderingar till LAUpgradeFeedback@microsoft.com.

## <a name="new-workspaces"></a>Nya arbetsytor
Startar den 29 juli, kommer du inte längre att kunna skapa nya arbetsytor med OMS-portalen. Följ riktlinjerna i [skapa en Log Analytics-arbetsyta i Azure-portalen](log-analytics-quick-create-workspace.md) att skapa en ny arbetsyta i Azure-portalen.

## <a name="changes-to-alerts"></a>Ändringar av aviseringar 

### <a name="alert-extension"></a>Varning-tillägg  

> [!NOTE]
> Aviseringar har nu utökats fullständigt till Azure-portalen. Befintliga Varningsregler kan ses i OMS-portalen, men de kan bara hanteras i Azure-portalen.

Aviseringar som håller på att [utökats till Azure-portalen](../monitoring-and-diagnostics/monitoring-alerts-extend.md). När processen är klar hanteringsåtgärder på aviseringar endast att vara tillgänglig i Azure portal. Befintliga aviseringar fortsätter att listas i OMS-portalen. Om du använder aviseringar programmässigt med hjälp av Log Analytics avisering REST API eller Log Analytics-avisering resursmall måste du använda åtgärdsgrupper istället för åtgärder i API-anrop, Azure Resource Manager-mallar och PowerShell-kommandon.

### <a name="alert-management-solution"></a>Lösning för aviseringshantering
I stället för den [lösning för aviseringshantering](log-analytics-solution-alert-management.md), du kan använda [Azure Monitor unified aviseringar gränssnittet](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att visualisera och hantera dina aviseringar. Den här nya upplevelsen aggregerar aviseringar från flera källor på Azure inklusive aviseringar från Log Analytics. Du kan se distributioner av dina aviseringar, dra nytta av automatisk gruppering av relaterade aviseringar via smart grupper och visa aviseringar över flera prenumerationer när de omfattande filtren har använts. Alla dessa funktioner är tillgängliga i förhandsversionen från och med 4 juni 2018. Lösning för aviseringshantering blir inte tillgänglig i Azure-portalen. 

Data som samlas in av lösning för aviseringshantering (poster med en typ av avisering) fortsätter att vara i Log Analytics som lösningen är installerad för arbetsytan. Från och med augusti 2018, kommer strömning av aviseringar från enhetlig aviseringar i arbetsytor att aktiveras, ersätter den här funktionen. Vissa ändringar av entitetsschemat förväntas och meddelas vid ett senare tillfälle.

## <a name="user-access-and-role-migration"></a>Åtkomst och rollen användarmigrering
Hantering av åtkomst i Azure portal är större och kraftfullare än åtkomsthantering i OMS-portalen, men det krävs vissa konverteringar. Se [hantera arbetsytor](log-analytics-manage-access.md#manage-accounts-and-users) för information om åtkomsthantering i Log Analytics.

Startar 30 juli automatisk konvertering av behörighet för åtkomstkontroll från OMS startar-portalen för att Azure-portalen behörigheter. När konverteringen har slutförts, dirigerar OMS-portalen användaren hanteringsavsnittet användare till åtkomstkontroll (IAM) i Azure. 

Under omvandlingen systemet kontrollerar varje användare eller säkerhetsgrupp som har behörigheter i OMS-portalen och ta reda på om den har samma nivå eller behörigheter i Azure. Om behörigheter saknas tilldelar den följande roller för den relevanta arbetsytor och lösningar.

| OMS behörighet | Azure-roll |
|:---|:---|
| Skrivskyddad | Log Analytics Reader |
| Deltagare | Log Analytics Contributor |
| Administratör | Ägare |

För att säkerställa att inga onödigt generösa behörigheter tilldelas till användare, tilldelas inte automatiskt dessa behörigheter på resursgruppsnivå. Därför kan administratörer för arbetsytan måste manuellt tilldela själva _ägare_ eller _deltagare_ roller resource group eller på prenumerationsnivån att utföra följande åtgärder.

- Lägg till eller ta bort lösningar
- Definiera nya anpassade vyer
- Hantera aviseringar 

I vissa fall kan automatisk konvertering kan inte använda behörigheten och uppmanar administratören tilldela behörigheter manuellt.

## <a name="oms-mobile-app"></a>OMS-Mobilappen
OMS-mobilappen kommer att sunsetted tillsammans med OMS-portalen. I stället för OMS-mobilappen, för att komma åt information om din IT-infrastruktur, instrumentpaneler och sparade frågor du kan komma åt Azure-portalen direkt från din webbläsare i din mobila enhet. Om du vill få aviseringar, bör du konfigurera [Azure-åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) att ta emot meddelanden i form av SMS- eller en röstsamtal

## <a name="application-insights-connector-and-solution"></a>Application Insights-anslutningsprogram och lösning
[Application Insights-anslutningsprogram](log-analytics-app-insights-connector.md) gör det möjligt att ta med Application Insights-data i Log Analytics-arbetsytan. Den här datadeduplicering krävdes för att tillhandahålla synlighet mellan infrastruktur-och programdata.

Med hjälp av [mellan resurser frågor](log-analytics-cross-workspace-search.md), det finns inte längre behovet av att kopiera data. Därför måste gälla den befintliga Application Insights-lösningen upphör att. Från och juli, kommer du inte att kunna länka ny Application Insights-resurser till Log Analytics-arbetsytor. Befintliga länkar och instrumentpaneler kommer fortsätta att fungera tills November 2018.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
Den [Azure Network Security Group Analytics-lösningen](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) kommer att ersättas med den nyligen lanserade [trafikanalys](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) som ger insyn i användar- och programaktiviteten i molnnätverk. Trafikanalys kan du granska din organisations nätverksaktivitet, säkra program och data, optimera prestanda för arbetsbelastningen och följ efterlevnad. 

Den här lösningen analyserar NSG-Flödesloggar loggar och ger insikter om följande.

- Trafiken flödar över ditt nätverk mellan Azure och Internet, regioner för offentliga moln, virtuella nätverk och undernät.
- Program och protokoll i nätverket, utan att behöva Sniffer-program eller dedikerade flow samling installationer.
- Toppkommunikatörer, trafikintensiva program, VM-konversationer i molnet, trafik hotspots.
- Källor och mål för trafik mellan virtuella nätverk, kommunikation mellan relationer mellan affärskritiska processer och program.
- Säkerhet, inklusive skadlig trafik, portar öppna till Internet, program eller virtuella datorer som försöker Internetåtkomst.
- Kapacitetsutnyttjande, vilket hjälper dig att eliminera problem med etablering eller underutnyttjande.

Du kan fortsätta att förlita dig på diagnostikinställningar att skicka NSG-loggar till Log Analytics så att dina befintliga sparade sökningar, aviseringar, instrumentpaneler fortsätter att fungera. Kunder som redan har installerat lösningen kan fortsätta att använda den tills vidare. Från och 15 augusti, Network Security Group Analytics-lösning tas bort från marketplace och görs tillgängliga via communityn som en [Azure-Snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Om du har [ansluten Operations Manager-hanteringsgrupp till Log Analytics](log-analytics-om-agents.md), och sedan den fortsätter att fungera utan ändringar. Nya anslutningar men du måste följa riktlinjerna i [Microsoft System Center Operations Manager-hanteringspaket du konfigurerar Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Nästa steg
- Se [vanliga frågor för övergången från OMS-portalen till Azure-portalen för Log Analytics användare](log-analytics-oms-portal-faq.md) anvisningar om hur du flyttar från OMS-portalen till Azure-portalen.