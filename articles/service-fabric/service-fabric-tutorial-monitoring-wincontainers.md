---
title: Övervaka och diagnostisera Windows-containers för Service Fabric i Azure | Microsoft Docs
description: I den här självstudien konfigurerar du Azure Monitor loggar för övervakning och diagnostik av Windows-behållare på Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 856e2859c778c9f23bc093c2283571a1440ef701
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68598780"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Självstudier: Övervaka Windows-behållare på Service Fabric med hjälp av Azure Monitor loggar

Detta är del tre i en själv studie kurs och vägleder dig genom att konfigurera Azure Monitor loggar för att övervaka dina Windows-behållare som dirigeras på Service Fabric.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Azure Monitor loggar för ditt Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från containrar och noder
> * Konfigurera Log Analytics-agenten så att containrar och nodvärden hämtas in

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar de här självstudierna bör du:

* ha ett kluster i Azure, eller [skapa ett via den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett program i containrar till det](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Konfigurera Azure Monitor loggar med ditt kluster i Resource Manager-mallen

Om du använde [mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) i den första delen av självstudiekursen bör den omfatta följande tillägg till en allmän Service Fabric Azure Resource Manager-mall. Om du har ett eget kluster som du vill konfigurera för övervakning av behållare med Azure Monitor loggar:

* göra följande ändringar i Resource Manager-mallen
* distribuera med PowerShell för att uppgradera klustret genom att [distribuera mallen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager ser att resursen finns, så den lanseras som en uppgradering.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Lägga till Azure Monitor loggar i kluster mal len

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

3. Lägg till Microsoft Monitoring Agent som ett tillägg för virtuella datorer. Hitta resursen skalningsuppsättningar för virtuella datorer: *resurser* >  *"apiVersion": "[variables('vmssApiVersion')]"* . Under *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* lägger du till följande tilläggsbeskrivning under tillägget *ServiceFabricNode*: 
    
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

Distribuera mallen med dina ändringar för att uppgradera det aktuella klustret. Du bör se Log Analytics-resurserna i resurs gruppen när detta har slutförts. När klustret är klart kan du distribuera ditt containerprogram till det. I nästa steg ställer vi in containerövervakning.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Lägga till övervakningslösning för containrar i Log Analytics-arbetsytan

När du vill konfigurera containerlösningen i arbetsytan söker du efter *Övervakningslösning för container* och skapar en containerresurs (i kategorin Övervakning och hantering).

![Lägga till containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

När du uppmanas till *arbets ytan Log Analytics*väljer du den arbets yta som skapades i resurs gruppen och klickar på **skapa**. Det här lägger till en *övervakningslösning för containrar* på arbetsytan. Det gör automatiskt att Log Analytics-agenten som driftsattes av mallen börjar samla in docker-loggar och statistik. 

Navigera tillbaka till *resursgruppen*. Du bör nu se den nyligen tillagda övervakningslösningen. Om du klickar på den visar landningssidan antalet containeravbildningar som körs.

*Observera att jag körde 5 instanser av fabrikam-containern från [del två](service-fabric-host-app-in-a-container.md) i självstudiekursen*

![Landningssida för containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Genom att klicka i lösningen för behållar **övervakaren** går du till en mer detaljerad instrument panel, som gör att du kan bläddra igenom flera paneler och köra frågor i Azure Monitor loggar.

*Observera att från och med september 2017 genomförs några uppdateringar för lösningen. Ignorera eventuella fel om Kubernetes-händelser. Vi arbetar med att integrera flera initierare i samma lösning.*

Eftersom agenten plockar upp dockerloggar är standardinställningen att *stdout* och *stderr* visas. Om du bläddrar åt höger ser du ett bibliotek med containeravbildningar, status, mått och exempelfrågor som du kan köra för att få mer användbara data.

![Instrumentpanel för containerlösning](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Om du klickar på någon av dessa paneler tas du till den Kusto-fråga som genererar det visade värdet. Ändra frågan till *\** så att du ser alla olika typer av loggar som hämtas in. Härifrån kan du fråga eller filtrera efter containrarnas prestanda och loggar och titta på händelser för Service Fabric-plattformen. Agenterna avger dessutom ständigt pulsslag från varje nod. Du kan ta en titt på dem och kontrollera att data fortfarande samlas in från alla datorer om klusterkonfigurationen ändras.

![Containerfråga](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurera Log Analytics-agenten för att hämta prestandaräknare

En annan fördel med att använda Log Analytics-agenten är möjligheten att ändra de prestanda räknare som du vill hämta via UI-gränssnittet i Log Analytics, i stället för att konfigurera Azure Diagnostics-agenten och göra en Resource Manager-mall baserad uppgradering varje tillfälle. För att göra det klickar du på **OMS-arbetsytan** på landningssidan för lösningen för övervakning av containrar (eller Service Fabric).

Det leder till Log Analytics-arbetsytan där du kan se dina lösningar, skapa anpassade instrumentpaneler och konfigurera Log Analytics-agenten. 
* Klicka på **Avancerade inställningar** för att öppna menyn Avancerade inställningar.
* Klicka på **Anslutna källor** > **Windows-servrar** för att kontrollera att du har *5 anslutna Windows-datorer*.
* Klicka på **Data** > **Windows-prestandaräknare** för att söka efter och lägga till nya prestandaräknare. Här visas en lista med rekommendationer från Azure Monitor loggar för prestanda räknare som du kan samla in samt alternativet för att söka efter andra räknare. Kontrollera att räknarna **Processor(_Total)\% Processortid** och **Minne (*) \Tillgängliga megabyte** samlas in.

**Uppdatera** övervakningslösningen för containrar efter ett par minuter. Du ska nu se information om *Datorprestanda* komma in. Det här hjälper dig att förstå hur dina resurser används. Du kan också använda de här måtten till att fatta rätt beslut om skalning av klustret och för att bekräfta om ett kluster balanserar ut belastningen som förväntat.

*Obs! Kontrollera att tidsfiltren är inställda på rätt sätt så att du kan använda de här måtten.*

![Prestandaräknare 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera Azure Monitor loggar för ditt Service Fabric-kluster
> * Använda en Log Analytics-arbetsyta till att visa och fråga loggar från containrar och noder
> * Konfigurera Log Analytics-agenten så att containrar och nodvärden hämtas in

Nu när du har ställt in övervakning för programmet i containern kan du testa följande:

* Konfigurera Azure Monitor loggar för ett Linux-kluster genom att följa liknande steg som ovan. Referera till [den här mallen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) och gör ändringar i Resource Manager-mallen.
* Konfigurera Azure Monitor loggar för att ställa in [automatiserad avisering](../log-analytics/log-analytics-alerts.md) för identifiering och diagnostik.
* Utforska Service Fabric-listan över [rekommenderade prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) för att konfigurera klustren.
* Bekanta dig med funktionerna för [loggs ökning och frågor](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor loggar.
