---
title: Flyttar till Azure OMS-portalen | Microsoft Docs
description: OMS-portalen är att sunsetted med alla funktioner som flyttar till Azure portal. Den här artikeln innehåller information om den här övergången.
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
ms.date: 06/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: e47e8cbd209ea34317ca9b176a2c4b0fef10a2b2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133160"
---
# <a name="oms-portal-moving-to-azure"></a>Flyttar till Azure OMS-portalen
Tack för att använda OMS-portalen. Vi uppmuntras av din support och fortsätta att kraftigt investera i våra övervakning och hantering av tjänster. En typ av feedback upprepade gånger hört från kunder är behovet av en enda användarupplevelse att övervaka och hantera både lokala och Azure arbetsbelastningar. Du vet förmodligen Azure-portalen NAV för alla Azure-tjänster och ger en omfattande partnerhanteringsfunktionen med funktioner, till exempel instrumentpaneler för att fästa resurser, intelligent Sök för att hitta resurser och märkning för resurshantering. För att konsolidera och förenkla övervakning och hantering av arbetsflödet, börjar vi lägga till OMS portal funktionerna i Azure-portalen. Vi är glada över att meddela de flesta av funktionerna i OMS-portalen är nu en del av Azure-portalen. Några av de nya funktionerna, till exempel Traffic Manager är i själva verket bara tillgängliga i Azure-portalen. Det finns bara ett fåtal luckor återstående, mest impactful som fem lösningar som fortfarande pågår flyttas till Azure-portalen. Om du inte använder dessa funktioner, kommer du att kunna göra allt du gjorde i OMS-portalen med Azure-portalen och mycket mer. Om du inte redan gjort det, rekommenderar vi att du börjar med Azure-portalen idag! 

Vi räknar med att stänga ned återstående glapp mellan två portaler av augusti 2018. Baserat på feedback från kunder, kommunicerar vi tidslinjen för sunsetting OMS-portalen. Vi kan vill flytta till Azure-portalen och räknar övergången ska vara enkelt. Men vi förstår ändringar är svåra och kan vara störande. Skicka några frågor, kommentarer eller frågor till LAUpgradeFeedback@microsoft.com. Resten av den här artikeln går över viktiga scenarier, aktuella luckor och översikt för den här övergången. 


## <a name="what-will-change"></a>Vad ändras? 
Följande ändringar meddelas med utfasningen av OMS-portalen. Var och en av de här ändringarna beskrivs mer ingående i avsnitten nedan.

- Ny avisering hanteringen av ersätter hanteringslösning för aviseringen.
- Hantering av användare kommer att göras i Azure-portalen med hjälp av rollbaserad åtkomstkontroll i Azure.
- Application Insights Connector inte längre behövs eftersom samma funktioner kan aktiveras via mellan arbetsytan frågor.
- Mobilapp OMS att bli inaktuell. 
- NSG-lösningen ersätts med utökad funktionalitet som är tillgängliga via trafik Analytics lösning.



