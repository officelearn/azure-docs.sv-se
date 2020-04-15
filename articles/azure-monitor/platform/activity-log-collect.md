---
title: Samla in och analysera Azure-aktivitetsloggen i Azure Monitor
description: Samla in Azure Activity Log i Azure Monitor Logs och använd övervakningslösningen för att analysera och söka i Azure-aktivitetsloggen i alla dina Azure-prenumerationer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382857"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Samla in och analysera Azure Activity-loggen i Azure Monitor
[Azure Activity-loggen](platform-logs-overview.md) är en [plattformslogg](platform-logs-overview.md) som ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Även om du kan visa aktivitetsloggen i Azure-portalen bör du konfigurera den för att skicka till en Log Analytics-arbetsyta för att aktivera ytterligare funktioner i Azure Monitor. I den här artikeln beskrivs hur du utför den här konfigurationen och hur du skickar aktivitetsloggen till Azure-lagrings- och händelsehubbar.

Att samla in aktivitetsloggen på en log analytics-arbetsyta ger följande fördelar:

- Ingen datainmatning eller datalagringsavgift för aktivitetsloggdata som lagras på en Log Analytics-arbetsyta.
- Korrelera aktivitetsloggdata med andra övervakningsdata som samlas in av Azure Monitor.
- Använd loggfrågor för att utföra komplexa analyser och få djupa insikter om aktivitetsloggposter.
- Använd loggaviseringar med aktivitetsposter som möjliggör mer komplex varningslogik.
- Lagra aktivitetsloggposter längre än 90 dagar.
- Konsolidera loggposter från flera Azure-prenumerationer och klienter till en plats för analys tillsammans.

