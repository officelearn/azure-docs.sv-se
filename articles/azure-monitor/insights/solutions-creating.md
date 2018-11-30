---
title: Skapa en lösning i Azure | Microsoft Docs
description: I hanteringslösningarna ingår paketerade hanteringsscenarier i Azure som kunder kan lägga till sina Log Analytics-arbetsyta.  Den här artikeln innehåller information om hur du kan skapa lösningar för hantering som ska användas i din egen miljö eller göras tillgängligt för dina kunder.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4fd36d58574b60e3e6351cba03c68b9217bc703d
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632474"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Utforma och skapa en lösning i Azure (förhandsversion)
> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering i Azure som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.

[Lösningar för hantering av]( solutions.md) ger paketerade hanteringsscenarier som kunder kan lägga till sina Log Analytics-arbetsyta.  Den här artikeln beskriver vi en grundläggande process för att utforma och skapa en lösning som passar för de vanligaste krav.  Om du är nybörjare på att bygga lösningar för hantering kan du använda den här processen som en startpunkt och sedan utnyttja principerna för mer komplexa lösningar enligt dina behov utvecklas.

## <a name="what-is-a-management-solution"></a>Vad är en hanteringslösning för?

Lösningar för hantering av innehåller Azure-resurser som arbetar tillsammans för att uppnå ett visst hanteringsscenario.  De är implementerade som [mallar för resurshantering](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) som innehåller information om hur du installerar och konfigurerar sina inneslutna resurser när lösningen har installerats.

Den grundläggande strategin är att starta din lösning genom att skapa enskilda komponenter i Azure-miljön.  När du har funktionen fungerar korrekt kan du kan starta paketera dem till en [management lösningsfilen]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Utforma din lösning
De vanligaste mönstret som en lösning för illustreras i följande diagram.  De olika komponenterna i det här mönstret beskrivs i den nedan.

![Översikt över lösning för hantering](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Datakällor
Det första steget när du utformar en lösning är att fastställa de data som du behöver från Log Analytics-databasen.  Dessa data kan samlas in av en [datakälla](../../azure-monitor/platform/agent-data-sources.md) eller [en annan lösning]( solutions.md), eller din lösning kan behöva ange processen för att samla in den.

Det finns ett antal sätt datakällor som kan samlas in i Log Analytics-databasen enligt beskrivningen i [datakällor i Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Detta innefattar händelser till händelseloggen i Windows eller genereras av Syslog förutom prestandaräknare för både Windows och Linux-klienter.  Du kan också samla in data från Azure-resurser som samlas in av Azure Monitor.  

Om du kräver att data som inte kan nås via någon av de tillgängliga datakällorna så du kan använda den [HTTP Data Collector API](../../log-analytics/log-analytics-data-collector-api.md) där du kan skriva data till Log Analytics-databasen från alla klienter som kan anropa en REST-API.  Det vanligaste sättet för anpassad insamling i en lösning för hantering av är att skapa en [runbook i Azure Automation](../../automation/automation-runbook-types.md) som samlar in nödvändiga data från Azure eller externa resurser och använder Data Collector API för att skriva till den lagringsplats.  

### <a name="log-searches"></a>Loggsökningar
[Loggsökningar](../../log-analytics/log-analytics-queries.md) används för att extrahera och analysera data i Log Analytics-databasen.  De används i vyer och aviseringar förutom tillåter användare att utföra ad hoc-analyser av data i databasen.  

Du bör definiera alla frågor som du tror att användbara för användaren även om de inte används av alla vyer eller aviseringar.  Dessa är tillgängliga för dem som sparade sökningar i portalen och du kan även inkludera dem i en [Listfrågor för visualisering del](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) i den anpassade vyn.

### <a name="alerts"></a>Aviseringar
[Aviseringar i Log Analytics](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) identifiera problem med hjälp av [loggsökningar](#log-searches) mot data i databasen.  De meddela användaren eller köra automatiskt en åtgärd som svar. Du bör identifiera olika aviseringsvillkor för ditt program och inkludera motsvarande Varningsregler i din lösningsfilen.

Om problemet kan eventuellt åtgärdas med en automatiserad process, ska du vanligtvis skapa en runbook i Azure Automation för att utföra den här reparationen.  De flesta Azure-tjänster kan hanteras med [cmdletar](/powershell/azure/overview) som runbook skulle använda för att utföra dessa funktioner.

Om din lösning kräver externa funktioner som svar på en avisering så kan du använda en [webhook-svar](../../monitoring-and-diagnostics/alert-metric.md).  På så sätt kan du anropa en extern webbtjänst skickar information från aviseringen.

### <a name="views"></a>Vyer
Vyer i Log Analytics används för att visualisera data från Log Analytics-databasen.  Varje lösning vanligtvis innehåller en enda vy med en [panelen](../../azure-monitor/platform/view-designer-tiles.md) som visas på användarens huvudinstrumentpanel.  Vyn kan innehålla valfritt antal [visualisering delar](../../azure-monitor/platform/view-designer-parts.md) att tillhandahålla olika visualiseringar av insamlade data för användaren.

Du [skapa anpassade vyer med hjälp av Vydesigner](../../azure-monitor/platform/view-designer.md) som du senare kan exportera ska ingå i din lösningsfilen.  


## <a name="create-solution-file"></a>Skapa lösningsfil
När du har konfigurerat och testas de komponenter som ska ingå i din lösning, kan du [skapa din lösningsfilen]( solutions-solution-file.md).  Du kommer att implementera lösningen komponenterna i en [Resource Manager-mall](../../azure-resource-manager/resource-group-authoring-templates.md) som innehåller en [lösning resource]( solutions-solution-file.md#solution-resource) med relationer till andra resurser i filen.  


## <a name="test-your-solution"></a>Testa din lösning
När du utvecklar din lösning, behöver du installera och testa den i din arbetsyta.  Du kan göra detta med hjälp av någon av de tillgängliga metoderna att [testa och installera Resource Manager-mallar](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicera din lösning
När du har slutfört och testas din lösning kan gör du det tillgängligt för kunder via följande källor.

- **Azure-snabbstartsmallar**.  [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) är en uppsättning med Resource Manager-mallar som tillförts av communityn via GitHub.  Du kan tillgängliggöra din lösning med följande information i den [bidragsguiden](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  Den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) kan du distribuera och sälja din lösning till andra utvecklare, ISV: er, och IT-proffs.  Du kan lära dig hur du publicerar din lösning till Azure Marketplace på [att publicera och hantera ett erbjudande på Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa en lösningsfil]( solutions-solution-file.md) för din lösning.
* Mer information om [redigera Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md).
* Sök [Azure-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates) exempel på olika Resource Manager-mallar.