## <a name="current-known-gaps"></a>Aktuella kända luckor 
Det finns vissa funktioner luckor som kräver att du fortfarande använda OMS-portalen. Dessa mellanrum stängs och det här dokumentet uppdateras korrekt. Du bör också läsa [Azure uppdaterar](https://azure.microsoft.com/updates/?product=log-analytics) för pågående meddelanden om tillägg och ändringar.

- Följande lösningar är ännu inte fungerar i Azure-portalen. Du bör fortsätta att använda dessa lösningar i den klassiska portalen förrän de har uppdaterats.

    - Windows Analyslösningar ([beredskap för uppgradering](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [enhetens hälsotillstånd](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), och [Uppdateringskompatibilitet](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [DNS-analys](log-analytics-dns.md) 
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Om du vill komma åt logganalys resurs i Azure, användaren beviljas åtkomst via [Azure rollbaserad åtkomst](#user-access-and-role-migration).
- Uppdatera scheman som skapats i OMS-portalen kanske inte återspeglas i distributioner för schemalagd uppdatering eller uppdatera jobbhistorik för Update management instrumentpanelen i Azure-portalen. Luckan förväntas åtgärdas i slutet av juni 2018.
- Anpassade loggar förhandsgranskningsfunktion kan bara aktiveras via OMS-portalen. I slutet av juni 2018 aktiveras detta automatiskt för alla utrymmet.
 
## <a name="what-should-i-do-now"></a>Vad ska jag göra nu?  
Du bör se [vanliga frågor för övergången från OMS-portalen på Azure-portalen för Log Analytics användare](../log-analytics/log-analytics-oms-portal-faq.md) information om hur du övergår till Azure-portalen. Om den [luckor ovan](#current-known-gaps) inte gäller för din miljö och sedan bör du starta med hjälp av Azure-portalen som din primära upplevelse. Skicka feedback, frågor och frågor till LAUpgradeFeedback@microsoft.com.

## <a name="changes-to-alerts"></a>Ändringar av aviseringar 

### <a name="alert-extension"></a>Varning-tillägget  
Aviseringar håller på att [utökats till Azure-portalen](../monitoring-and-diagnostics/monitoring-alerts-extend.md). När den är klar går hanteringsåtgärder på aviseringar bara att i Azure-portalen. Befintliga aviseringar fortsätter att visas i OMS-portalen. Om du ansluter till aviseringar via programmering med hjälp av Log Analytics avisering REST API eller Log Analytics avisering Resource mall, måste du använda åtgärdsgrupper i stället för åtgärder i API-anrop, mallar för Azure Resource Manager och PowerShell-kommandon.

### <a name="alert-management-solution"></a>Lösning för avisering
I stället för den [Varna hanteringslösning](log-analytics-solution-alert-management.md), kan du använda [Azure-Monitor unified aviseringar gränssnittet](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) att visualisera och hantera aviseringar. Den nya upplevelsen aggregerar aviseringar från flera källor i Azure inklusive loggen aviseringar från logganalys. Du kan se distributioner av aviseringarna, dra nytta av automatisk gruppering av relaterade aviseringar via smart grupper och visa aviseringar över flera prenumerationer när de omfattande filtren har använts. Alla dessa funktioner är tillgängliga i förhandsversionen startar 4 juni 2018. Aviseringen hanteringslösningen blir inte tillgängliga i Azure-portalen. 

Data som samlas in av Alert Management-lösning (poster med en typ av avisering) fortsätter att vara i logganalys så länge lösningen har installerats för arbetsytan. Starta augusti 2018 aktiveras strömning av aviseringar från enhetlig aviseringar i arbetsytor, ersätter den här funktionen. Vissa schemaändringar är förväntat och kommer att utannonseras vid ett senare tillfälle.

## <a name="user-access-and-role-migration"></a>Användarmigrering för åtkomst och roll
Azure portal access management är bättre och mer kraftfulla än åtkomsthantering i OMS-portalen, men den kräver vissa konverteringar. Se [hantera arbetsytor](log-analytics-manage-access.md#manage-accounts-and-users) information för åtkomsthantering av logganalys.

Starta 25 juni och fortsätta via juli, styra automatisk konvertering av åtkomst behörigheter från OMS-portalen på Azure-portalen behörigheter startar. När konverteringen har slutförts kommer avsnittet OMS-portalen användare management dirigera användare till åtkomstkontroll (IAM) i Azure. 

Under konverteringen systemet kontrollerar varje användare eller säkerhetsgrupp som har behörigheter i OMS-portalen och kontrollera om den har samma nivå eller behörigheter i Azure. Behörighet saknas, tilldelar följande roller för den relevanta arbetsytor och lösningar.

| OMS portal behörighet | Azure-rollen |
|:---|:---|
| Skrivskyddad | Log Analytics Reader |
| Deltagare | Log Analytics Contributor |
| Administratör | Ägare |

Se till att inga överdriven behörigheter tilldelas användare genom tilldelar systemet inte automatiskt dessa behörigheter på resursgruppsnivå. Därför arbetsytan administratörer måste manuellt tilldela själva _ägare_ eller _deltagare_ roller på resurs eller en prenumeration nivå för att utföra följande åtgärder.

- Lägg till eller ta bort lösningar
- Definiera nya anpassade vyer
- Hantera aviseringar 

I vissa fall kan den automatiska konverteringen kan inte använda behörigheten och uppmanar administratören att tilldela behörigheter manuellt.

## <a name="oms-mobile-app"></a>OMS Mobilapp
OMS mobilappen blir sunsetted tillsammans med OMS-portalen. I stället för mobila appar OMS för att komma åt information om din IT-infrastruktur, instrumentpaneler och sparade frågor du kan komma åt Azure-portalen direkt från din webbläsare i din mobila enhet. För att få aviseringar, bör du konfigurera [Azure åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) att ta emot meddelanden i form av SMS- eller ett röstsamtal

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector och lösning
[Application Insights Connector](log-analytics-app-insights-connector.md) är ett sätt att hämta Application Insights-data till logganalys-arbetsytan. Dataduplicering krävdes för att aktivera synlighet mellan infrastruktur-och programdata.

Med stöd för [cross-resurs frågor](log-analytics-cross-workspace-search.md), finns det inte längre behovet att kopiera data. Det innebär att den befintliga Application Insights-lösningen bli inaktuell. Starta juli kommer du inte att länka ny Application Insights-resurser till logganalys arbetsytor. Befintliga länkar och instrumentpaneler fortsätter att fungera förrän November 2018.


## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
Den [Azure Network Security Group Analytics lösning](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) ersätts med de nyligen lanserade [trafik Analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) som ger inblick i användar- och programaktivitet på molnet nätverk. Trafik Analytics kan du granska din organisations nätverksaktivitet, säkra program och data, optimera arbetsbelastningsprestanda och vara kompatibla. 

Den här lösningen analyserar NSG flöda loggar och ger insikter om följande.

- Trafiken flödar över ditt nätverk mellan Azure och Internet, offentligt moln regioner, Vnet och undernät.
- Program och protokoll i nätverket, utan att behöva Sniffer-program eller dedikerade flödet samling installationer.
- Övre talkers chatty program, Virtuella konversationer i molnet, trafik anslutningar.
- Källor eller mål för trafik mellan virtuella nätverk, mellan relationer mellan affärskritiska tjänster och program.
- Säkerhet inklusive skadlig trafik, öppna till Internet, program eller virtuella datorer som försöker Internetåtkomst portar.
- Kapacitetsutnyttjande som du kan utesluta problem av över etablering och underutnyttjande.

Du kan fortsätta att lita på diagnostikinställningar att skicka NSG-loggar till logganalys så att din befintliga sparade sökningar, aviseringar, instrumentpaneler fortsätter att fungera. Kunder som redan har installerat lösningen kan fortsätta att använda tills vidare. Starta 20 juni Network Security Group Analytics lösningen tas bort från marketplace och tillgängliga via gemenskapen som en [Azure QuickStart mallen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="next-steps"></a>Nästa steg
- Se [vanliga frågor för övergången från OMS-portalen på Azure-portalen för Log Analytics användare](log-analytics-oms-portal-faq.md) anvisningar om hur du flyttar från OMS-portalen till Azure-portalen.