> [!IMPORTANT]
> Samla in loggar över klienter kräver [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Samla in aktivitetslogg
Aktivitetsloggen samlas in automatiskt för [visning i Azure-portalen](activity-log-view.md). Om du vill samla in den på en Log Analytics-arbetsyta eller skicka den till Azure-lagrings- eller händelsehubbar skapar du en [diagnostikinställning](diagnostic-settings.md). Detta är samma metod som används av resursloggar vilket gör det konsekvent för alla [plattformsloggar](platform-logs-overview.md).  

Om du vill skapa en diagnostikinställning för aktivitetsloggen väljer du **Diagnostikinställningar** på **aktivitetsloggmenyn** i Azure Monitor. Se [Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure](diagnostic-settings.md) för mer information om hur du skapar inställningen. Se [Kategorier i aktivitetsloggen](activity-log-view.md#categories-in-the-activity-log) för en beskrivning av de kategorier som du kan filtrera. Om du har några äldre inställningar måste du inaktivera dem innan du skapar en diagnostikinställning. Om båda har aktiverat kan det resultera i dubblettdata.

![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> För närvarande kan du bara skapa en diagnostikinställning på prenumerationsnivå med Azure-portalen och en Resource Manager-mall. 


## <a name="legacy-settings"></a>Äldre inställningar 
Även om diagnostikinställningar är den metod som föredras för att skicka aktivitetsloggen till olika mål, fortsätter äldre metoder att fungera om du inte väljer att ersätta med en diagnostikinställning. Diagnostikinställningarna har följande fördelar jämfört med äldre metoder och vi rekommenderar att du uppdaterar konfigurationen:

- Konsekvent metod för att samla in alla plattformsloggar.
- Samla in aktivitetslogg över flera prenumerationer och klienter.
- Filtrera insamling för att endast samla in loggar för vissa kategorier.
- Samla in alla aktivitetsloggkategorier. Vissa kategorier samlas inte in med äldre metod.
- Snabbare svarstid för logga inmatning. Den tidigare metoden har ca 15 minuters latens medan diagnostikinställningarna lägger till endast ca 1 minut.



### <a name="log-profiles"></a>Loggprofiler
Loggprofiler är äldre metod för att skicka aktivitetsloggen till Azure-lagring eller händelsehubbar. Använd följande procedur för att fortsätta arbeta med en loggprofil eller inaktivera den som förberedelse för migrering till en diagnostikinställning.

1. Välj **Aktivitetslogg**på **Azure Monitor-menyn** i Azure-portalen .
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för äldreupplevelsen.

    ![Äldre erfarenhet](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Den äldre metoden för att samla in aktivitetsloggen på en Log Analytics-arbetsyta ansluter loggen i arbetsytans konfiguration. 

1. På menyn **Logganalysarbetsytor** i Azure-portalen väljer du arbetsytan för att samla in aktivitetsloggen.
1. I avsnittet **Datakällor på arbetsytan** på arbetsytans meny väljer du **Azure Activity log**.
1. Klicka på den prenumeration som du vill ansluta.

    ![Arbetsytor](media/activity-log-collect/workspaces.png)

1. Klicka på **Anslut** om du vill ansluta aktivitetsloggen i prenumerationen till den valda arbetsytan. Om prenumerationen redan är ansluten till en annan arbetsyta klickar du på **Koppla från** först för att koppla från den.

    ![Ansluta arbetsytor](media/activity-log-collect/connect-workspace.png)


Om du vill inaktivera inställningen utför du samma procedur och klickar på **Koppla** för att ta bort prenumerationen från arbetsytan.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analysera aktivitetslogg i logganalysarbetsyta
När du ansluter en aktivitetslogg till en Log Analytics-arbetsyta skrivs poster till arbetsytan till en tabell som heter *AzureActivity* som du kan hämta med en [loggfråga](../log-query/log-query-overview.md). Tabellens struktur varierar beroende på [loggpostens kategori](activity-log-view.md#categories-in-the-activity-log). Se [Azure Activity Log händelseschema](activity-log-schema.md) för en beskrivning av varje kategori.


### <a name="data-structure-changes"></a>Förändringar i datastruktur
Diagnostikinställningar samlar in samma data som den äldre metoden som används för att samla in aktivitetsloggen med vissa ändringar i strukturen i tabellen *AzureActivity.*

Kolumnerna i följande tabell har inaktuellts i det uppdaterade schemat. De finns fortfarande i *AzureActivity* men de har inga data. Ersättningen för dessa kolumner är inte ny, men de innehåller samma data som den inaktuella kolumnen. De är i ett annat format, så du kan behöva ändra loggfrågor som använder dem. 

| Föråldrad kolumn | Ersättningskolumn |
|:---|:---|
| Aktivitetsstatus    | ActivityStatusValue    |
| AktivitetSubstatus | ActivitySubstatusVärdera |
| OperationName     | OperationNameVärde     |
| ResourceProvider  | ResursProviderVärde  |

> [!IMPORTANT]
> I vissa fall kan värdena i dessa kolumner vara i versaler. Om du har en fråga som innehåller dessa kolumner bör du använda [operatorn =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) för att göra en okänslig jämförelse.

Följande kolumn har lagts till i *AzureActivity* i det uppdaterade schemat:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Övervakningslösning för Aktivitetsloggar Analytics
Övervakningslösningen för Azure Log Analytics kommer snart att inaktiveras och ersättas av en arbetsbok med det uppdaterade schemat på logganalysarbetsytan. Du kan fortfarande använda lösningen om du redan har aktiverat den, men den kan bara användas om du samlar in aktivitetsloggen med äldre inställningar. 



### <a name="use-the-solution"></a>Använd lösningen
Övervakningslösningar nås från **Monitor-menyn** i Azure-portalen. Välj **Mer** i avsnittet **Insikter** om du vill öppna sidan **Översikt** med lösningspanelerna. Panelen **Azure Activity Logs** visar antalet **AzureActivity-poster** på arbetsytan.

![Panel för Azure-aktivitetsloggar](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på panelen **Azure-aktivitetsloggar** för att öppna vyn **Azure-aktivitetsloggar.** Vyn innehåller visualiseringsdelarna i följande tabell. Varje del listar upp till 10 objekt som matchar de delarnas villkor för det angivna tidsintervallet. Du kan köra en loggfråga som returnerar alla matchande poster genom att klicka på **Visa alla** längst ned i delen.

![Instrumentpanelen Azure Activity Logs](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Aktivera lösningen för nya prenumerationer
Du kommer snart inte längre att kunna lägga till aktivitetslogganalyslösningen i din prenumeration med Hjälp av Azure-portalen. Du kan lägga till den med hjälp av följande procedur med en resurshanterares mall. 

1. Kopiera följande json till en fil som heter *ActivityLogTemplate*.json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Distribuera mallen med följande PowerShell-kommandon:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitetsloggen](platform-logs-overview.md).
- Läs mer om [Azure Monitor-dataplattformen](data-platform.md).
- Använd [loggfrågor](../log-query/log-query-overview.md) för att visa detaljerad information från aktivitetsloggen.
