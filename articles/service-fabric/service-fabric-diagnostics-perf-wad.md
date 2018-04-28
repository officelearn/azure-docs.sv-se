---
title: Azure Service Fabric - prestandaövervakning med Windows Azure-diagnostik tillägget | Microsoft Docs
description: Använda Windows Azure-diagnostik för att samla in prestandaräknare för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: a9e8ef7243fcef990dae6ddc6509cd31b3f36e3d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Övervakning av programprestanda med Windows Azure-diagnostik-tillägget

Det här dokumentet beskriver steg som krävs för att ställa in insamling av prestandaräknare via Windows Azure Diagnostics (BOMULLSTUSS)-tillägget för Windows-kluster. För Linux-kluster, ställa in den [OMS-Agent](service-fabric-diagnostics-oms-agent.md) att samla in prestandaräknare för noderna. 

 > [!NOTE]
> Tillägget BOMULLSTUSS ska distribueras på ditt kluster för de här stegen fungerar för dig. Om den inte har ställts in, gå till [händelse aggregering och med Windows Azure-diagnostik](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

## <a name="collect-performance-counters-via-the-wadcfg"></a>Samla in prestandaräknare via WadCfg

Om du vill samla in prestandaräknare via BOMULLSTUSS måste du ändra konfigurationen korrekt i ditt kluster Resource Manager-mall. Följ dessa steg för att lägga till en prestandaräknare som du vill samla in i mallen och Resource Manager resurs uppgraderas.

1. Hitta BOMULLSTUSS konfigurationen i ditt kluster mall - hitta `WadCfg`. Du kommer att lägga till prestandaräknare som samlas in den `DiagnosticMonitorConfiguration`.

2. Ställ in konfigurationen för att samla in prestandaräknare genom att lägga till följande avsnitt för att din `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    Den `scheduledTransferPeriod` definierar hur frquently som värden för de räknare som samlas in överförs till Azure storage-tabellen och alla konfigurerade mottagare. 

3. Lägg till prestandaräknare som du vill samla in till den `PerformanceCounterConfiguration` som har deklarerats i föregående steg. Varje räknare du vill samla in definieras med en `counterSpecifier`, `sampleRate`, `unit`, `annotation`, och alla relevanta `sinks`.

Här är ett exempel på en konfiguration med räknaren för de *Total processortid* (hur lång tid som Processorn användes för bearbetningsåtgärder) och *Service Fabric aktören metoden anrop per sekund*, en av de anpassa prestandaräknarna för Service Fabric. Referera till [tillförlitliga aktören prestandaräknare](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) och [tillförlitliga prestandaräknare för tjänsten](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) för en fullständig lista över Service Fabric anpassade prestandaräknarna.

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

 Samplingsfrekvens för räknaren kan ändras enligt dina behov. Formatet för den är `PT<time><unit>`, så om du vill att räknaren samlas in varje sekund du bör ange den `"sampleRate": "PT15S"`.

 >[!NOTE]
 >Även om du kan använda `*` för att ange grupper av prestandaräknare som har liknande namn, skickar räknare via en mottagare (till Application Insights) kräver att de deklareras individuellt. 

4. När du har lagt till lämplig prestandaräknare som ska samlas in, måste du uppgradera din klusterresurs så att ändringarna återspeglas i ditt kluster som körs. Spara dina ändrade `template.json` och öppna PowerShell. Du kan uppgradera kluster med hjälp av `New-AzureRmResourceGroupDeployment`. Anropet kräver namnet på resursgruppen och filen med uppdaterade mallen parameterfilen och uppmanar resurshanteraren för att göra ändringar i de resurser som du har uppdaterat. När du är inloggad på ditt konto och är i rätt prenumerationen, använder du följande kommando för att köra uppgraderingen:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. När uppgraderingen har slutförts lansera (tar mellan 15-45 minuter) BOMULLSTUSS bör vara att samla in prestandaräknare och skicka dem till tabellen med namnet WADPerformanceCountersTable i storage-konto som är associerade med klustret. Se dina prestandaräknare i Application Insights av [lägger till AI Sink i Resource Manager-mall](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Nästa steg
* Samla in mer prestandaräknare för klustret. Se [prestandamått](service-fabric-diagnostics-event-generation-perf.md) för en lista över räknare som du bör samla in.
* [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](../virtual-machines/windows/extensions-diagnostics-template.md) att göra ytterligare ändringar i din `WadCfg`, inklusive hur du konfigurerar ytterligare lagringskonton för att skicka diagnostikdata till.
