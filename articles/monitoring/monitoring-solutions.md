---
title: Lösningar för hantering i Azure | Microsoft Docs
description: Lösningar för hantering i Azure är en samling av logik och visualisering data förvärv regler som ger mått pivoteras runt ett specifikt problem.  Den här artikeln innehåller information om hur du installerar och använder lösningar för hantering.
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
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 3377a0b4e6440d83962d103b3e1770ccf43bd785
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752929"
---
# <a name="management-solutions-in-azure"></a>Lösningar för hantering i Azure
Hanteringslösningar utnyttja tjänster i Azure för att ge ytterligare insikter i driften av ett visst program eller tjänst. Den här artikeln innehåller en kort översikt över lösningar för hantering i Azure och information om använda och installera dem.

Hanteringslösningar vanligtvis samla in information till logganalys och anger loggen sökningar och vyer för att analysera insamlade data. De kan också använda andra tjänster som Azure Automation för att utföra åtgärder som rör programmet eller tjänsten.

Du kan lägga till lösningar för din Azure-prenumeration för alla program och tjänster som du använder. De finns vanligtvis på utan kostnad men samla in data som kan anropa avgifter för användning. Förutom lösningar som tillhandahålls av Microsoft, partner och kunder kan [skapa hanteringslösningar](../monitoring/monitoring-solutions-creating.md) som ska användas i sina egna miljö eller som finns tillgängliga för kunder via gemenskapen.

## <a name="using-management-solutions"></a>Med hjälp av lösningar för hantering
Den **översikt** sidan för varje logganalys-arbetsytan visar en panel för varje lösning som är installerad på arbetsytan. Klicka på ikonen att öppna vyn som innehåller mer detaljerad analys lösningen insamlade data.

![Översikt](media/monitoring-solutions/overview.png)

Hanteringslösningar kan innehålla flera typer av Azure-resurser och du kan visa alla resurser som ingår i en lösning precis som alla andra resurser. Till exempel loggen sökningar ingår i lösningen ingår i **sparade sökningar** i arbetsytan. Du kan använda dessa sökningar när du utför ad hoc-analys i logganalys.

## <a name="list-installed-management-solutions"></a>Visa installerade hanteringslösningar 
Använd följande procedur för att lista hanteringslösningar som installerats i din prenumeration.

1. Logga in på Azure Portal.
2. I den vänstra rutan, Välj **alla tjänster**.
3. Antingen rulla ned till **lösningar** eller typ *lösningar* till den **Filter** dialogrutan.
4. Lösningar som installerats i alla arbetsytor finns i listan. Namnet på lösningen följt av namnet på logganalys-arbetsytan som är installerad.
1. Använda rutorna listrutan överst på skärmen för att filtrera efter prenumeration eller resursgrupp.


![Visa en lista över alla lösningar](media/monitoring-solutions/list-solutions-all.png)

Klicka på namnet på en lösning för att öppna dess sammanfattningssidan. Den här sidan visas alla logganalys vyer som ingår i lösningen och innehåller olika alternativ för lösningen sig själv och dess arbetsyta. Visa sidan Sammanfattning för en lösning med någon av procedurerna ovan för att lista lösningar och klicka sedan på namnet för lösningen.

![Egenskaper för lösning](media/monitoring-solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Installera en lösning
Av hanteringslösningar från Microsoft och partner som är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com). Du kan söka efter tillgängliga lösningar och installera dem med hjälp av följande procedur.

1. Från den [lista med lösningar för din prenumeration](#list-installed-management-solutions), klickar du på **Lägg till**. 
1. Till höger om **hanteringslösningar**, klickar du på **mer**. 
1. Leta upp den hanteringslösning och om du vill läsa igenom beskrivningen.
1. Klicka på **skapa** att starta installationen.
1. När installationen startar uppmanas du att tillhandahålla nödvändig konfiguration som varierar för varje lösning. Alla kräver att du väljer en logganalys-arbetsyta där lösningen ska installeras och var data ska samlas. 

![Installera en lösning](media/monitoring-solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Installera en lösning i communityn
Medlemmar i gruppen kan skicka hanteringslösningar till Azure Quickstart mallar. Du kan installera dessa lösningar direkt eller hämta dem mallar för senare installation.

1. Följ processen som beskrivs i [logganalys-arbetsytan och Automation-konto](#log-analytics-workspace-and-automation-account) att länka en arbetsyta och kontot.
2. Gå till [Azure Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/). 
3. Sök efter en lösning som du är intresserad av.
4. Välj lösningen resultat att visa information.
5. Klicka på den **till Azure** knappen.
6. Du uppmanas att ange information, till exempel resursgrupp och plats förutom värden för alla parametrar i lösningen.
7. Klicka på **inköp** installera lösningen.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-arbetsyta och Automation-konto
Alla hanteringslösningar för av kräver en [logganalys-arbetsytan](../log-analytics/log-analytics-manage-access.md) att lagra data som samlas in av lösningen och dess loggen sökningar och vyer. Vissa lösningar kräver också en [Automation-konto](../automation/automation-security-overview.md#automation-account-overview) ska innehålla runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* Varje installation av en lösning kan bara använda en logganalys-arbetsytan och en Automation-konto. Du kan installera lösningen separat i flera arbetsytor.
* Om en lösning kräver ett Automation-konto kan måste sedan logganalys-arbetsytan och Automation-konto länkas till varandra. Logganalys-arbetsytan kan bara kopplas till ett Automation-konto och ett Automation-konto kan bara kopplas till en logganalys-arbetsytan.
* Om du vill för att länka måste logganalys-arbetsytan och Automation-kontot vara i samma resursgrupp och region. Undantaget är en arbetsyta i östra USA och Automation-konto i östra USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Skapa en länk mellan en logganalys-arbetsytan och Automation-konto
Hur du anger logganalys-arbetsytan och Automation-konto beror på installationsmetoden för lösningen.

* När du installerar en lösning via Azure Marketplace uppmanas för en arbetsyta och Automation-konto. Länken mellan dem skapas om de inte är redan länkad.
* Lösningar utanför Azure Marketplace kan länka du logganalys-arbetsytan och Automation-konto innan du installerar lösningen. Du kan göra detta genom att välja en lösning i Azure Marketplace och välja logganalys-arbetsytan och Automation-konto. Du behöver installera lösningen faktiskt eftersom skapas länken så snart logganalys-arbetsytan och Automation-konto har valts. När länken har skapats kan du använda den logganalys-arbetsytan och Automation-kontot för alla lösningar.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifiering av länken mellan en logganalys-arbetsytan och Automation-konto
Du kan kontrollera länken mellan logganalys-arbetsytan och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure-portalen.
1. Bläddra till den **relaterade resurser** på menyn.
1. Om den **arbetsytan** inställningen är aktiverad och det här kontot är kopplad till logganalys-arbetsytan. Du kan klicka på **arbetsytan** att visa detaljer för arbetsytan.

## <a name="remove-a-management-solution"></a>Ta bort en lösning
Om du vill ta bort en installerad lösning, leta upp den i den [listan över installerade lösningar](#list-installed-management-solutions). Klicka på namnet på lösningen för att öppna dess sammanfattningssidan och klicka sedan på **ta bort**.




## <a name="next-steps"></a>Nästa steg
* Hämta en [lista med lösningar från Microsoft](monitoring-solutions-inventory.md).
* Lär dig hur du [skapa frågor](../log-analytics/log-analytics-log-searches.md) att analysera data som samlas in av lösningar för hantering.

