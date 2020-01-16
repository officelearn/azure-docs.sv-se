---
title: Bygg en hanterings lösning i Azure | Microsoft Docs
description: Hanterings lösningar omfattar paketlösningar för hantering av paket i Azure som kunder kan lägga till i sin Log Analytics-arbetsyta.  Den här artikeln innehåller information om hur du kan skapa hanterings lösningar som ska användas i din egen miljö eller som görs tillgängliga för dina kunder.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5465c177cf174ebf8d6b7d4f43c5387bce3adb70
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969704"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Utforma och skapa en hanterings lösning i Azure (för hands version)
> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar i Azure som för närvarande är en för hands version. Alla scheman som beskrivs nedan kan komma att ändras.

[Hanterings lösningar]( solutions.md) tillhandahåller paketlösningar för hantering av paket som kunder kan lägga till i sin Log Analytics-arbetsyta.  Den här artikeln innehåller en grundläggande process för att utforma och skapa en hanterings lösning som är lämplig för de flesta vanliga kraven.  Om du är nybörjare på att skapa hanterings lösningar kan du använda den här processen som en start punkt och sedan använda koncepten för mer komplexa lösningar när dina krav utvecklas.

## <a name="what-is-a-management-solution"></a>Vad är en hanterings lösning?

Hanterings lösningar innehåller Azure-resurser som arbetar tillsammans för att uppnå ett visst hanterings scenario.  De är implementerade som [resurs hanterings mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) som innehåller information om hur du installerar och konfigurerar de resurser som finns när lösningen installeras.

Den grundläggande strategin är att starta hanterings lösningen genom att skapa enskilda komponenter i din Azure-miljö.  När funktionen fungerar som den ska kan du börja paketera dem i en [hanterings lösnings fil]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Utforma din lösning
Det vanligaste mönstret för en hanterings lösning visas i följande diagram.  De olika komponenterna i det här mönstret beskrivs i nedan.

![Översikt över hanterings lösning](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Datakällor
Det första steget i att designa en lösning är att bestämma vilka data du behöver från Log Analytics-lagringsplatsen.  Dessa data kan samlas in av en [data källa](../../azure-monitor/platform/agent-data-sources.md) eller [en annan lösning]( solutions.md), eller så kan din lösning behöva ange processen för att samla in den.

Det finns ett antal olika sätt data källor som kan samlas in i Log Analytics-lagringsplatsen som beskrivs i [data källor i Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Detta inkluderar händelser i Windows-händelseloggen eller som skapats av syslog förutom prestanda räknare för både Windows-och Linux-klienter.  Du kan också samla in data från Azure-resurser som samlas in av Azure Monitor.  

Om du behöver data som inte är tillgängliga via någon av de tillgängliga data källorna kan du använda [API: et för http-data insamling](../../azure-monitor/platform/data-collector-api.md) som gör att du kan skriva data till Log Analytics-lagringsplatsen från alla klienter som kan anropa en REST API.  Det vanligaste sättet för anpassad data insamling i en hanterings lösning är att skapa en [Runbook i Azure Automation](../../automation/automation-runbook-types.md) som samlar in nödvändiga data från Azure eller externa resurser och använder data insamlings-API: et för att skriva till databasen.  

### <a name="log-searches"></a>Loggs ökningar
[Loggs ökningar](../../azure-monitor/log-query/log-query-overview.md) används för att extrahera och analysera data i Log Analytics-lagringsplatsen.  De används av vyer och aviseringar förutom för att tillåta att användaren utför ad hoc-analys av data i databasen.  

Du bör definiera alla frågor som du tror kommer att vara användbara för användaren även om de inte används av några vyer eller varningar.  De är tillgängliga för dem som sparade sökningar i portalen och du kan även ta med dem i en [lista över frågor visualiserings del](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) i din anpassade vy.

### <a name="alerts"></a>Aviseringar
[Aviseringar i Log Analytics](../../azure-monitor/platform/alerts-overview.md) identifiera problem genom [loggs ökningar](#log-searches) mot data i databasen.  De meddelar antingen användaren eller kör automatiskt en åtgärd som svar. Du bör identifiera olika aviserings villkor för programmet och inkludera motsvarande varnings regler i lösnings filen.

Om problemet kan åtgärdas med en automatiserad process kommer du vanligt vis att skapa en Runbook i Azure Automation för att utföra den här reparationen.  De flesta Azure-tjänster kan hanteras med [cmdletar](/powershell/azure/overview) som Runbook skulle utnyttja för att utföra sådana funktioner.

Om din lösning kräver extern funktionalitet som svar på en avisering kan du använda ett [webhook-svar](../../azure-monitor/platform/alerts-metric.md).  På så sätt kan du anropa en extern webb tjänst som skickar information från aviseringen.

### <a name="views"></a>Vyer
Vyer i Log Analytics används för att visualisera data från Log Analytics-lagringsplatsen.  Varje lösning innehåller vanligt vis en enda vy med en [panel](../../azure-monitor/platform/view-designer-tiles.md) som visas på användarens huvud instrument panel.  Vyn kan innehålla valfritt antal [visualiserings delar](../../azure-monitor/platform/view-designer-parts.md) för att ge olika visualiseringar av insamlade data till användaren.

Du kan [skapa anpassade vyer med hjälp av View Designer](../../azure-monitor/platform/view-designer.md) som du senare kan exportera för att inkludera i lösnings filen.  


## <a name="create-solution-file"></a>Skapa lösnings fil
När du har konfigurerat och testat komponenterna som ska ingå i din lösning kan du [skapa en lösnings fil]( solutions-solution-file.md).  Du kommer att implementera lösnings komponenterna i en [Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) som innehåller en [lösnings resurs]( solutions-solution-file.md#solution-resource) med relationer till de andra resurserna i filen.  


## <a name="test-your-solution"></a>Testa din lösning
Medan du utvecklar din lösning måste du installera och testa den i din arbets yta.  Du kan göra detta med någon av de tillgängliga metoderna för att [testa och installera Resource Manager-mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publicera din lösning
När du har slutfört och testat lösningen kan du göra den tillgänglig för kunder via antingen följande källor.

- **Azure snabb starts mallar**.  [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) är en uppsättning Resource Manager-mallar som communityn har bidragit via GitHub.  Du kan göra din lösning tillgänglig genom att följa informationen i [bidrags hand boken](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  Med [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) kan du distribuera och sälja din lösning till andra utvecklare, ISV: er och IT-proffs.  Du kan lära dig hur du publicerar din lösning på Azure Marketplace när du [publicerar och hanterar ett erbjudande på Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar en lösnings fil]( solutions-solution-file.md) för din hanterings lösning.
* Läs mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).
* Sök i [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates) efter exempel på olika Resource Manager-mallar.
