---
title: "Azure hanteringslösningar | Microsoft Docs"
description: "Lösningar för hantering med scenarier med hantering av paketerade i Azure som kunder kan lägga till sina logganalys-arbetsytan.  Den här artikeln innehåller information om hur anpassade lösningar som skapats av kunder och partner."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b9ad6da3963fefc5441581d113f6f690bd72be0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Arbeta med hanteringslösningar i Azure (förhandsversion)
> [!NOTE]
> Den här är dokumentationen preliminär för hanteringslösningar i Azure som för närvarande finns i förhandsgranskningen.    
> 
> 

Av hanteringslösningar omfattar paketerade hanteringsscenarier som kunder kan lägga till sina Azure-miljön.  Förutom [lösningar som tillhandahålls av Microsoft](../log-analytics/log-analytics-add-solutions.md), partner och kunder kan skapa lösningar för att användas i sina egna miljö eller som finns tillgängliga för kunder via gemenskapen.

## <a name="finding-and-installing-management-solutions"></a>Söka efter och installera lösningar för hantering
Det finns flera metoder för att hitta och installera lösningar som beskrivs i följande avsnitt.

### <a name="azure-marketplace"></a>Azure Marketplace
Lösningar som tillhandahålls av Microsoft och tillförlitliga partners kan installeras från Azure Marketplace i Azure-portalen.

