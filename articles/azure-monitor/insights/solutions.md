---
title: Övervaknings lösningar i Azure Monitor | Microsoft Docs
description: Övervaknings lösningar i Azure Monitor är en samling logik-, visualiserings-och data hämtnings regler som tillhandahåller mått som pivoteras runt ett visst problemområden.  Den här artikeln innehåller information om hur du installerar och använder övervaknings lösningar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 7ea7051b24deeddea25b1db771b73a226f650067
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75401156"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Övervaknings lösningar i Azure Monitor
Övervaknings lösningar utnyttjar tjänster i Azure för att ge ytterligare insyn i driften av ett visst program eller en viss tjänst. Den här artikeln innehåller en kort översikt över övervakning av lösningar i Azure och information om hur du använder och installerar dem.

> [!NOTE]
> Övervaknings lösningar kallades tidigare för hanterings lösningar.

Övervaknings lösningar samlar normalt in loggdata och tillhandahåller frågor och vyer för att analysera insamlade data. De kan även använda andra tjänster som Azure Automation för att utföra åtgärder som rör programmet eller tjänsten.

Du kan lägga till övervaknings lösningar som Azure Monitor för alla program och tjänster som du använder. De är vanligt vis tillgängliga utan kostnad utan att samla in data som kan anropa användnings kostnader. Förutom lösningar från Microsoft, partner och kunder kan [skapa lösningar för hantering av](solutions-creating.md) som ska användas i sin egen miljö eller göras tillgängliga för kunder via communityn.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Använda övervakningslösningar
Öppna sidan **Översikt** i Azure Monitor om du vill visa en panel för varje lösning som är installerad i arbets ytan. 

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **övervakare**.
1. Under den **Insights** menyn och välj **mer**.
1. Använd rutorna listrutan överst på skärmen för att ändra arbetsytan eller tidsintervallet som används för panelerna.
1. Klicka på panelen för en lösning för att öppna dess vy som innehåller mer detaljerad analys av insamlade data.

![Översikt](media/solutions/overview.png)

Övervaknings lösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som andra resurser. Till exempel visas alla logg frågor som ingår i lösningen under **lösnings frågor** i [query Explorer](../log-query/get-started-portal.md#load-queries) . du kan använda dessa frågor när du utför ad hoc-analyser med [logg frågor](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista installerade övervaknings lösningar 
Använd följande procedur för att visa en lista över de övervaknings lösningar som är installerade i din prenumeration.

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **lösningar**.
1. Lösningar som är installerade på alla dina arbetsytor visas i listan. Namnet på lösningen följs av namnet på arbets ytan som den är installerad i.
1. Använda rutorna listrutan överst på skärmen för att filtrera efter prenumeration eller resursgrupp.


![Lista över alla lösningar](media/solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna dess sammanfattningssidan. Den här sidan visar alla vyer som ingår i lösningen och olika alternativ för lösningen själva och sin arbetsyta. Visa sammanfattningssidan för en lösning med någon av procedurerna ovan för att lista lösningar och klicka sedan på namnet på lösningen.

![Egenskaper för lösningen](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Installera en övervaknings lösning
Övervaknings lösningar från Microsoft och partner är tillgängliga från [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem med hjälp av följande procedur. När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../platform/manage-access.md) var lösningen ska installeras och där data samlas in.

1. Från den [listan med lösningar för din prenumeration](#list-installed-monitoring-solutions), klickar du på **Lägg till**.
1. Bläddra eller Sök efter en lösning. Du kan också bläddra bland lösningar från [den här Sök länken](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Leta upp den övervaknings lösning som du vill ha och Läs igenom beskrivningen.
1. Klicka på **skapa** att starta installationen.
1. När installations processen startar uppmanas du att ange Log Analytics arbets ytan och tillhandahålla nödvändig konfiguration för lösningen.

![Installera en lösning](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installera en lösning från communityn
Medlemmar i communityn kan skicka datahanteringslösningar till Azure-Snabbstartsmallar. Du kan installera dessa lösningar direkt eller hämta dem mallar för senare installation.

1. Följ processen som beskrivs i [Log Analytics-arbetsytan och Automation-kontot](#log-analytics-workspace-and-automation-account) att länka en arbetsyta och konto.
2. Gå till [Azures Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/). 
3. Sök efter en lösning som du är intresserad av.
4. Välj lösningen resultat att visa dess egenskaper.
5. Klicka på den **distribuera till Azure** knappen.
6. Du uppmanas att ange information, till exempel resursgrupp och plats förutom värden för alla parametrar i lösningen.
7. Klicka på **köp** att installera lösningen.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-arbetsytan och Automation-konto
Alla övervaknings lösningar kräver en [Log Analytics arbets yta](../platform/manage-access.md) för att lagra data som samlas in av lösningen och vara värd för loggs ökningar och vyer. Vissa lösningar kräver också en [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview) som innehåller runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en Log Analytics-arbetsyta och ett Automation-konto. Du kan installera lösningen separat till flera arbetsytor.
* Om en lösning kräver ett Automation-konto, måste sedan Log Analytics-arbetsytan och Automation-kontot kopplas till varandra. Log Analytics-arbetsytan kan endast kopplas till ett Automation-konto och ett Automation-konto kan endast kopplas till en Log Analytics-arbetsyta.
* Om du vill för att länka måste Log Analytics-arbetsytan och Automation-kontot vara i samma resursgrupp och region. Undantaget är en arbetsyta i regionen USA, östra och Automation-konto i USA, östra 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Skapa en länk mellan en Log Analytics-arbetsytan och Automation-konto
Hur du anger Log Analytics-arbetsytan och Automation-kontot beror på vilken installationsmetod för din lösning.

* När du installerar en lösning via Azure Marketplace uppmanas du att ange en arbets yta och ett Automation-konto. Länken mellan dem skapas om de inte redan är länkad.
* För lösningar utanför Azure Marketplace måste du koppla den Log Analytics-arbetsytan och Automation-konto innan du installerar lösningen. Du kan göra detta genom att välja en lösning på Azure Marketplace och välja Log Analytics-arbetsytan och Automation-kontot. Du behöver att faktiskt installera lösningen eftersom länken skapas när Log Analytics-arbetsytan och Automation-kontot har markerats. När länken har skapats kan du använda den Log Analytics-arbetsytan och Automation-kontot för alla lösningar.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Kontrollera länken mellan en Log Analytics-arbetsytan och Automation-konto
Du kan kontrollera länken mellan en Log Analytics-arbetsyta och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure-portalen.
1. Bläddra till den **relaterade resurser** på menyn.
1. Om den **arbetsytan** inställningen är aktiverad och sedan det här kontot är länkat till en Log Analytics-arbetsyta. Du kan klicka på **arbetsytan** att visa information om arbetsytan.

## <a name="remove-a-monitoring-solution"></a>Ta bort en övervaknings lösning
Om du vill ta bort en installerad lösning, hitta det i den [listan över installerade lösningar](#list-installed-monitoring-solutions). Klicka på namnet på lösningen att öppna dess sammanfattningssidan och klicka sedan på **ta bort**.


## <a name="next-steps"></a>Nästa steg
* Hämta en [lista över övervaknings lösningar från Microsoft](solutions-inventory.md).
* Lär dig hur du [skapar frågor](../log-query/log-query-overview.md) för att analysera data som samlas in av övervaknings lösningar.

