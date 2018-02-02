---
title: "Skapa en lösning i Azure | Microsoft Docs"
description: "Lösningar för hantering med scenarier med hantering av paketerade i Azure som kunder kan lägga till sina logganalys-arbetsytan.  Den här artikeln innehåller information om hur du kan skapa lösningar för hantering som ska användas i din egen miljö eller göras tillgängligt för kunderna."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f5d42292c8e885491aed55ada129f05cb3bcd35
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Skapa en lösning i Azure (förhandsversion)
> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar för hantering i Azure som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.

[Hanteringslösningar](operations-management-suite-solutions.md) ange paketerade hanteringsscenarier som kunder kan lägga till sina logganalys-arbetsytan.  Den här artikeln beskriver en grundläggande process för att skapa en lösning som passar för de vanligaste krav.  Om du har använt till att skapa lösningar för hantering kan du använda den här processen som en startpunkt och sedan utnyttja begrepp för mer komplexa lösningar som dina behov utvecklas.

## <a name="what-is-a-management-solution"></a>Vad är en lösning?

Lösningar för hantering innehåller Azure-resurser som arbetar tillsammans för att uppnå ett visst hanteringsscenario.  De är implementerade som [resurshantering mallar](../azure-resource-manager/resource-manager-template-walkthrough.md) som innehåller information om hur du installerar och konfigurerar sina befintliga resurser när lösningen har installerats.

Basic-strategi är att starta din lösning för hantering genom att skapa de enskilda komponenterna i Azure-miljön.  När du har funktionen fungerar kan du kan starta paketera dem till en [management lösningsfilen](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Utforma din lösning
De vanligaste mönster som en lösning för visas i följande diagram.  De olika komponenterna i det här mönstret beskrivs i den nedan.

![Lösning: översikt](media/operations-management-suite-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Datakällor
Det första steget i utforma en lösning är att fastställa de data som du behöver från logganalys-databasen.  Dessa data kan samlas in av en [datakällan](../log-analytics/log-analytics-data-sources.md) eller [en annan lösning](operations-management-suite-solutions.md), eller din lösning kan behöva ange processen att samla in den.

Det finns ett antal sätt datakällor som kan samlas i logganalys-databasen enligt beskrivningen i [datakällor i logganalys](../log-analytics/log-analytics-data-sources.md).  Detta innefattar händelser i händelseloggen i Windows eller genereras av Syslog förutom prestandaräknare för både Windows- och Linux-klienter.  Du kan också samla in data från Azure-resurser som samlas in av Azure-Monitor.  

Om du behöver data som inte är tillgängligt via någon av de tillgängliga datakällorna, så du kan använda den [HTTP Data Collector API: et](../log-analytics/log-analytics-data-collector-api.md) där du kan skriva data till logganalys-databasen från klienter som kan anropa REST-API.  Det vanligaste sättet att anpassade datainsamling i en lösning är att skapa en [runbook i Azure Automation](../automation/automation-runbook-types.md) som samlar in nödvändiga data från Azure eller externa resurser och använder Data Collector API: et för att skriva till databasen.  

### <a name="log-searches"></a>Log-sökningar
[Logga sökningar](../log-analytics/log-analytics-log-searches.md) används för att extrahera och analysera data i logganalys-databasen.  De används i vyer och aviseringar förutom tillåter användaren att utföra ad hoc-analys av data i databasen.  

Du bör definiera frågor som du tror är användbar för användaren även om de inte används av alla vyer eller aviseringar.  Dessa är tillgängliga för dem som sparade sökningar i portalen och du kan även inkludera dem i en [listan frågor visualiseringen del](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) i den anpassade vyn.

### <a name="alerts"></a>Aviseringar
[Aviseringar i logganalys](../log-analytics/log-analytics-alerts.md) identifiera problem med hjälp av [logga sökningar](#log-searches) mot data i databasen.  De meddela användaren eller köra automatiskt en åtgärd som svar. Du bör identifiera olika avisering villkor för ditt program och inkludera motsvarande Varningsregler i din lösningsfilen.

Om problemet kan eventuellt åtgärdas med en automatiserad process, ska du vanligtvis skapa en runbook i Azure Automation för att utföra den här reparation.  De flesta Azure-tjänster kan hanteras med [cmdlets](/powershell/azure/overview) som runbook skulle använda för att utföra dessa funktioner.

Om din lösning kräver externa funktioner som svar på en avisering så att du kan använda en [webhook svar](../log-analytics/log-analytics-alerts-actions.md).  På så sätt kan du anropa en extern webbtjänst som skickar information från aviseringen.

### <a name="views"></a>Vyer
Vyer i logganalys används för att visualisera data från logganalys-databasen.  Varje lösning innehåller vanligtvis en enskild vy med en [panelen](../log-analytics/log-analytics-view-designer-tiles.md) som visas på användarens huvudinstrumentpanelen.  Vyn kan innehålla valfritt antal [visualiseringen delar](../log-analytics/log-analytics-view-designer-parts.md) att tillhandahålla olika visualiseringar av insamlade data till användaren.

Du [skapa anpassade vyer med hjälp av Vydesigner](../log-analytics/log-analytics-view-designer.md) som du kan exportera som ska ingå i din lösningsfilen senare.  


## <a name="create-solution-file"></a>Skapa lösningsfilen
När du har konfigurerat och testas de komponenter som ska ingå i din lösning, kan du [skapa din lösningsfilen](operations-management-suite-solutions-solution-file.md).  Du kommer att implementera komponenter i en [Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) som innehåller en [resurs](operations-management-suite-solutions-solution-file.md#solution-resource) med relationer till andra resurser i filen.  


## <a name="test-your-solution"></a>Testa din lösning
När du utvecklar din lösning behöver du installera och testa den i din arbetsyta.  Du kan göra detta med hjälp av de tillgängliga metoderna för [testa och installera Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicera din lösning
När du är klar och testat din lösning, kan du göra det tillgängligt för kunder via följande källor.

- **Azure-snabbstartsmallar**.  [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) är en uppsättning Resource Manager-mallar som har bidragit med gemenskapen via GitHub.  Du kan göra din lösning tillgängliga med följande information i den [bidrag guiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  Den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) kan du distribuera och sälja din lösning till andra utvecklare ISV: er, och IT-proffs.  Du kan lära dig hur du publicerar din lösning på Azure Marketplace på [att publicera och hantera ett erbjudande på Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa en lösningsfil](operations-management-suite-solutions-solution-file.md) för din lösning för hantering.
* Mer information om [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).
* Sök [Azure Quickstart mallar](https://azure.microsoft.com/documentation/templates) exempel på olika Resource Manager-mallar.