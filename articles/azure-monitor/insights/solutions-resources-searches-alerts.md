---
title: Sparade sökningar i hanteringslösningar | Microsoft Docs
description: I hanteringslösningarna ingår vanligtvis sparade sökningar i Log Analytics för att analysera data som samlas in av lösningen. Den här artikeln beskriver hur du definierar sparade sökningar i en Resource Manager-mall så att de kan ingå i lösningar för hantering av Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64b79d613463ac2097a89a8e3ca3870b885a3332
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985265"
---
# <a name="adding-log-analytics-saved-searches-to-management-solution-preview"></a>Att lägga till Log Analytics sparade sökningar till lösning för hantering (förhandsversion)

> [!IMPORTANT]
> En tidigare version av den här artikeln med information om hur du skapar en avisering med en Resource Manager-mall. Den här informationen är av datum nu när [Log Analytics-aviseringar har utökats till Azure Monitor](../platform/alerts-extend.md). Mer information om hur du skapar en avisering om log med en Resource Manager-mall finns i [Hantera aviseringar med hjälp av Azure-resursmall](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.

Den här artikeln beskriver hur du definierar sparade sökningar i Log Analytics en [Resource Manager-mall](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) så att de kan ingå i [hanteringslösningar](solutions-creating.md).

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure](solutions-creating.md)

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med hur du [skapa en lösning för](solutions-creating.md) och strukturen för en [Resource Manager-mall](../../azure-resource-manager/resource-group-authoring-templates.md) och lösningsfilen.


## <a name="log-analytics-workspace"></a>Log Analytics Workspace
Alla resurser i Log Analytics finns i en [arbetsytan](../../azure-monitor/platform/manage-access.md). Mer information finns i [Log Analytics-arbetsytan och Automation-kontot](solutions.md#log-analytics-workspace-and-automation-account), arbetsytan ingår inte i hanteringslösningen men måste finnas innan lösningen är installerad. Om den inte är tillgänglig misslyckas lösning installationen.

Namnet på arbetsytan är namnet på varje Log Analytics-resurs. Detta görs i lösningen med den **arbetsytan** parameter som i följande exempel för en resurs för SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics-API-version
Alla Log Analytics-resurser som definierats i en Resource Manager-mallen har en egenskap **apiVersion** som definierar versionen av API: et som resursen ska använda.

I följande tabell visas den API-versionen för resursen i det här exemplet.

| Resurstyp | API-version | Söka i data |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Händelsen &#124; där EventLevelName == ”Error”  |


## <a name="saved-searches"></a>Sparade sökningar
Inkludera [sparade sökningar](../../azure-monitor/log-query/log-query-overview.md) i en lösning för att tillåta användare att köra frågor mot data som samlas in av din lösning. Sparade sökningar visas under **sparade sökningar** i Azure-portalen. En sparad sökning krävs också för varje avisering.

[Sparade log Analytics-sökningen](../../azure-monitor/log-query/log-query-overview.md) resurser har en typ av `Microsoft.OperationalInsights/workspaces/savedSearches` och har följande struktur. Detta inkluderar vanliga variabler och parametrar så att du kan kopiera och klistra in det här kodfragmentet i dina lösningsfilen och ändra parameternamnen.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

I följande tabell beskrivs varje egenskap för en sparad sökning.

| Egenskap  | Beskrivning |
|:--- |:--- |
| category | Kategorin för den sparade sökningen.  Alla sparade sökningar i samma lösning kommer ofta att dela en enskild kategori så att de grupperas tillsammans i konsolen. |
| displayname (visningsnamn) | Namn som ska visas på den sparade sökningen i portalen. |
| DocumentDB | Frågan ska köras. |

> [!NOTE]
> Du kan behöva använda escape-tecken i frågan om den innehåller tecken som kan tolkas som JSON. Exempel: om din fråga var **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write”**, den måste skrivas i lösningsfilen som **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.


## <a name="next-steps"></a>Nästa steg
* [Lägga till vyer](solutions-resources-views.md) till din lösning.
* [Lägg till Automation-runbooks och andra resurser](solutions-resources-automation.md) till din lösning.
