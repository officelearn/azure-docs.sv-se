---
title: Skapa en hanteringslösning i Azure | Microsoft-dokument
description: Hanteringslösningar omfattar paketerade hanteringsscenarier i Azure som kunder kan lägga till i sin Log Analytics-arbetsyta.  Den här artikeln innehåller information om hur du kan skapa hanteringslösningar som ska användas i din egen miljö eller göras tillgängliga för dina kunder.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663223"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Designa och skapa en hanteringslösning i Azure (förhandsversion)
> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar i Azure som för närvarande är i förhandsversion. Alla scheman som beskrivs nedan kan komma att ändras.

[Hanteringslösningar]( solutions.md) tillhandahåller paketerade hanteringsscenarier som kunder kan lägga till i sin Log Analytics-arbetsyta.  Den här artikeln presenterar en grundläggande process för att utforma och bygga en hanteringslösning som är lämplig för de vanligaste kraven.  Om du är ny på bygghanteringslösningar kan du använda den här processen som utgångspunkt och sedan utnyttja begreppen för mer komplexa lösningar när dina krav utvecklas.

## <a name="what-is-a-management-solution"></a>Vad är en hanteringslösning?

Hanteringslösningar innehåller Azure-resurser som arbetar tillsammans för att uppnå ett visst hanteringsscenario.  De implementeras som [Resource Management-mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) som innehåller information om hur du installerar och konfigurerar sina inneslutna resurser när lösningen installeras.

Den grundläggande strategin är att starta din hanteringslösning genom att skapa de enskilda komponenterna i din Azure-miljö.  När du har funktionen fungerar korrekt, då kan du börja paketera dem i en [hanteringslösning fil]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Designa din lösning
Det vanligaste mönstret för en hanteringslösning visas i följande diagram.  De olika komponenterna i detta mönster diskuteras nedan.

![Översikt över hanteringslösningar](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Datakällor
Det första steget i utformningen av en lösning är att bestämma vilka data du behöver från Log Analytics-databasen.  Dessa data kan samlas in av en [datakälla](../../azure-monitor/platform/agent-data-sources.md) eller [en annan lösning,]( solutions.md)eller din lösning kan behöva tillhandahålla processen för att samla in den.

Det finns ett antal sätt datakällor som kan samlas in i Log Analytics-databasen enligt beskrivningen i [Datakällor i Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Detta inkluderar händelser i Windows-händelseloggen eller som genereras av Syslog utöver prestandaräknare för både Windows- och Linux-klienter.  Du kan också samla in data från Azure-resurser som samlas in av Azure Monitor.  

Om du behöver data som inte är tillgängliga via någon av de tillgängliga datakällorna kan du använda [HTTP-datainsamlare-API:et](../../azure-monitor/platform/data-collector-api.md) som gör att du kan skriva data till Log Analytics-databasen från alla klienter som kan anropa ett REST API.  Det vanligaste sättet att samla in anpassade data i en hanteringslösning är att skapa en [runbook i Azure Automation](../../automation/automation-runbook-types.md) som samlar in nödvändiga data från Azure eller externa resurser och använder Data Collector API för att skriva till databasen.  

### <a name="log-searches"></a>Logga sökningar
[Loggsökningar](../../azure-monitor/log-query/log-query-overview.md) används för att extrahera och analysera data i Log Analytics-databasen.  De används av vyer och aviseringar förutom att tillåta användaren att utföra ad hoc-analys av data i databasen.  

Du bör definiera alla frågor som du tror kommer att vara till hjälp för användaren även om de inte används av några vyer eller aviseringar.  Dessa är tillgängliga för dem som sparade sökningar i portalen, och du kan också inkludera dem i en [lista över frågor visualisering del](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) i din anpassade vy.

### <a name="alerts"></a>Aviseringar
[Aviseringar i Logganalys](../../azure-monitor/platform/alerts-overview.md) identifierar problem genom [loggsökningar](#log-searches) mot data i databasen.  De meddelar antingen användaren eller kör automatiskt en åtgärd som svar. Du bör identifiera olika varningsvillkor för ditt program och inkludera motsvarande varningsregler i lösningsfilen.

Om problemet eventuellt kan korrigeras med en automatiserad process, skapar du vanligtvis en runbook i Azure Automation för att utföra den här reparationen.  De flesta Azure-tjänster kan hanteras med [cmdlets](/powershell/azure/overview) som runbooken skulle utnyttja för att utföra sådana funktioner.

Om din lösning kräver externa funktioner som svar på en avisering kan du använda ett [webhook-svar](../../azure-monitor/platform/alerts-metric.md).  På så sätt kan du ringa en extern webbtjänst som skickar information från aviseringen.

### <a name="views"></a>Vyer
Vyer i Logganalys används för att visualisera data från Log Analytics-databasen.  Varje lösning innehåller vanligtvis en enda vy med en [panel](../../azure-monitor/platform/view-designer-tiles.md) som visas på användarens huvudinstrumentpanel.  Vyn kan innehålla valfritt antal [visualiseringsdelar](../../azure-monitor/platform/view-designer-parts.md) för att ge olika visualiseringar av insamlade data till användaren.

Du [skapar anpassade vyer med vydesignern](../../azure-monitor/platform/view-designer.md) som du senare kan exportera för att inkluderas i lösningsfilen.  


## <a name="create-solution-file"></a>Skapa lösningsfil
När du har konfigurerat och testat de komponenter som ska ingå i din lösning kan du [skapa lösningsfilen]( solutions-solution-file.md).  Du implementerar lösningskomponenterna i en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) som innehåller en [lösningsresurs]( solutions-solution-file.md#solution-resource) med relationer till de andra resurserna i filen.  


## <a name="test-your-solution"></a>Testa din lösning
Medan du utvecklar din lösning måste du installera och testa den på arbetsytan.  Du kan göra detta med någon av de tillgängliga metoderna för att [testa och installera Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publicera din lösning
När du har slutfört och testat din lösning kan du göra den tillgänglig för kunder via antingen följande källor.

- **Azure Snabbstartsmallar**.  [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/) är en uppsättning Resource Manager-mallar som communityn har bidragit med via GitHub.  Du kan göra din lösning tillgänglig genom att följa informationen i [bidragsguiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Med Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) kan du distribuera och sälja din lösning till andra utvecklare, ISV och IT-proffs.  Du kan läsa om hur du publicerar din lösning på Azure Marketplace på [Hur du publicerar och hanterar ett erbjudande på Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar en lösningsfil]( solutions-solution-file.md) för hanteringslösningen.
* Lär dig mer om [hur du skapar Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).
* Sök i [Azure Quickstart-mallar](https://azure.microsoft.com/documentation/templates) efter exempel på olika Resource Manager-mallar.
