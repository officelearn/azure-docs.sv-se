---
title: Övervaknings lösningar i Azure Monitor | Microsoft Docs
description: Övervaknings lösningar i Azure Monitor är en samling logik-, visualiserings-och data hämtnings regler som tillhandahåller mått som pivoteras runt ett visst problemområden.  Den här artikeln innehåller information om hur du installerar och använder övervaknings lösningar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/12/2020
ms.openlocfilehash: 2627cd540efe644315e303337b7dce95a6e2d126
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832052"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Övervaknings lösningar i Azure Monitor

Övervaknings lösningar utnyttjar tjänster i Azure för att ge ytterligare insyn i driften av ett visst program eller en viss tjänst. Den här artikeln innehåller en kort översikt över övervakning av lösningar i Azure och information om hur du använder och installerar dem.

Du kan lägga till övervaknings lösningar som Azure Monitor för alla program och tjänster som du använder. De är vanligt vis tillgängliga utan kostnad utan att samla in data som kan anropa användnings kostnader. Förutom lösningar som tillhandahålls av Microsoft kan partner och kunder [skapa hanterings lösningar](solutions-creating.md) som ska användas i sin egen miljö eller som görs tillgängliga för kunder via communityn.

## <a name="use-monitoring-solutions"></a>Använda övervakningslösningar

Öppna sidan **Översikt** i Azure Monitor om du vill visa en panel för varje lösning som är installerad i arbets ytan. 

1. Gå till [Azure Portal](https://ms.portal.azure.com). Sök efter och välj **övervakare**.
1. Under menyn **insikter** väljer du **mer**.
1. Använd List rutorna längst upp på skärmen för att ändra arbets ytan eller tidsintervallet som används för panelerna.
1. Klicka på panelen för en lösning för att öppna dess vy som innehåller mer detaljerad analys av insamlade data.

![Översikt](media/solutions/overview.png)

Övervaknings lösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som andra resurser. Till exempel visas alla logg frågor som ingår i lösningen under **lösnings frågor** i [query Explorer](../log-query/get-started-portal.md#load-queries) . du kan använda dessa frågor när du utför ad hoc-analyser med [logg frågor](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista installerade övervaknings lösningar

Använd följande procedur för att visa en lista över de övervaknings lösningar som är installerade i din prenumeration.

1. Gå till [Azure Portal](https://ms.portal.azure.com). Sök efter och välj **lösningar**.
1. Lösningar som är installerade på alla dina arbets ytor visas. Namnet på lösningen följs av namnet på arbets ytan som den är installerad i.
1. Använd List rutorna överst på skärmen för att filtrera efter prenumeration eller resurs grupp.


![Lista alla lösningar](media/solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna sammanfattnings sidan. Den här sidan visar alla vyer som ingår i lösningen och innehåller olika alternativ för själva lösningen och dess arbets yta. Visa sammanfattnings sidan för en lösning med hjälp av någon av procedurerna ovan för att lista lösningar och klicka sedan på lösningens namn.

![Lösnings egenskaper](media/solutions/solution-properties.png)

## <a name="install-a-monitoring-solution"></a>Installera en övervaknings lösning

Övervaknings lösningar från Microsoft och partner är tillgängliga från [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem på följande sätt. När du installerar en lösning måste du välja en [Log Analytics arbets yta](../platform/manage-access.md) där lösningen ska installeras och var data ska samlas in.

1. I [listan med lösningar för din prenumeration](#list-installed-monitoring-solutions)klickar du på **Lägg till**.
1. Bläddra eller Sök efter en lösning. Du kan också bläddra bland lösningar från [den här Sök länken](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Leta upp den övervaknings lösning som du vill ha och Läs igenom beskrivningen.
1. Starta installations processen genom att klicka på **skapa** .
1. När installations processen startar uppmanas du att ange Log Analytics arbets ytan och tillhandahålla nödvändig konfiguration för lösningen.

![Installera en lösning](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installera en lösning från communityn

Medlemmar i communityn kan skicka in hanterings lösningar till Azures snabb starts mallar. Du kan installera dessa lösningar direkt eller hämta mallar för senare installation.

1. Följ processen som beskrivs i [Log Analytics arbets yta och Automation-konto](#log-analytics-workspace-and-automation-account) för att länka en arbets yta och ett konto.
2. Gå till [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/). 
3. Sök efter en lösning som du är intresse rad av.
4. Välj lösningen från resultaten om du vill visa information om den.
5. Klicka på knappen **distribuera till Azure** .
6. Du uppmanas att ange information, till exempel resurs gruppen och platsen, förutom värdena för alla parametrar i lösningen.
7. Klicka på **köp** för att installera lösningen.

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics arbets yta och Automation-konto

Alla övervaknings lösningar kräver en [Log Analytics arbets yta](../platform/manage-access.md) för att lagra data som samlas in av lösningen och vara värd för loggs ökningar och vyer. Vissa lösningar kräver också ett [Automation-konto](../../automation/automation-security-overview.md) för att innehålla Runbooks och relaterade resurser. Arbets ytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en Log Analytics arbets yta och ett Automation-konto. Du kan installera lösningen separat i flera arbets ytor.
* Om en lösning kräver ett Automation-konto måste Log Analytics arbets yta och Automation-konto länkas till varandra. En Log Analytics-arbetsyta kan bara länkas till ett Automation-konto och ett Automation-konto kan bara länkas till en Log Analytics arbets yta.
* För att länkas måste Log Analytics-arbetsytan och automation-kontot finnas i samma prenumeration, men kan finnas i olika resurs grupper som distribueras till samma region. Undantaget är en arbets yta i regionen USA, östra och ett Automation-konto i USA, östra 2.

När du installerar en lösning via Azure Marketplace uppmanas du att ange en arbets yta och ett Automation-konto. Länken mellan dem skapas om de inte redan är länkade.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifiera länken mellan en Log Analytics arbets yta och ett Automation-konto

Du kan kontrol lera länken mellan en Log Analytics-arbetsyta och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure Portal.
1. Rulla till avsnittet **relaterade resurser** på menyn och välj **länkad arbets yta**.
1. Om **arbets ytan** är länkad till ett Automation-konto visar den här sidan arbets ytan som den är länkad till. Om du väljer namnet på arbets ytan som visas, omdirigeras du till sidan Översikt för den arbets ytan.

## <a name="remove-a-monitoring-solution"></a>Ta bort en övervaknings lösning

Om du vill ta bort en installerad lösning letar du upp den i [listan över installerade lösningar](#list-installed-monitoring-solutions). Klicka på namnet på lösningen för att öppna sammanfattnings sidan och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

* Hämta en [lista över övervaknings lösningar från Microsoft](solutions-inventory.md).
* Lär dig hur du [skapar frågor](../log-query/log-query-overview.md) för att analysera data som samlas in av övervaknings lösningar.