---
title: Vyer i hanterings lösningar | Microsoft Docs
description: 'Hanterings lösningar innehåller vanligt vis en eller flera vyer för att visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapats av View Designer och inkluderar den i en hanterings lösning. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663053"
---
# <a name="views-in-management-solutions-preview"></a>Vyer i hanterings lösningar (förhands granskning)
> [!NOTE]
> Det här är en preliminär dokumentation för att skapa hanterings lösningar som för närvarande finns i för hands version. Alla scheman som beskrivs nedan kan komma att ändras.    


[Hanterings lösningar](solutions.md) innehåller vanligt vis en eller flera vyer för att visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapats av [View Designer](../../azure-monitor/platform/view-designer.md) och inkluderar den i en hanterings lösning.  

> [!NOTE]
> I exemplen i den här artikeln används parametrar och variabler som antingen är obligatoriska eller vanliga för hanterings lösningar och som beskrivs i [utforma och skapa en hanterings lösning i Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan är bekant med hur du [skapar en hanterings lösning](solutions-creating.md) och strukturen för en lösnings fil.

## <a name="overview"></a>Översikt
Om du vill inkludera en vy i en hanterings lösning skapar du en **resurs** för den i [lösnings filen](solutions-creating.md).  JSON som beskriver vyns detaljerade konfiguration är vanligt vis komplex, men inte något som en typisk lösnings författare kan skapa manuellt.  Det vanligaste sättet är att skapa vyn med hjälp av [View Designer](../../azure-monitor/platform/view-designer.md), exportera den och sedan lägga till den detaljerade konfigurationen i lösningen.

De grundläggande stegen för att lägga till en vy i en lösning är som följer.  Varje steg beskrivs i detalj i avsnittet nedan.

1. Exportera vyn till en fil.
2. Skapa vyn resurs i lösningen.
3. Lägg till vyn information.

## <a name="export-the-view-to-a-file"></a>Exportera vyn till en fil
Följ instruktionerna på [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) för att exportera en vy till en fil.  Den exporterade filen är i JSON-format med samma [element som lösnings filen](solutions-solution-file.md).  

Resurs **elementet i** visnings filen har en resurs med en typ av **Microsoft. OperationalInsights/arbets ytor** som representerar arbets ytan Log Analytics.  Det här elementet har ett under element med en typ av **vyer** som representerar vyn och innehåller den detaljerade konfigurationen.  Du kommer att kopiera information om det här elementet och sedan kopiera det till din lösning.

## <a name="create-the-view-resource-in-the-solution"></a>Skapa vyn resurs i lösningen
Lägg till följande vy-resurs till **resurs elementet i** lösnings filen.  Detta använder variabler som beskrivs nedan. du måste också lägga till.  Observera att egenskaperna **Dashboard** och **OverviewTile** är plats hållare som du kommer att skriva över med motsvarande egenskaper från den exporterade View-filen.

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

Lägg till följande variabler i elementet variabler i lösnings filen och ersätt värdena med dem för din lösning.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observera att du kan kopiera hela vyn resurs från den exporterade View-filen, men du måste göra följande ändringar för att den ska fungera i din lösning.  

* **Typen** för vyn Visa resurs måste ändras från **vyer** till **Microsoft. OperationalInsights/arbets ytor**.
* **Namn** egenskapen för vyn Visa resurs måste ändras för att inkludera namnet på arbets ytan.
* Beroendet på arbets ytan måste tas bort eftersom arbets ytans resurs inte har definierats i lösningen.
* Egenskapen **DisplayName** måste läggas till i vyn.  **ID**, **namn**och **DisplayName** måste matcha.
* Parameter namn måste ändras för att matcha den obligatoriska parameter uppsättningen.
* Variabler bör definieras i lösningen och användas i lämpliga egenskaper.

### <a name="log-analytics-api-version"></a>Log Analytics API-version
Alla Log Analytics resurser som definierats i en Resource Manager-mall har en egenskaps- **API version** som definierar den version av API som resursen ska använda.  Den här versionen är annorlunda för vyer med frågor som använder det [äldre och det uppgraderade frågespråket](../../azure-monitor/log-query/log-query-overview.md).  

 I följande tabell anges Log Analytics API-versioner för vyer i äldre och uppgraderade arbets ytor: 

| Arbetsyte version | API-version | Söka i data |
|:---|:---|:---|
| v1 (bakåtkompatibelt)   | 2015-11-01 – för hands version | Äldre format.<br> Exempel: Type = Event EventLevelName = Error  |
| v2 (uppgraderat) | 2015-11-01 – för hands version | Äldre format.  Konverterat till uppgraderat format vid installation.<br> Exempel: Type = Event EventLevelName = Error<br>Konverterad till: händelse &#124; där EventLevelName = = "Error"  |
| v2 (uppgraderat) | 2017-03-03 – för hands version | Uppgraderings format. <br>Exempel: Event &#124; där EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>Lägg till vyn Detaljer
Vyn Visa resursen i den exporterade View-filen innehåller två element i **Properties** -elementet med namnet **instrument panel** och **OverviewTile** som innehåller den detaljerade konfigurationen av vyn.  Kopiera de här två elementen och deras innehåll till elementet **Properties** (Visa resurs) i din lösnings fil.

## <a name="example"></a>Exempel
Följande exempel visar till exempel en enkel lösnings fil med en vy.  Ellipser (...) visas för **instrument panelen** och **OverviewTile** -innehållet av utrymmes skäl.

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
* Läs mer om hur du skapar [hanterings lösningar](solutions-creating.md).
* Inkludera [Automation-runbooks i hanterings lösningen](solutions-resources-automation.md).
