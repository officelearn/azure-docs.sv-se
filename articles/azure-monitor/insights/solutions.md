---
title: Övervakningslösningar i Azure Monitor | Microsoft-dokument
description: Övervakningslösningar i Azure Monitor är en samling regler för logik, visualisering och datainsamling som ger mått som pivoteras runt ett visst problemområde.  Den här artikeln innehåller information om hur du installerar och använder övervakningslösningar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275209"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Övervaka lösningar i Azure Monitor
Övervakningslösningar utnyttjar tjänster i Azure för att ge ytterligare insikt i driften av ett visst program eller en viss tjänst. Den här artikeln innehåller en kort översikt över övervakningslösningar i Azure och information om hur du använder och installerar dem.

> [!NOTE]
> Övervakningslösningar kallades tidigare hanteringslösningar.

Övervakningslösningar samlar vanligtvis in loggdata och tillhandahåller frågor och vyer för att analysera insamlade data. De kan också utnyttja andra tjänster, till exempel Azure Automation, för att utföra åtgärder som är relaterade till programmet eller tjänsten.

Du kan lägga till övervakningslösningar i Azure Monitor för alla program och tjänster som du använder. De är vanligtvis tillgängliga utan kostnad men samlar in data som kan anropa användningsavgifter. Förutom lösningar som tillhandahålls av Microsoft kan partner och kunder [skapa hanteringslösningar](solutions-creating.md) som ska användas i sin egen miljö eller göras tillgängliga för kunder via communityn.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Använda övervakningslösningar
Öppna sidan **Översikt** i Azure Monitor om du vill visa en panel för varje lösning som är installerad på arbetsytan. 

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **Övervaka**.
1. Välj **Mer**under **Insights-menyn** .
1. Använd listrutorna högst upp på skärmen för att ändra arbetsytan eller tidsintervallet som används för panelerna.
1. Klicka på panelen för en lösning för att öppna sin vy som innehåller mer detaljerad analys dess insamlade data.

![Översikt](media/solutions/overview.png)

Övervakningslösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som alla andra resurser. Alla loggfrågor som ingår i lösningen visas till exempel under **Lösningsfrågor** i [Frågeutforskaren](../log-query/get-started-portal.md#load-queries) Du kan använda dessa frågor när du utför ad hoc-analys med [loggfrågor](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista installerade övervakningslösningar 
Använd följande procedur för att lista de övervakningslösningar som är installerade i din prenumeration.

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **Lösningar**.
1. Lösningar som är installerade i alla dina arbetsytor visas. Namnet på lösningen följs av namnet på arbetsytan som den är installerad på.
1. Använd listrutorna högst upp på skärmen för att filtrera efter prenumeration eller resursgrupp.


![Lista alla lösningar](media/solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna sammanfattningssidan. På den här sidan visas alla vyer som ingår i lösningen och olika alternativ för själva lösningen och dess arbetsyta. Visa sammanfattningssidan för en lösning genom att använda något av procedurerna ovan för att lista lösningar och klicka sedan på lösningens namn.

![Egenskaper för lösning](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Installera en övervakningslösning
Övervakningslösningar från Microsoft och partner är tillgängliga från [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem med hjälp av följande procedur. När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../platform/manage-access.md) där lösningen ska installeras och var dess data ska samlas in.

1. Klicka på **Lägg till**i listan över lösningar för [din prenumeration.](#list-installed-monitoring-solutions)
1. Bläddra eller sök efter en lösning. Du kan också bläddra lösningar från [denna söklänk](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Leta reda på den övervakningslösning du vill ha och läs igenom beskrivningen.
1. Klicka på **Skapa** för att starta installationsprocessen.
1. När installationsprocessen startar uppmanas du att ange log analytics-arbetsytan och tillhandahålla eventuell nödvändig konfiguration för lösningen.

![Installera en lösning](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installera en lösning från communityn
Medlemmar i communityn kan skicka hanteringslösningar till Azure Quickstart-mallar. Du kan installera dessa lösningar direkt eller hämta mallar för senare installation.

1. Följ processen som beskrivs i [Log Analytics arbetsyta och Automation-konto](#log-analytics-workspace-and-automation-account) för att länka en arbetsyta och ett konto.
2. Gå till [Azure Quickstart-mallar](https://azure.microsoft.com/documentation/templates/). 
3. Sök efter en lösning som du är intresserad av.
4. Välj lösningen i resultaten för att visa dess information.
5. Klicka på knappen **Distribuera till Azure.**
6. Du uppmanas att ange information som resursgruppen och platsen utöver värden för eventuella parametrar i lösningen.
7. Klicka på **Köp** för att installera lösningen.


## <a name="log-analytics-workspace-and-automation-account"></a>Logganalysarbetsyta och Automation-konto
Alla övervakningslösningar kräver en [Log Analytics-arbetsyta](../platform/manage-access.md) för att lagra data som samlas in av lösningen och för att vara värd för dess loggsökningar och vyer. Vissa lösningar kräver också att ett [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview) innehåller runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en Log Analytics-arbetsyta och ett Automation-konto. Du kan installera lösningen separat i flera arbetsytor.
* Om en lösning kräver ett Automation-konto måste log analytics-arbetsytan och automationskontot vara länkade till varandra. En Log Analytics-arbetsyta kan bara länkas till ett Automation-konto och ett Automation-konto kan bara länkas till en Log Analytics-arbetsyta.
* För att kunna länkas måste log analytics-arbetsytan och automationskontot finnas i samma resursgrupp och region. Undantaget är en arbetsyta i östra USA region och Automation konto i östra USA 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Skapa en länk mellan en Log Analytics-arbetsyta och Automation-konto
Hur du anger logganalysarbetsytan och automationskontot beror på installationsmetoden för din lösning.

* När du installerar en lösning via Azure Marketplace uppmanas du att ange ett arbetsyte- och Automation-konto. Länken mellan dem skapas om de inte redan är länkade.
* För lösningar utanför Azure Marketplace måste du länka log analytics-arbetsytan och automationskontot innan du installerar lösningen. Du kan göra detta genom att välja en lösning på Azure Marketplace och välja log analytics-arbetsytan och automationskontot. Du behöver inte installera lösningen eftersom länken skapas så snart logganalysarbetsytan och automationskontot har valts. När länken har skapats kan du använda den logganalysarbetsytan och automationskontot för alla lösningar.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifiera länken mellan en Log Analytics-arbetsyta och Automation-konto
Du kan verifiera länken mellan en Log Analytics-arbetsyta och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure-portalen.
1. Bläddra till avsnittet **Relaterade resurser** på menyn.
1. Om **inställningen Arbetsyta** är aktiverad länkas det här kontot till en Log Analytics-arbetsyta. Du kan klicka på **Arbetsytan** för att visa information om arbetsytan.

## <a name="remove-a-monitoring-solution"></a>Ta bort en övervakningslösning
Om du vill ta bort en installerad lösning letar du reda på den i [listan över installerade lösningar](#list-installed-monitoring-solutions). Klicka på namnet på lösningen för att öppna sammanfattningssidan och klicka sedan på **Ta bort**.


## <a name="next-steps"></a>Nästa steg
* Få en [lista över övervakningslösningar från Microsoft](solutions-inventory.md).
* Lär dig hur du [skapar frågor](../log-query/log-query-overview.md) för att analysera data som samlas in av övervakningslösningar.

