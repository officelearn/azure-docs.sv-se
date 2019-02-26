---
title: Azure Service Fabric - prestandaövervakning med Windows Azure-diagnostiktillägget | Microsoft Docs
description: Använda Windows Azure-diagnostik för att samla in prestandaräknare för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 12ea25b9f1b9f13c153348c285ee6641a69909f0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823186"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Övervakning av programprestanda med Windows Azure Diagnostics-tillägget

Det här dokumentet beskriver steg som krävs för att ställa in insamling av prestandaräknare via Windows Azure Diagnostics SÄKERHETSSPECIFIKA-tillägg för Windows-kluster. För Linux-kluster, konfigurerar du den [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) att samla in prestandaräknare för noderna. 

 > [!NOTE]
> Tillägget WAD ska distribueras i klustret för de här stegen arbeta åt dig. Om det inte har ställts in, gå till [händelse aggregering och samling med Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  

## <a name="collect-performance-counters-via-the-wadcfg"></a>Samla in prestandaräknare via WadCfg

Om du vill samla in prestandaräknare via WAD, måste du ändra konfigurationen på rätt sätt i ditt kluster Resource Manager-mall. Följ dessa steg för att lägga till en prestandaräknare som du vill samla in i mallen och Resource Manager resource uppgraderas.

1. Hitta WAD konfigurationen i ditt kluster mall – hitta `WadCfg`. Du kommer att lägga till prestandaräknare enligt den `DiagnosticMonitorConfiguration`.

2. Konfigurera din konfiguration för att samla in prestandaräknare genom att lägga till följande avsnitt för att din `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    Den `scheduledTransferPeriod` definierar hur ofta värdena för de räknare som samlas in överförs till Azure storage-tabell och till alla konfigurerade mottagare. 

3. Lägg till prestandaräknare som du vill samla in till den `PerformanceCounterConfiguration` som har deklarerats i föregående steg. Varje räknare du vill samla in definieras med en `counterSpecifier`, `sampleRate`, `unit`, `annotation`, och alla relevanta `sinks`.

Här är ett exempel på en konfiguration med räknaren för de *Total processortid* (hur lång tid som Processorn användes för bearbetningsåtgärder) och *metodanropen för Service Fabric-aktör per sekund*, en av de anpassa prestandaräknarna för Service Fabric. Referera till [Reliable Actor-prestandaräknare](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) och [prestandaräknare för tillförlitlig](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) för en fullständig lista över anpassade prestandaräknare för Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Du kan ändra samplingsfrekvensen för räknaren enligt dina behov. Formatet för den är `PT<time><unit>`, så om du vill att den räknare som varje sekund sedan bör du ange den `"sampleRate": "PT15S"`.

 Du kan också använda variabler i ARM-mall för att samla in en matris med prestandaräknare som kan är användbar när du samlar in prestandaräknare processer. I det exemplet nedan vi samlar in processortid och skräpinsamlaren tid per process och sedan 2 prestandaräknare på noderna alla med hjälp av variabler. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

 >[!NOTE]
 >Även om du kan använda `*` om du vill ange grupper av prestandaräknare som namnges på samma sätt skicka räknare via en mottagare (till Application Insights) kräver att de deklareras individuellt. 

4. När du har lagt till lämplig prestandaräknare som behöver samlas in, måste du uppgradera din klusterresursen så att de här ändringarna i ditt kluster som körs. Spara den ändrade `template.json` och öppna PowerShell. Du kan uppgradera ditt kluster med hjälp av `New-AzureRmResourceGroupDeployment`. Anropet kräver namnet på resursgruppen och uppdaterade mallfilen parameterfilen och uppmanar Resource Manager för att göra lämpliga ändringar i de resurser som du har uppdaterat. När du är inloggad på ditt konto och är i rätt prenumeration, använder du följande kommando med uppgraderingen:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. När uppgraderingen har slutförts ska lansera (tar mellan 15-45 minuter beroende på om det är den första distributionen och storleken på din resursgrupp) WAD samla in prestandaräknare och skicka dem till tabellen med namnet WADPerformanceCountersTable i lagringskontot som associerats med klustret. Se dina prestandaräknare i Application Insights genom [lägger till AI-mottagare i Resource Manager-mall](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Nästa steg
* Samla in mer prestandaräknare för klustret. Se [prestandamått](service-fabric-diagnostics-event-generation-perf.md) för en lista över räknare som du bör samla in.
* [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](../virtual-machines/windows/extensions-diagnostics-template.md) göra ytterligare ändringar i din `WadCfg`, inklusive hur du konfigurerar ytterligare lagringskonton för att skicka diagnostikdata.
* Gå till den [WadCfg builder](http://azure.github.io/azure-diagnostics-tools/config-builder/) att skapa en mall från början och kontrollera att din syntax är korrekt.