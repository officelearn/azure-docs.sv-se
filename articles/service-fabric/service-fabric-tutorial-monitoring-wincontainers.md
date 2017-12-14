---
title: "Övervaknings- och diagnostikfunktionerna för Windows-behållare i Azure Service Fabric | Microsoft Docs"
description: "Konfigurera övervakning och diagnostik för Windows-behållaren styrd på Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 69a59ea9fb93f6e9f3f3eea66b1a9e973b1b4eea
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="monitor-windows-containers-on-service-fabric-using-oms"></a>Övervaka Windows-behållare i Service Fabric med OMS

Detta är en del tre i självstudiekursen och vägleder dig genom att ställa in OMS för att övervaka din Windows-behållare för samordnade på Service Fabric.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera OMS för Service Fabric-kluster
> * En OMS-arbetsyta kan du visa och fråga loggar från behållare och noder
> * Konfigurera OMS-agent för behållaren och nod-mått

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen bör du:
- Har ett kluster i Azure, eller [skapa ett med den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [Distribuera en av programmet till den](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>Konfigurera OMS med klustret i Resource Manager-mall

I de fall som du använde den [-mall](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) i den första delen av kursen den ska innehålla följande tillägg till en allmän Service Fabric Azure Resource Manager-mall. I fallet om söker att du har ett kluster med din egen som du om du vill konfigurera för övervakning av behållare med OMS:
* Gör följande ändringar i Resource Manager-mallen.
* Distribuera den med hjälp av PowerShell så här uppgraderar du klustret med [distribuerar mallen](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager inser att resursen finns, så görs det som en uppgradering.

### <a name="adding-oms-to-your-cluster-template"></a>Lägger till OMS i mallen för kluster

Gör följande ändringar i din *template.json*:

1. Lägga till OMS-arbetsytan platsen och att din *parametrar* avsnitt:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your OMS Log Analytics Workspace"
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
        "description": "Specify the Azure Region for your OMS workspace"
      }
    }
    ```

    Det värde som används för att ändra lägga till samma parametrar på din *template.parameters.json* och ändra värdena används där.

2. Lägg till lösningens namn och lösningen på din *variabler*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Lägg till OMS Microsoft Monitoring Agent som ett tillägg för virtuell dator. Hitta virtuella datorn anger resurs: *resurser* > *”apiVersion”: ”[variables('vmssApiVersion')]”*. Under den *egenskaper* > *virtualMachineProfile* > *extensionProfile* > *tillägg*, Lägg till följande tillägg beskrivning under den *ServiceFabricNode* tillägg: 
    
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

4. Lägg till OMS-arbetsyta som en enskild resurs. I *resurser*efter att virtuella datorns skaluppsättning anger resursen, Lägg till följande:
    
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

[Här](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) är en exempelmall (används i del 1 av den här självstudiekursen) som har alla de här ändringarna som du kan referera till efter behov. De här ändringarna lägger till en OMS logganalys-arbetsytan till resursgruppen. Arbetsytan konfigureras för att hämta Service Fabric-plattformen händelser från storage-tabeller som har konfigurerats med den [Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md) agent. OMS-agent (Microsoft Monitoring Agent) har också lagts till varje nod i klustret som ett tillägg för virtuell dator - detta innebär att när du skalar klustret agenten är automatiskt konfigurerade på varje dator och kopplat till samma arbetsyta.

Distribuera mallen med din nya ändringar för att uppgradera din aktuella klustret. Du bör se OMS-resurser i resursgruppen när detta har slutförts. När klustret är klar kan du distribuera av programmet till den. I nästa steg ska ställa vi in övervakning behållarna.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>Lägg till behållaren övervakning lösningen till OMS-arbetsyta

Om du vill konfigurera lösningen behållare i din arbetsyta, söka efter *lösning för övervakning av behållare* och skapa en behållare resurs (under övervakning + Management kategori).

![Lägga till behållare lösning](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

När du tillfrågas om den *OMS-arbetsytan*, markera arbetsytan som skapades i resursgruppen och på **skapa**. Detta lägger till en *lösning för övervakning av behållare* till din arbetsyta blir automatiskt OMS-agenten distribueras med mallen ska börja samla in docker loggar och statistik. 

Gå tillbaka till din *resursgruppen*, där du bör nu se nyligen tillagda övervakningslösning. Om du klickar på den landningssida ska visa du antalet behållare avbildningar som du kör. 

*Observera att jag har kört 5 instanser av min fabrikam behållare från [del två](service-fabric-host-app-in-a-container.md) av kursen*

![Landningssida för behållaren lösning](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Att klicka på den **behållare övervakaren lösning** tar dig vidare till en mer detaljerad instrumentpanel där du kan bläddra igenom flera paneler som kör frågor i logganalys. 

*Observera att från och med September 2017 lösningen gå igenom några uppdateringar - ignorera eventuella fel som du kan få om Kubernetes händelser när vi arbetar med att integrera flera orchestrators i samma lösning.*

Eftersom agenten plocka upp docker loggar, används som standard visar *stdout* och *stderr*. Om du bläddrar till höger visas behållaren image lager, status, mått och exempelfrågor som du kan köra för att få fler användbara data. 

![Behållaren lösning instrumentpanelen](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Att klicka på någon av dessa paneler tar dig vidare till logganalys-frågan som genererar det visa värdet. Ändra frågan så att  *\**  att se olika typer av loggar som fångas upp. Härifrån kan du fråga eller filter för behållaren prestanda, loggar eller titta på händelser för Service Fabric-plattformen. Agenterna också ständigt avger något pulsslag från varje nod som du kan granska och kontrollera data samlas fortfarande från alla datorer om klusterkonfigurationen ändras.   

![Behållaren fråga](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Konfigurera OMS-agent för prestandaräknare

En annan fördel med OMS-agenten är möjligheten att ändra de prestandaräknare som du vill hämta OMS UI-miljö, i stället för att behöva konfigurera Azure diagnostics agent och gör en resurshanterare mallen baseras uppgraderingen varje gång. Om du vill göra det, klickar du på **OMS-portalen** på Landningssida för din lösning för övervakning av behållare (eller Service Fabric).

![OMS-portalen](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Detta kommer ta dig till din arbetsyta i OMS-portalen där du kan visa dina lösningar, skapa anpassade instrumentpaneler, samt konfigurera OMS-agent. 
* Klicka på den **kugge hjul** i övre högra hörnet av skärmen för att öppna den *inställningar* menyn.
* Klicka på **anslutna källor** > **Windows-servrar** att kontrollera att du har *5 Windows-datorer anslutna*.
* Klicka på **Data** > **Windows prestandaräknare** att söka efter och lägga till nya prestandaräknare. Här visas en lista över rekommendationerna från OMS för prestandaräknarna du kan samla in samt alternativet för att söka efter andra räknare. Klicka på **Lägg till valda prestandaräknare** att börja samla in föreslagna mått.

    ![Prestandaräknarna](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Tillbaka i Azure-portalen **uppdatera** behållare övervakning lösningen i några minuter och du ska starta att se *datorprestanda* data kommer in. Detta hjälper dig att förstå hur dina resurser används. Du kan också använda de här måtten att fatta rätt beslut om skalning av klustret eller för att bekräfta om ett kluster belastningsutjämning din som förväntat.

*Obs: Kontrollera din tidsfilter är inställda på rätt sätt som du kan använda de här måtten.* 

![Prestandaräknarna 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera OMS för Service Fabric-kluster
> * En OMS-arbetsyta kan du visa och fråga loggar från behållare och noder
> * Konfigurera OMS-agent för behållaren och nod-mått

Nu när du har konfigurerat övervakning för tillämpningsprogrammet av du testa följande:

* Ställ in OMS för ett Linux-kluster, följa liknande steg som ovan. Referens [mallen](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) att göra ändringar i Resource Manager-mall.
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) att underlätta identifiering och diagnostik.
* Utforska Service Fabric-lista över [rekommenderas prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) du konfigurerar för ditt kluster.
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys.