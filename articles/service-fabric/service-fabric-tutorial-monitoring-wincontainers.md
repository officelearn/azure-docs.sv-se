---
title: Övervaka och diagnostisera Windows-containers för Service Fabric i Azure | Microsoft Docs
description: I den här självstudien får du konfigurera Log Analytics för övervakning och diagnostisering av Windows-containers i Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: b013627c5a0dc596c9897d7fa2c5bf2b2a79ee40
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114014"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>Självstudie: Övervaka Windows-containers i Service Fabric med Log Analytics

Det här är del två i självstudiekursen och beskriver steg för steg hur du konfigurerar Log Analytics för att övervaka Windows-behållare som dirigeras i Service Fabric.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera Log Analytics för Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från behållare och noder
> * Konfigurera Log Analytics-agenten så att behållare och nodvärden hämtas in

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

* ha ett kluster i Azure, eller [skapa ett via den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [distribuera ett program i behållare till det](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Konfigurera Log Analytics med klustret i Resource Manager-mall

Om du använde [mallen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) i den första delen av självstudiekursen bör den omfatta följande tillägg till en allmän Service Fabric Azure Resource Manager-mall. Om du har ett eget kluster som du vill konfigurera för behållarövervakning med Log Analytics ska du:

* göra följande ändringar i Resource Manager-mallen
* distribuera med PowerShell för att uppgradera klustret genom att [distribuera mallen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager ser att resursen finns, så den lanseras som en uppgradering.

### <a name="adding-log-analytics-to-your-cluster-template"></a>Lägga till Log Analytics i klustermallen

Gör följande ändringar i *template.json*:

1. Lägga till Log Analytics-arbetsytan, position och namn, i avsnittet *parametrar*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Om du vill ändra något av värdena lägger du till samma parametrar i *template.parameters.json* och ändrar värdena som används där.

2. Lägg till lösningsnamnet och lösningen i dina *variabler*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Lägg till Microsoft Monitoring Agent som ett tillägg för virtuella datorer. Hitta resursen skalningsuppsättningar för virtuella datorer: *resurser* > *"apiVersion": "[variables('vmssApiVersion')]"*. Under *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* lägger du till följande tilläggsbeskrivning under tillägget *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Lägg till Log Analytics-arbetsytan som enskild resurs. I *resources*, efter resursen för skalningsuppsättningar för virtuella datorer, lägger du till följande:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Här](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) är en exempelmall (används i del 1 i den här guiden). I den finns alla ändringarna, att referera till vid behov. De här ändringarna lägger till Log Analytics-arbetsytan i resursgruppen. Arbetsytan konfigureras så att den hämtar upp Service Fabric-plattformshändelser från lagringstabeller som har konfigurerats med [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)-agenten. Log Analytics-agenten (Microsoft Monitoring Agent) har också lagts till i varje nod i klustret som ett tillägg för virtuella datorer. Det innebär att agenten konfigureras automatiskt på varje dator och kopplas till samma arbetsyta när du skalar klustret.

Distribuera mallen med dina ändringar för att uppgradera det aktuella klustret. Du bör se Log Analytics-resurserna i resursgruppen när det här har slutförts. När klustret är klart kan du distribuera program i behållare till det. I nästa steg ställer vi in behållarövervakning.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Lägga till lösning för övervakning av behållare i Log Analytics-arbetsytan

När du vill konfigurera behållarlösningen i arbetsyta söker du efter *Lösning för övervakning av behållare* och skapar en behållarresurs (i kategorin Övervakning och hantering).

![Lägga till behållarlösning](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

När du tillfrågas om *Log Analytics-arbetsytan* markerar du den arbetsyta som skapades i resursgruppen och klickar på **Skapa**. Det här lägger till en *lösning för övervakning av behållare*  på arbetsytan. Det gör automatiskt att Log Analytics-agenten som driftsattes av mallen börjar samla in docker-loggar och statistik. 

Navigera tillbaka till *resursgruppen*. Du bör nu se den nyligen tillagda övervakningslösningen. Om du klickar på den visar landningssidan antalet behållaravbildningar som körs.

*Observera att jag körde 5 instanser av fabrikam-behållaren från [del två](service-fabric-host-app-in-a-container.md) i självstudiekursen*

![Landningssida för behållarlösning](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

När du klickar på **lösningen för övervakning av behållare** dirigeras du till en mer detaljerad instrumentpanel där du kan bläddra igenom flera paneler och köra frågor i Log Analytics.

*Observera att från och med september 2017 genomförs några uppdateringar för lösningen. Ignorera eventuella fel om Kubernetes-händelser. Vi arbetar med att integrera flera initierare i samma lösning.*

Eftersom agenten plockar upp dockerloggar är standardinställningen att *stdout* och *stderr* visas. Om du bläddrar åt höger ser du ett bibliotek med behållaravbildningar, status, mått och exempelfrågor som du kan köra för att få mer användbara data.

![Instrumentpanel för behållarlösning](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Om du klickar på någon av dessa paneler dirigeras du till Log Analytics-frågan som genererar det visade värdet. Ändra frågan till *\** så att du ser alla olika typer av loggar som hämtas in. Härifrån kan du fråga eller filtrera efter behållares prestanda och loggar och titta på händelser för Service Fabric-plattformen. Agenterna avger dessutom ständigt pulsslag från varje nod. Du kan ta en titt på dem och kontrollera att data fortfarande samlas in från alla datorer om klusterkonfigurationen ändras.

![Behållarfråga](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurera Log Analytics-agenten för att hämta prestandaräknare

En annan fördel med att använda Log Analytics-agenten är möjligheten att ändra de prestandaräknare som du hämtar in i Log Analytics UI-miljön. Du slipper att konfigurera Azure-diagnostikagenten och genomföra en uppgradering baserad på resurshanteringsmallen varje gång. För att göra det klickar du på **OMS-arbetsytan** på landningssidan för lösningen för övervakning av behållare (eller Service Fabric).

Det tar dig till Log Analytics-arbetsytan där du kan se dina lösningar, skapa anpassade instrumentpaneler och konfigurera Log Analytics-agenten. 
* Klicka på **Avancerade inställningar** för att öppna menyn Avancerade inställningar.
* Klicka på **Anslutna källor** > **Windows-servrar** för att kontrollera att du har *5 anslutna Windows-datorer*.
* Klicka på **Data** > **Windows-prestandaräknare** för att söka efter och lägga till nya prestandaräknare. Här visas en lista över rekommendationer från Log Analytics för prestandaräknare som du kan samla in samt alternativet att söka efter andra räknare. Kontrollera att räknarna **Processor(_Total)\% Processortid** och **Minne (*) \Tillgängliga megabyte** samlas in.

**Uppdatera** lösningen för övervakning av behållare efter ett par minuter. Du ska nu se information om *Datorprestanda* komma in. Det här hjälper dig att förstå hur dina resurser används. Du kan också använda de här måtten till att fatta rätt beslut om skalning av klustret och för att bekräfta om ett kluster balanserar ut belastningen som förväntat.

*Obs! Kontrollera tidsfiltren är inställda på rätt sätt så att du kan använda de här måtten.*

![Prestandaräknare 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera Log Analytics för Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från behållare och noder
> * Konfigurera Log Analytics-agenten så att behållare och nodvärden hämtas in

Nu när du har ställt in övervakning för programmet i behållaren kan du testa följande:

* Ställ in Log Analytics för ett Linux-kluster, via liknande steg som ovan. Referera till [den här mallen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) och gör ändringar i Resource Manager-mallen.
* Konfigurera Log Analytics och ställ in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) för att underlätta identifiering och diagnostik.
* Utforska Service Fabric-listan över [rekommenderade prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) för att konfigurera klustren.
* Bekanta dig med funktionerna [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) i Log Analytics.