---
title: Lösningar för hantering i Azure Monitor | Microsoft Docs
description: Lösningar för hantering i Azure Monitor är en samling logik, visualisering och datahämtningsregler som tillhandahåller statistik rörande särskilda problemområden.  Den här artikeln innehåller information om att installera och använda lösningar för hantering.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: bwren
ms.openlocfilehash: 1eac238c2379abf0b46d0975889a71ac7aa6a9ab
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141568"
---
# <a name="management-solutions-in-azure-monitor"></a>Lösningar för hantering i Azure Monitor
Lösningar för hantering av utnyttja tjänster i Azure för att ge ytterligare insikter i driften av ett visst program eller tjänst. Den här artikeln innehåller en kort översikt över lösningar för hantering i Azure och information om hur du använder och installeras.

Lösningar för hantering av normalt samla in loggdata och tillhandahålla frågor och vyer för att analysera insamlade data. De kan även använda andra tjänster som Azure Automation för att utföra åtgärder som rör programmet eller tjänsten.

Du kan lägga till lösningar för hantering av Azure Monitor för alla program och tjänster som du använder. De finns vanligtvis på utan kostnad men samla in data som kan anropa avgifter för användning. Förutom lösningar från Microsoft, partner och kunder kan [skapa lösningar för hantering av](solutions-creating.md) som ska användas i sin egen miljö eller göras tillgängliga för kunder via communityn.

## <a name="use-management-solutions"></a>Använd lösningar för hantering
Öppna den **översikt** för Log Analytics-arbetsytan visas en panel för varje lösning som är installerade på arbetsytan. 

1. Logga in på Azure Portal.
1. Öppna **alla tjänster** och leta upp **övervakaren**.
1. Under den **Insights** menyn och välj **mer**.
1. Använd rutorna listrutan överst på skärmen för att ändra arbetsytan eller tidsintervallet som används för panelerna.
1. Klicka på panelen för en lösning för att öppna vyn som innehåller mer detaljerad analys dess insamlade data.

![Översikt](media/solutions/overview.png)

Hanteringslösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som alla andra resurser. Till exempel alla loggfrågor som ingår i lösningen visas under **lösning frågor** i [frågeutforskaren](../log-query/get-started-portal.md#load-queries) du kan använda dessa frågor när du utför ad hoc-analyser med Log Analytics.

## <a name="list-installed-management-solutions"></a>Visa installerade hanteringslösningar 
Använd följande procedur för att lista hanteringslösningar som installerats i prenumerationen.

1. Logga in på Azure Portal.
1. Öppna **alla tjänster** och leta upp **lösningar**.
4. Lösningar som är installerade på alla dina arbetsytor visas i listan. Namnet på lösningen följt av namnet på den installeras på Log Analytics-arbetsytan.
1. Använda rutorna listrutan överst på skärmen för att filtrera efter prenumeration eller resursgrupp.


![Lista över alla lösningar](media/solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna dess sammanfattningssidan. Den här sidan visar alla vyer som ingår i lösningen och olika alternativ för lösningen själva och sin arbetsyta. Visa sammanfattningssidan för en lösning med någon av procedurerna ovan för att lista lösningar och klicka sedan på namnet på lösningen.

![Egenskaper för lösningen](media/solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Installera en lösning för hantering
Lösningar från Microsoft och partner som är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem med hjälp av följande procedur. När du installerar en lösning måste du välja en [Log Analytics-arbetsyta](../platform/manage-access.md) var lösningen ska installeras och där data samlas in.

1. Från den [listan med lösningar för din prenumeration](#list-installed-monitoring-solutions), klickar du på **Lägg till**. 
1. Till höger om **hanteringslösningar**, klickar du på **mer**. 
1. Leta upp den hanteringslösning som du vill ha och Läs igenom en beskrivning.
1. Klicka på **skapa** att starta installationen.
1. När installationen startar uppmanas du att ange vilken konfiguration som krävs som varierar för varje lösning.

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
Alla lösningar kräver en [Log Analytics-arbetsyta](../platform/manage-access.md) att lagra data som samlas in av lösningen och som värd för loggsökningar och vyer. Vissa lösningar kräver också en [Automation-konto](../../automation/automation-security-overview.md#automation-account-overview) som innehåller runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en Log Analytics-arbetsyta och ett Automation-konto. Du kan installera lösningen separat till flera arbetsytor.
* Om en lösning kräver ett Automation-konto, måste sedan Log Analytics-arbetsytan och Automation-kontot kopplas till varandra. Log Analytics-arbetsytan kan endast kopplas till ett Automation-konto och ett Automation-konto kan endast kopplas till en Log Analytics-arbetsyta.
* Om du vill för att länka måste Log Analytics-arbetsytan och Automation-kontot vara i samma resursgrupp och region. Undantaget är en arbetsyta i regionen USA, östra och Automation-konto i USA, östra 2.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Skapa en länk mellan en Log Analytics-arbetsytan och Automation-konto
Hur du anger Log Analytics-arbetsytan och Automation-kontot beror på vilken installationsmetod för din lösning.

* När du installerar en lösning på Azure Marketplace, uppmanas du för en arbetsyta och Automation-konto. Länken mellan dem skapas om de inte redan är länkad.
* För lösningar utanför Azure Marketplace måste du koppla den Log Analytics-arbetsytan och Automation-konto innan du installerar lösningen. Du kan göra detta genom att välja en lösning på Azure Marketplace och välja Log Analytics-arbetsytan och Automation-kontot. Du behöver att faktiskt installera lösningen eftersom länken skapas när Log Analytics-arbetsytan och Automation-kontot har markerats. När länken har skapats kan du använda den Log Analytics-arbetsytan och Automation-kontot för alla lösningar.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Kontrollera länken mellan en Log Analytics-arbetsytan och Automation-konto
Du kan kontrollera länken mellan en Log Analytics-arbetsyta och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure-portalen.
1. Bläddra till den **relaterade resurser** på menyn.
1. Om den **arbetsytan** inställningen är aktiverad och sedan det här kontot är länkat till en Log Analytics-arbetsyta. Du kan klicka på **arbetsytan** att visa information om arbetsytan.

## <a name="remove-a-management-solution"></a>Ta bort en lösning för hantering
Om du vill ta bort en installerad lösning, hitta det i den [listan över installerade lösningar](#list-installed-monitoring-solutions). Klicka på namnet på lösningen att öppna dess sammanfattningssidan och klicka sedan på **ta bort**.




## <a name="next-steps"></a>Nästa steg
* Hämta en [lista med lösningar från Microsoft](solutions-inventory.md).
* Lär dig hur du [skapa frågor](../../azure-monitor/log-query/log-query-overview.md) att analysera data som samlas in av lösningar för hantering.

