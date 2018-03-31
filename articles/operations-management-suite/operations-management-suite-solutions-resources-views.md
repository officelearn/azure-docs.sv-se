---
title: Vyer i hanteringslösningar | Microsoft Docs
description: 'Hanteringslösningar inkluderar vanligtvis en eller flera vyer om du vill visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapats av Vydesigner och inkludera den i en lösning. '
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: b44763fe67b1c70c0b6ecdff73c32d8bb4fab3a4
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="views-in-management-solutions-preview"></a>Vyer i hanteringslösningar (förhandsgranskning)
> [!NOTE]
> Den här är dokumentationen preliminär för att skapa lösningar som för närvarande finns i förhandsgranskningen. Ett schema som beskrivs nedan kan ändras.    


[Hanteringslösningar](operations-management-suite-solutions.md) inkluderar vanligtvis en eller flera vyer om du vill visualisera data.  Den här artikeln beskriver hur du exporterar en vy som skapades av den [Vydesigner](../log-analytics/log-analytics-view-designer.md) och inkludera den i en lösning.  

> [!NOTE]
> Exemplen i den här artikeln använder parametrar och variabler som är obligatoriska eller vanligt att hanteringslösningar och beskrivs i [utforma och skapa en lösning i Azure](operations-management-suite-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan är bekant med [skapar en lösning för](operations-management-suite-solutions-creating.md) och strukturen för en lösningsfil.

## <a name="overview"></a>Översikt
Om du vill inkludera en vy i en lösning som du skapar en **resurs** för den i den [lösningsfilen](operations-management-suite-solutions-creating.md).  JSON som beskriver den visa detaljerad konfiguration är komplex men och inte något att en typisk lösning författare skulle kunna skapa manuellt.  Den vanligaste metoden är att skapa en vy med hjälp av den [Vydesigner](../log-analytics/log-analytics-view-designer.md), exportera den och sedan lägga till den detaljerade konfigurationen i lösningen.

De grundläggande stegen för att lägga till en vy i en lösning är som följer.  Varje steg beskrivs detaljerat i nedanstående avsnitt.

1. Exportera vyn till en fil.
2. Skapa vy resursen i lösningen.
3. Lägga till vyn Detaljer.

## <a name="export-the-view-to-a-file"></a>Exportera vyn till en fil
Följ anvisningarna på [Log Analytics Vydesigner](../log-analytics/log-analytics-view-designer.md) att exportera en vy till en fil.  Den exporterade filen kommer att i JSON-format med samma [element som lösningsfilen](operations-management-suite-solutions-solution-file.md).  

Den **resurser** element i filen vyn har en resurs med en typ av **Microsoft.OperationalInsights/workspaces** som representerar logganalys-arbetsytan.  Det här elementet har ett underelement av typen **vyer** som representerar vyn och innehåller detaljerade konfigurationen.  Du kan kopiera information om det här elementet och kopierar den till din lösning.

## <a name="create-the-view-resource-in-the-solution"></a>Skapa vy resursen i lösningen
Lägg till följande vy resursen till den **resurser** element i din lösningsfil.  Den använder variabler som beskrivs nedan som du måste också lägga till.  Observera att den **instrumentpanelen** och **OverviewTile** egenskaper är platshållare som kommer att skrivas över med motsvarande egenskaper från den exportera visa filen.

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

Lägg till följande variabler i elementet variabler i lösningsfilen och Ersätt värdena som de för din lösning.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Observera att du kan kopiera hela vyn resursen från din exporterade visa filen, men måste du göra följande ändringar att fungera i din lösning.  

* Den **typen** för vyn resurs ska ändras från **vyer** till **Microsoft.OperationalInsights/workspaces**.
* Den **namn** -egenskapen för resursen visa behöver ändras för att inkludera namnet på arbetsytan.
* Beroende på arbetsytan måste tas bort eftersom den arbetsyta resursen har inte definierats i lösningen.
* **DisplayName** egenskapen måste läggas till vyn.  Den **Id**, **namn**, och **DisplayName** måste matcha alla.
* Parameternamn måste ändras för att matcha uppsättningen parametrar som krävs.
* Variabler ska definieras i lösningen och används i lämpliga egenskaper.

### <a name="log-analytics-api-version"></a>Log Analytics API-version
Alla logganalys-resurser som definierats i en Resource Manager-mall har en egenskap **apiVersion** som definierar versionen av resursen ska använda API: et.  Den här versionen är olika för vyer med frågor som använder den [äldre och uppgraderade frågespråket](../log-analytics/log-analytics-log-search-upgrade.md).  

 I följande tabell anger Log Analytics API-versioner för vyer i äldre och uppgraderade arbetsytor: 

| Arbetsyteversion | API-version | Fråga |
|:---|:---|:---|
| V1 (äldre)   | 2015-11-01-preview | Äldre format.<br> Exempel: Skriv = händelse EventLevelName = fel  |
| v2 (uppgraderade) | 2015-11-01-preview | Äldre format.  Konvertera till uppgraderade format på installera.<br> Exempel: Skriv = händelse EventLevelName = fel<br>Konvertera till: händelsen &#124; där EventLevelName == ”Error”  |
| v2 (uppgraderade) | 2017-03-03-preview | Uppgradera format. <br>Exempel: Händelsen &#124; där EventLevelName == ”Error”  |


## <a name="add-the-view-details"></a>Lägg till Visa detaljer
Visa resursen i filen exporterade vyn innehåller två element i den **egenskaper** element med namnet **instrumentpanelen** och **OverviewTile** som innehåller den detaljerade konfiguration av vyn.  Kopiera dessa två element och innehållet i den **egenskaper** element i vyn resurs i din lösningsfilen.

## <a name="example"></a>Exempel
I följande exempel visar exempelvis en enkel lösning-fil med en vy.  Ellipserna (...) visas för den **instrumentpanelen** och **OverviewTile** innehållet utrymme skäl.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
* Lär dig mer detaljerad information om hur du skapar [hanteringslösningar](operations-management-suite-solutions-creating.md).
* Inkludera [Automation-runbooks i din lösning](operations-management-suite-solutions-resources-automation.md).
