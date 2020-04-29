---
title: Samla in och analysera Azure aktivitets logg i Azure Monitor
description: Samla in Azure aktivitets logg i Azure Monitor loggar och Använd övervaknings lösningen för att analysera och söka i Azure aktivitets loggen i alla dina Azure-prenumerationer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382857"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Samla in och analysera Azure aktivitets logg i Azure Monitor
[Azure aktivitets loggen](platform-logs-overview.md) är en [plattforms logg](platform-logs-overview.md) som ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Även om du kan visa aktivitets loggen i Azure Portal bör du konfigurera den så att den skickas till en Log Analytics arbets yta för att aktivera ytterligare funktioner i Azure Monitor. Den här artikeln beskriver hur du utför den här konfigurationen och hur du skickar aktivitets loggen till Azure Storage och Event Hub.

Att samla in aktivitets loggen på en Log Analytics arbets yta ger följande fördelar:

- Ingen data inmatning eller data lagrings avgift för aktivitets logg data som lagras i en Log Analytics-arbetsyta.
- Korrelera aktivitets logg data med andra övervaknings data som samlas in av Azure Monitor.
- Använd logg frågor för att utföra komplexa analyser och få djupgående insikter om aktivitets logg poster.
- Använd logg aviseringar med aktivitets poster som tillåter mer komplex aviserings logik.
- Lagra aktivitets logg poster under längre tid än 90 dagar.
- Konsolidera logg poster från flera Azure-prenumerationer och-klienter till en plats för analys av varandra.

