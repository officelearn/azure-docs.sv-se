---
title: "Azure Service Fabric - prestandaövervakning med Windows Azure-diagnostik tillägg | Microsoft Docs"
description: "Använda Windows Azure-diagnostik för att samla in prestandaräknare för Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Övervakning av programprestanda med Windows Azure-diagnostik

Det här dokumentet beskriver steg som krävs för att ställa in insamling av prestandaräknare via Windows Azure Diagnostics (BOMULLSTUSS)-tillägget för Windows-kluster. För Linux-kluster, ställa in den [OMS-Agent](service-fabric-diagnostics-oms-agent.md) att samla in prestandaräknare för noderna. 

 > [!NOTE]
> Tillägget BOMULLSTUSS ska distribueras på ditt kluster för de här stegen fungerar för dig. Om den inte har ställts in, gå till [händelse aggregering och med Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md) och följer anvisningarna i *distribuera diagnostik tillägget*.

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

3. Lägg till prestandaräknare som du vill samla in till den `PerformanceCounterConfiguration` som har deklarerats i föregående steg. Varje räknare du vill samla in definieras med en `counterSpecifier`, `sampleRate`, `unit`, `annotation`, och alla relevanta `sinks`. Här är ett exempel på en prestandaräknaren för den *Total processortid* (hur lång tid som Processorn användes för bearbetningsåtgärder):

    ```json
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
            }
        ]
    }
    ```

    Samplingsfrekvens för räknaren kan ändras enligt dina behov. Formatet för den är `PT<time><unit>`, så om du vill att räknaren samlas in varje sekund du bör ange den `"sampleRate": "PT15S"`.

    På liknande sätt kan du samla in flera andra prestandaräknare genom att lägga till dem i listan i den `PerformanceCounterConfiguration`. Även om du kan använda `*` för att ange grupper av prestandaräknare som har liknande namn, skickar räknare via en mottagare (till Application Insights) kräver att de deklareras individuellt. 

4. När du har lagt till lämplig prestandaräknare som ska samlas in, måste du uppgradera din klusterresurs så att ändringarna återspeglas i ditt kluster som körs. Spara dina ändrade `template.json` och öppna PowerShell. Du kan uppgradera kluster med hjälp av `New-AzureRmResourceGroupDeployment`. Anropet kräver namnet på resursgruppen och filen med uppdaterade mallen parameterfilen och uppmanar resurshanteraren för att göra ändringar i de resurser som du har uppdaterat. När du är inloggad på ditt konto och är i rätt prenumerationen, använder du följande kommando för att köra uppgraderingen:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. När uppgraderingen har slutförts lansera (tar mellan 15-45 minuter) BOMULLSTUSS bör vara att samla in prestandaräknare och skicka dem till en tabell i lagringskontot deklareras i den `WadCfg`.

## <a name="next-steps"></a>Nästa steg
* Prestandaräknare i Application Insights genom att lägga till Application Insights mottagare för att se din `WadCfg`. Läs den *lägger du till AI Sink Resource Manager-mallen* i avsnittet [händelseanalys och visualisering med Application Insights ](service-fabric-diagnostics-event-analysis-appinsights.md) att konfigurera Application Insights sink.
* Samla in mer prestandaräknare för klustret. Se [prestandamått](service-fabric-diagnostics-event-generation-perf.md) för en lista över räknare som du bör samla in.
* [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](../virtual-machines/windows/extensions-diagnostics-template.md) att göra ytterligare ändringar i din `WadCfg`, inklusive hur du konfigurerar ytterligare lagringskonton för att skicka diagnostikdata till.