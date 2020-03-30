---
title: Synpunkter på förvaltningslösningar | Microsoft-dokument
description: 'Hanteringslösningar innehåller vanligtvis en eller flera vyer för att visualisera data.  I den här artikeln beskrivs hur du exporterar en vy som skapats av Vydesignern och inkludera den i en hanteringslösning. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663053"
---
# <a name="views-in-management-solutions-preview"></a>Vyer i hanteringslösningar (förhandsversion)
> [!NOTE]
> Detta är preliminär dokumentation för att skapa hanteringslösningar som för närvarande förhandsgranskas. Alla scheman som beskrivs nedan kan komma att ändras.    


[Hanteringslösningar](solutions.md) innehåller vanligtvis en eller flera vyer för att visualisera data.  I den här artikeln beskrivs hur du exporterar en vy som skapats av [Vydesignern](../../azure-monitor/platform/view-designer.md) och inkludera den i en hanteringslösning.  

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen krävs eller är gemensamma för hanteringslösningar och beskrivs i [Design och bygg en hanteringslösning i Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med hur du [skapar en hanteringslösning](solutions-creating.md) och strukturen för en lösningsfil.

## <a name="overview"></a>Översikt
Om du vill inkludera en vy i en hanteringslösning skapar du en **resurs** för den i [lösningsfilen](solutions-creating.md).  Den JSON som beskriver vyns detaljerade konfiguration är vanligtvis komplex men och inte något som en typisk lösningsförfattare skulle kunna skapa manuellt.  Den vanligaste metoden är att skapa vyn med [Vydesignern](../../azure-monitor/platform/view-designer.md), exportera den och sedan lägga till den detaljerade konfigurationen i lösningen.

De grundläggande stegen för att lägga till en vy i en lösning är följande.  Varje steg beskrivs närmare i avsnitten nedan.

1. Exportera vyn till en fil.
2. Skapa vyresursen i lösningen.
3. Lägg till vyinformationen.

## <a name="export-the-view-to-a-file"></a>Exportera vyn till en fil
Följ instruktionerna på [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) för att exportera en vy till en fil.  Den exporterade filen kommer att vara i JSON-format med samma [element som lösningsfilen](solutions-solution-file.md).  

**Resurselementet** i vyfilen har en resurs med en typ av **Microsoft.OperationalInsights/workspaces** som representerar Log Analytics-arbetsytan.  Det här elementet har ett underelement med en typ av **vyer** som representerar vyn och innehåller dess detaljerade konfiguration.  Du kommer att kopiera detaljerna i detta element och sedan kopiera den till din lösning.

## <a name="create-the-view-resource-in-the-solution"></a>Skapa vyresursen i lösningen
Lägg till följande vyresurs i **resurselementet** i lösningsfilen.  Detta använder variabler som beskrivs nedan som du också måste lägga till.  Observera att egenskaperna **Instrumentpanel** och **ÖversiktTil** är platshållare som du ska skriva över med motsvarande egenskaper från den exporterade vyfilen.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Lägg till följande variabler i variabelelementet i lösningsfilen och ersätt värdena till värdena för din lösning.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observera att du kan kopiera hela vyresursen från den exporterade vyfilen, men du måste göra följande ändringar för att den ska fungera i din lösning.  

* **Typen** för vyresursen måste ändras från **vyer** till **Microsoft.OperationalInsights/workspaces**.
* Namnegenskapen för vyresursen måste ändras så att arbetsytans namn inkluderas. **name**
* Beroendet på arbetsytan måste tas bort eftersom arbetsytans resurs inte har definierats i lösningen.
* **Egenskapen DisplayName** måste läggas till i vyn.  **Id,** **Namn**och **DisplayName** måste alla matchas.
* Parameternamn måste ändras för att matcha den obligatoriska uppsättningen parametrar.
* Variabler bör definieras i lösningen och användas i lämpliga egenskaper.

### <a name="log-analytics-api-version"></a>Api-version för logganalys
Alla Log Analytics-resurser som definierats i en Resource Manager-mall har en egenskap **apiVersion** som definierar den version av API:et som resursen ska använda.  Den här versionen är annorlunda för vyer med frågor som använder det [äldre och det uppgraderade frågespråket](../../azure-monitor/log-query/log-query-overview.md).  

 I följande tabell anges API-versionerna för Logganalys för vyer i äldre och uppgraderade arbetsytor: 

| Arbetsyteversion | API-version | Söka i data |
|:---|:---|:---|
| v1 (äldre)   | 2015-11-01-förhandsvisning | Äldre format.<br> Exempel: Type=Event EventLevelName = Fel  |
| v2 (uppgraderad) | 2015-11-01-förhandsvisning | Äldre format.  Konverteras till uppgraderat format vid installation.<br> Exempel: Type=Event EventLevelName = Fel<br>Konverterad till: Händelse &#124; där EventLevelName == "Fel"  |
| v2 (uppgraderad) | 2017-03-03-förhandsvisning | Uppgradera format. <br>Exempel: Händelse &#124; där EventLevelName == "Fel"  |


## <a name="add-the-view-details"></a>Lägga till vyinformation
Vyresursen i den exporterade vyfilen innehåller två element i **egenskapselementet** **Dashboard** och **OverviewTile** som innehåller den detaljerade konfigurationen av vyn.  Kopiera dessa två element och deras innehåll till **egenskapselementet** för vyresursen i lösningsfilen.

## <a name="example"></a>Exempel
I följande exempel visas till exempel en enkel lösningsfil med en vy.  Ellipser (...) visas för **dashboard-** och **overviewTile-innehållet** av utrymmesskäl.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Nästa steg
* Lär dig fullständig information om hur du skapar [hanteringslösningar](solutions-creating.md).
* Inkludera [Automation-runbooks i hanteringslösningen](solutions-resources-automation.md).