> [!IMPORTANT]
> Att samla in loggar över klienter kräver [Azure-Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Samlar in aktivitets logg
Aktivitets loggen samlas in automatiskt för [visning i Azure Portal](activity-log-view.md). Skapa en [diagnostisk inställning](diagnostic-settings.md)om du vill samla in den på en Log Analytics arbets yta eller skicka den till Azure Storage eller Event Hub. Det här är samma metod som används av resurs loggar som gör den konsekvent för alla [plattforms loggar](platform-logs-overview.md).  

Om du vill skapa en diagnostisk inställning för aktivitets loggen väljer du **diagnostikinställningar** på menyn **aktivitets logg** i Azure Monitor. Mer information om hur du skapar inställningen finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](diagnostic-settings.md) . Se [Kategorier i aktivitets loggen](activity-log-view.md#categories-in-the-activity-log) för en beskrivning av de kategorier som du kan filtrera. Om du har äldre inställningar kontrollerar du att du inaktiverar dem innan du skapar en diagnostisk inställning. Om båda är aktiverade kan duplicerade data uppstå.

![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> För närvarande kan du bara skapa en diagnostisk inställning på prenumerations nivå med hjälp av Azure Portal och en Resource Manager-mall. 


## <a name="legacy-settings"></a>Äldre inställningar 
Även om diagnostikinställningar är den bästa metoden för att skicka aktivitets loggen till olika mål, fortsätter äldre metoder att fungera om du inte väljer att ersätta med en diagnostisk inställning. Diagnostikinställningar har följande fördelar jämfört med tidigare metoder, och vi rekommenderar att du uppdaterar konfigurationen:

- Konsekvent metod för att samla in alla plattforms loggar.
- Samla in aktivitets logg över flera prenumerationer och klienter.
- Filtrera samling för att endast samla in loggar för särskilda kategorier.
- Samla in alla aktivitets logg kategorier. Vissa kategorier samlas inte in med hjälp av en äldre metod.
- Snabbare svars tid för logg inmatning. Den föregående metoden har ungefär 15 minuters fördröjning medan diagnostikinställningar bara lägger till ungefär 1 minut.



### <a name="log-profiles"></a>Logg profiler
Logg profiler är den äldre metoden för att skicka aktivitets loggen till Azure Storage eller Event Hub. Använd följande procedur för att fortsätta arbeta med en logg profil eller inaktivera den för att förbereda för migrering till en diagnostisk inställning.

1. Välj **aktivitets logg**på **Azure Monitor** -menyn i Azure Portal.
3. Klicka på **Diagnostikinställningar**.

   ![Diagnostikinställningar](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klicka på den lila banderollen för den äldre upplevelsen.

    ![Äldre miljö](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Den äldre metoden för att samla in aktivitets loggen på en Log Analytics arbets yta ansluter loggen i arbets ytans konfiguration. 

1. Från menyn **Log Analytics arbets ytor** i Azure Portal väljer du arbets ytan för att samla in aktivitets loggen.
1. I avsnittet **data källor för arbets yta** på menyn för arbets ytan väljer du **Azure aktivitets logg**.
1. Klicka på den prenumeration som du vill ansluta.

    ![Arbetsytor](media/activity-log-collect/workspaces.png)

1. Klicka på **Anslut** för att ansluta aktivitets loggen i prenumerationen till den valda arbets ytan. Om prenumerationen redan är ansluten till en annan arbets yta, klickar du på **Koppla från** först för att koppla bort den.

    ![Anslut arbets ytor](media/activity-log-collect/connect-workspace.png)


Om du vill inaktivera inställningen utför du samma procedur och klickar på **Koppla från** för att ta bort prenumerationen från arbets ytan.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analysera aktivitets loggen i Log Analytics arbets ytan
När du ansluter en aktivitets logg till en Log Analytics arbets yta skrivs poster till arbets ytan i en tabell med namnet *AzureActivity* som du kan hämta med en [logg fråga](../log-query/log-query-overview.md). Strukturen för den här tabellen varierar beroende på vilken [kategori logg posten](activity-log-view.md#categories-in-the-activity-log)har. Se [händelse schema för Azure aktivitets logg](activity-log-schema.md) för en beskrivning av varje kategori.


### <a name="data-structure-changes"></a>Data struktur ändringar
Diagnostikinställningar samlar in samma data som den äldre metoden som används för att samla in aktivitets loggen med vissa ändringar i strukturen i *AzureActivity* -tabellen.

Kolumnerna i följande tabell är föråldrade i det uppdaterade schemat. De finns fortfarande kvar i *AzureActivity* , men de kommer inte att ha några data. Ersättningen för de här kolumnerna är inte nya, men de innehåller samma data som den föråldrade kolumnen. De har ett annat format, så du kan behöva ändra logg frågor som använder dem. 

| Inaktuell kolumn | Ersättnings kolumn |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> I vissa fall kan värdena i dessa kolumner vara i alla versaler. Om du har en fråga som innehåller dessa kolumner bör du använda [operatorn = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) för att göra en Skift läges okänslig jämförelse.

Följande kolumn har lagts till i *AzureActivity* i det uppdaterade schemat:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Aktivitets loggarna analys övervaknings lösning
Lösningen för övervakning av Azure Log Analytics kommer snart att ersättas och ersätts av en arbets bok med det uppdaterade schemat i arbets ytan Log Analytics. Du kan fortfarande använda lösningen om du redan har den aktive rad, men den kan bara användas om du samlar in aktivitets loggen med hjälp av äldre inställningar. 



### <a name="use-the-solution"></a>Använd lösningen
Övervaknings lösningar nås från **Monitor** -menyn i Azure Portal. Välj **mer** i avsnittet om **insikter** för att öppna **översikts** sidan med lösnings panelerna. I panelen **Azure aktivitets loggar** visas antalet **AzureActivity** -poster i din arbets yta.

![Panel för Azure aktivitets loggar](media/collect-activity-logs/azure-activity-logs-tile.png)


Klicka på panelen **Azure aktivitets loggar** för att öppna vyn **Azure aktivitets loggar** . Vyn innehåller visualiserings delarna i följande tabell. Varje del visar upp till 10 objekt som matchar delarnas kriterier för det angivna tidsintervallet. Du kan köra en logg fråga som returnerar alla matchande poster genom att klicka på **Visa alla** längst ned i delen.

![Instrument panel för Azure aktivitets loggar](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Aktivera lösningen för nya prenumerationer
Du kommer snart inte längre att kunna lägga till aktivitets loggs analys lösningen i din prenumeration med hjälp av Azure Portal. Du kan lägga till den med hjälp av följande procedur med en Resource Manager-mall. 

1. Kopiera följande JSON till en fil med namnet *ActivityLogTemplate*. JSON.

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

- Läs mer om [aktivitets loggen](platform-logs-overview.md).
- Läs mer om [Azure Monitor data plattform](data-platform.md).
- Använd [logg frågor](../log-query/log-query-overview.md) för att visa detaljerad information från aktivitets loggen.
