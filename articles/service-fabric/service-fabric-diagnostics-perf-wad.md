---
title: Prestandaövervakning med Windows Azure Diagnostics
description: Använd Windows Azure Diagnostics för att samla in prestandaräknare för dina Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 0819ca02d088aeb9ada5de1269467f70242bbcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609918"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Prestandaövervakning med tillägget Windows Azure Diagnostics

Det här dokumentet innehåller steg som krävs för att konfigurera insamling av prestandaräknare via RAD-tillägget Windows Azure Diagnostics (WAD) för Windows-kluster. För Linux-kluster konfigurerar du [Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) för att samla in prestandaräknare för dina noder. 

 > [!NOTE]
> WAD-tillägget bör distribueras i klustret för att de här stegen ska fungera för dig. Om den inte är konfigurerad går du över till [Händelseaggregering och samling med Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Samla in prestandaräknare via WadCfg

Om du vill samla in prestandaräknare via WAD måste du ändra konfigurationen på rätt sätt i klustrets Resource Manager-mall. Följ dessa steg för att lägga till en prestandaräknare som du vill samla in i mallen och köra en Resource Manager-resursuppgradering.

1. Hitta WAD-konfigurationen i klustrets mall - sök `WadCfg`. Du kommer att lägga till prestandaräknare att samla under `DiagnosticMonitorConfiguration`.

2. Konfigurera konfigurationen för att samla in prestandaräknare `DiagnosticMonitorConfiguration`genom att lägga till följande avsnitt i . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    Definitionerna `scheduledTransferPeriod` hur ofta värdena för de räknare som samlas in överförs till din Azure-lagringstabell och till alla konfigurerade mottagare. 

3. Lägg till prestandaräknarna som `PerformanceCounterConfiguration` du vill samla in till det som deklarerades i föregående steg. Varje räknare som du vill `counterSpecifier`samla `sampleRate` `unit`in `annotation`definieras med `sinks`en , , , och alla relevanta .

Här är ett exempel på en konfiguration med räknaren för den *totala processortiden* (den tid som processorn användes för bearbetning) och *Service Fabric Actor Method Anrop per sekund*, en av service fabric anpassade prestandaräknare. Se [tillförlitliga prestandaräknare](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) för skådespelare och [tillförlitliga serviceprestandaräknare](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) för en fullständig lista över anpassade perf-räknare för Service Fabric.

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

 Samplingsfrekvensen för räknaren kan ändras enligt dina behov. Formatet för det `PT<time><unit>`är , så om du vill att räknaren `"sampleRate": "PT15S"`samlas in varje sekund, så ska du ställa in .

 Du kan också använda variabler i ARM-mallen för att samla in en rad prestandaräknare, som kan vara praktiskt när du samlar in prestandaräknare per process. I exemplet nedan samlar vi in processortid och skräpinsamlingstid per process och sedan 2 prestandaräknare på noderna själva alla med hjälp av variabler. 

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

1. När du har lagt till lämpliga prestandaräknare som måste samlas in måste du uppgradera klusterresursen så att dessa ändringar återspeglas i klustret som körs. Spara din `template.json` ändrade och öppna PowerShell. Du kan uppgradera `New-AzResourceGroupDeployment`klustret med . Anropet kräver namnet på resursgruppen, den uppdaterade mallfilen och parameterfilen och uppmanar Resource Manager att göra lämpliga ändringar i de resurser som du har uppdaterat. När du har loggat in på ditt konto och har rätt prenumeration använder du följande kommando för att köra uppgraderingen:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. När uppgraderingen har slutförts (tar mellan 15–45 minuter beroende på om det är den första distributionen och storleken på resursgruppen) bör WAD samla in prestandaräknare och skicka dem till tabellen med namnet WADPerformanceCountersTable i lagringskontot som är kopplat till klustret. Se dina prestandaräknare i Application Insights genom [att lägga till AI Sink i Resource Manager-mallen](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Nästa steg
* Samla in fler prestandaräknare för klustret. Se [Prestandamått](service-fabric-diagnostics-event-generation-perf.md) för en lista över räknare som du bör samla in.
* [Använd övervakning och diagnostik med mallar](../virtual-machines/windows/extensions-diagnostics-template.md) för Windows VM och Azure `WadCfg`Resource Manager för att göra ytterligare ändringar i din, inklusive att konfigurera ytterligare lagringskonton för att skicka diagnostikdata till.
* Besök [WadCfg-byggaren](https://azure.github.io/azure-diagnostics-tools/config-builder/) för att skapa en mall från grunden och se till att syntaxen är korrekt. (förhttps://azure.github.io/azure-diagnostics-tools/config-builder/) att bygga en mall från grunden och se till att din syntax är korrekt.
