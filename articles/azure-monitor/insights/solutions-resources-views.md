---
title: Vyer i lösningar för hantering av | Microsoft Docs
description: 'Hanteringslösningar har vanligtvis vyer en eller flera om du vill visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapats av Vydesigner och inkludera den i en lösning. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: 4bd8e4ea347c1b26cba831317bdc1d837701788b
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107403"
---
# <a name="views-in-management-solutions-preview"></a>Vyer i lösningar för hantering (förhandsversion)
> [!NOTE]
> Det här är preliminära dokumentationen för att skapa lösningar för hantering som för närvarande i förhandsversion. Ett schema som beskrivs nedan kan komma att ändras.    


[Lösningar för hantering av](solutions.md) inkluderar vanligtvis en eller flera vyer för att visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapats av den [Vydesigner](../../azure-monitor/platform/view-designer.md) och inkludera den i en lösning.  

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med hur du [skapa en lösning för](solutions-creating.md) och strukturen för en lösningsfil.

## <a name="overview"></a>Översikt
För att inkludera en vy i en lösning för hantering, skapar du en **resource** för den i den [lösningsfilen](solutions-creating.md).  Den JSON som beskriver den visa detaljerad konfiguration är komplex men och inte något att en typisk lösning författare skulle kunna skapa manuellt.  Den vanligaste metoden är att skapa en vy med hjälp av den [Vydesigner](../../azure-monitor/platform/view-designer.md), exportera det och sedan lägga till den detaljerade konfigurationen i lösningen.

De grundläggande stegen för att lägga till en vy i en lösning är som följer.  Varje steg beskrivs mer ingående i avsnitten nedan.

1. Exportera vyn till en fil.
2. Skapa vy resursen i lösningen.
3. Lägg till Visa information.

## <a name="export-the-view-to-a-file"></a>Exportera vyn till en fil
Följ anvisningarna på [Log Analytics-Vydesigner](../../azure-monitor/platform/view-designer.md) att exportera en vy till en fil.  Den exporterade filen kommer att i JSON-format med samma [element som lösningsfilen](solutions-solution-file.md).  

Den **resurser** element i Vyfilen har en resurs med en typ av **Microsoft.OperationalInsights/workspaces** som representerar Log Analytics-arbetsytan.  Det här elementet har ett underelement med en typ av **vyer** som representerar vyn och innehåller detaljerade konfigurationen.  Du kopierar informationen på det här elementet och kopiera den till din lösning.

## <a name="create-the-view-resource-in-the-solution"></a>Skapa vy resursen i lösningen
Lägg till följande vy resursen till den **resurser** element i din lösningsfilen.  Här används variabler som beskrivs nedan att du måste också lägga till.  Observera att den **instrumentpanelen** och **OverviewTile** egenskaper är platshållare som skrivs med motsvarande egenskaper från den exporterade visa filen.

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

Lägg till följande variabler i elementet variabler i lösningsfilen och ersätter värdena som dem för din lösning.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observera att du kan kopiera hela vyn resursen från den exporterade visa filen, men behöver du göra följande ändringar att fungera i din lösning.  

* Den **typ** för vyn resurs ska ändras från **vyer** till **Microsoft.OperationalInsights/workspaces**.
* Den **namn** för visa-resursen måste ändras för att inkludera namnet på arbetsytan.
* Beroende på arbetsytans måste tas bort eftersom den arbetsyteresursen inte är definierad i lösningen.
* **DisplayName** egenskapen måste läggas till vyn.  Den **Id**, **namn**, och **DisplayName** måste matcha alla.
* Parameternamn måste ändras för att matcha de obligatoriska parametrar.
* Variabler ska definieras i lösningen och används i egenskaperna.

### <a name="log-analytics-api-version"></a>Log Analytics-API-version
Alla Log Analytics-resurser som definierats i en Resource Manager-mallen har en egenskap **apiVersion** som definierar versionen av API: et som resursen ska använda.  Den här versionen är olika för vyer med frågor som använder den [äldre system och det uppgraderade frågespråket](../../azure-monitor/log-query/log-query-overview.md).  

 I följande tabell anger Log Analytics API-versioner för vyer i äldre och uppgraderade arbetsytor: 

| Arbetsyteversion | API-version | Söka i data |
|:---|:---|:---|
| V1 (äldre)   | 2015-11-01-preview | Äldre format.<br> Exempel: Typ = händelse EventLevelName = fel  |
| v2 (uppgraderade) | 2015-11-01-preview | Äldre format.  Konverteras till uppgraderade format vid installation.<br> Exempel: Typ = händelse EventLevelName = fel<br>Konverteras till: Händelsen &#124; där EventLevelName == ”Error”  |
| v2 (uppgraderade) | 2017-03-03-förhandsversion | Uppgradera format. <br>Exempel: Händelsen &#124; där EventLevelName == ”Error”  |


## <a name="add-the-view-details"></a>Lägg till Visa detaljer
Visa resurs i den exporterade Visa fil innehåller två element i den **egenskaper** element med namnet **instrumentpanelen** och **OverviewTile** som innehåller detaljerade konfiguration av vyn.  Kopiera dessa två element och deras innehåll till den **egenskaper** element i Visa-resurs i din lösningsfilen.

## <a name="example"></a>Exempel
I följande exempel visas till exempel en enkel lösning-fil med en vy.  Ellipserna (...) visas för den **instrumentpanelen** och **OverviewTile** innehållet utrymme skäl.

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
* Lär dig mer information för att skapa [hanteringslösningar](solutions-creating.md).
* Inkludera [Automation-runbooks i din lösning för](solutions-resources-automation.md).