1. Logga in på Azure Portal.
2. I den vänstra rutan, Välj **fler tjänster**.
3. Antingen rulla ned till **lösningar** eller typ *lösningar* till den **Filter** dialogrutan.
4. Klicka på den **+ Lägg till** knappen.
5. Sök efter lösningar som du är intresserad av antingen genom att bläddra, klicka på den **Filter** knappen eller skriva i den **Sök Everthing** rutan.
6. Klicka på en marketplace-objekt om du vill visa detaljerad information.
7. Klicka på **skapa** att öppna den **Lägg till lösning** fönstret.
8. Du uppmanas att nödvändig information som den [logganalys-arbetsytan och Automation-konto](#log-analytics-workspace-and-automation-account) förutom värden för parametrar i lösningen.
9. Klicka på **skapa** att installera lösningen.

### <a name="oms-portal"></a>OMS-portalen
Lösningar som tillhandahålls av Microsoft kan installeras från galleriet lösningar i OMS-portalen.

1. Logga in på OMS-portalen.
2. Klicka på den **lösningar galleriet** panelen.
3. Läs mer om varje tillgänglig lösning på sidan OMS lösningar Gallery. Klicka på namnet på den lösning som du vill lägga till.
4. På sidan för lösningen som du har valt visas detaljerad information om lösningen. Klicka på **Lägg till**.
5. En ny panel för lösningen som du har lagt till visas i översikten sida i portalen och du kan börja använda det när logganalys bearbetar dina data.

### <a name="azure-quickstart-templates"></a>Azure-snabbstartmallar
Medlemmar i gruppen kan skicka hanteringslösningar till Azure Quickstart mallar.  Du kan hämta dessa mallar för senare installation eller inspektera dem att lära dig hur du [skapa egna lösningar](#creating-a-solution).

1. Följ processen som beskrivs i [logganalys-arbetsytan och Automation-konto](#log-analytics-workspace-and-automation-account) att länka en arbetsyta och kontot.
2. Gå till [Azure Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).  
3. Sök efter en lösning som du är intresserad av.
4. Välj lösningen resultat att visa information.
5. Klicka på den **till Azure** knappen.
6. Du uppmanas att ange information, till exempel resursgrupp och plats förutom värden för alla parametrar i lösningen.
7. Klicka på **inköp** installera lösningen.

### <a name="deploy-azure-resource-manager-template"></a>Distribuera Azure Resource Manager-mall
Lösningar som du får från gemenskapen eller som du [skapar själv](#creating-a-solution) implementeras som en Resource Manager-mall, och du kan använda någon av metoderna standard för [distribuerar en mall](../azure-resource-manager/resource-group-template-deploy-portal.md).  Observera att innan du installerar lösningen måste du skapa och länka den [logganalys-arbetsytan och Automation-konto](#log-analytics-workspace-and-automation-account).

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-arbetsyta och Automation-konto
De flesta lösningar kräver en [logganalys-arbetsytan](../log-analytics/log-analytics-manage-access.md) innehåller vyer och en [Automation-konto](../automation/automation-security-overview.md#automation-account-overview) ska innehålla runbooks och relaterade resurser. Arbetsytan och kontot måste uppfylla följande krav.

* En lösning kan bara använda en logganalys-arbetsytan och en Automation-konto.  
* Logganalys-arbetsytan och Automation-konto som används av en lösning måste länkas till varandra. Logganalys-arbetsytan kan bara kopplas till ett Automation-konto och ett Automation-konto kan bara kopplas till en logganalys-arbetsytan.
* Om du vill för att länka måste logganalys-arbetsytan och Automation-kontot vara i samma resursgrupp och region.  Undantaget är en arbetsyta i östra USA och och Automation-konto i östra USA 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Skapa en länk mellan en logganalys-arbetsytan och Automation-konto
Hur du anger logganalys-arbetsytan och Automation-konto beror på installationsmetoden för lösningen.

* När du installerar en Microsoft-lösning via OMS-portalen kan den installeras i den aktuella arbetsytan och inga Automation-konto krävs.
* När du installerar en lösning via Azure Marketplace du tillfrågas om en arbetsyta och Automation-konto och länken mellan dem skapas åt dig.  
* Lösningar utanför Azure Marketplace kan länka du logganalys-arbetsytan och Automation-konto innan du installerar lösningen.  Du kan göra detta genom att välja en lösning i Azure Marketplace och välja logganalys-arbetsytan och Automation-konto.  Du behöver installera lösningen faktiskt eftersom länken kommer att skapas så snart logganalys-arbetsytan och Automation-konto har valts.  När länken har skapats kan du använda den logganalys-arbetsytan och Automation-kontot för alla lösningar. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifiering av länken mellan en logganalys-arbetsytan och Automation-konto
Du kan kontrollera länken mellan logganalys-arbetsytan och ett Automation-konto med hjälp av följande procedur.

1. Välj Automation-kontot i Azure-portalen.
2. Om den **arbetsytan** i den **relaterade resurser** på menyn är aktiverat och det här kontot är ansluten till logganalys-arbetsytan.  Du kan klicka på **arbetsytan** att visa detaljer för arbetsytan.

## <a name="listing-management-solutions"></a>Visar en lista över lösningar för hantering
Använd följande procedur till att visa hanteringslösningarna i arbetsytorna länkad till din Azure-prenumeration.

1. Logga in på Azure Portal.
2. I den vänstra rutan, Välj **fler tjänster**.
3. Antingen rulla ned till **lösningar** eller typ *lösningar* till den **Filter** dialogrutan.
4. Lösningar som installerats i alla arbetsytor visas.

Observera att du kan visa endast de Microsoft-lösningar som installerats i den aktuella arbetsytan med hjälp av OMS-portalen.

## <a name="removing-a-management-solution"></a>Ta bort en lösning
När en hanteringslösning tas bort tas även alla resurser i lösningen bort.  

1. Leta reda på lösningen i Azure-portalen med hjälp av proceduren i [lista lösningar](#listing-solutions).
2. Markera den lösning som du vill ta bort.
3. Klicka på den **ta bort** knappen.

## <a name="creating-a-management-solution"></a>Skapa en lösning
Fullständig information om att skapa lösningar finns på [och skapa lösningar i Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Nästa steg
* Sök [Azure Quickstart mallar](https://azure.microsoft.com/documentation/templates) exempel på olika Resource Manager-mallar.
* Skapa en egen [hanteringslösningar](operations-management-suite-solutions-creating.md).

