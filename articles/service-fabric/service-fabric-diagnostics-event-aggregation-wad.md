---
title: "Azure Service Fabric händelse aggregeringen med Windows Azure-diagnostik | Microsoft Docs"
description: "Läs mer om sammanställa och samlar in händelser med hjälp av BOMULLSTUSS för övervakning och diagnostik av Azure Service Fabric-kluster."
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
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 8e6c82aa60544d672bb249d589b63d55b48309fe
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Aggregering av händelse och med Windows Azure-diagnostik
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggar från alla noder i en central plats. Med loggarna på en central plats hjälper dig att analysera och felsöka problem i klustret eller problem i program och tjänster som körs i klustret.

Ett sätt att överföra och samla in loggar är att använda Windows Azure Diagnostics (BOMULLSTUSS)-tillägget, som överför loggar till Azure Storage och har även möjlighet att skicka loggar till Azure Application Insights eller Händelsehubbar. Du kan också använda en extern process för att läsa händelser från lagring och placera dem i en analys plattform produkt som [OMS logganalys](../log-analytics/log-analytics-service-fabric.md) eller en annan lösning för parsning av loggen.

## <a name="prerequisites"></a>Krav
Verktygen används för att utföra vissa åtgärder i det här dokumentet:

* [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (rör Azure Cloud Services men har bra information och exempel)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-klienten](https://github.com/projectkudu/ARMClient)
* [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Loggen och händelsen källor

### <a name="service-fabric-platform-events"></a>Service Fabric-plattformen händelser
Enligt beskrivningen i [i den här artikeln](service-fabric-diagnostics-event-generation-infra.md), Service Fabric ställer du in med några out box loggning kanaler, som följande kanaler enkelt är konfigurerade med BOMULLSTUSS att skicka övervakning och diagnostikdata till en tabell för lagring eller någon annanstans:
  * Operativa händelser: att åtgärder som utförs av Service Fabric-plattformen. Exempel: skapa program och tjänster, nod tillståndsändringar och information om uppgradering. Dessa orsakat som loggar händelsen ETW Tracing for Windows)
  * [Reliable Actors programmering modellen händelser](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programming modellen händelser](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Programhändelser
 Händelser som orsakat från program och tjänsterna kod och skrivs med hjälp av EventSource hjälpklass i Visual Studio-mallar. Mer information om hur du skriver EventSource loggar från ditt program finns [övervaka och diagnostisera tjänster i en inställning för lokal dator development](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuera diagnostik-tillägget
Det första steget i att samla in loggar är att distribuera diagnostik tillägg på var och en av de virtuella datorerna i Service Fabric-klustret. Diagnostik-tillägget samlar in loggar på varje virtuell dator och överför dem till lagringskontot som du anger. Stegen variera något beroende på om du använder Azure-portalen eller Azure Resource Manager. Stegen variera beroende på om distributionen är en del av klustret har skapats eller är i ett kluster som redan finns. Nu ska vi titta på stegen för varje scenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuera diagnostik-tillägget som en del av klustret har skapats via Azure portal
Om du vill distribuera diagnostik-tillägg till de virtuella datorerna i klustret som en del av klustret skapas, Använd panelen diagnostik inställningar visas i följande bild - Kontrollera att diagnostik är **på** (standardinställningen). När du skapar klustret kan du inte ändra dessa inställningar med hjälp av portalen.

![Azure diagnostikinställningar för klustret skapas i portalen](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

När du skapar ett kluster med hjälp av portalen, vi rekommenderar starkt att du hämtar mallen **innan du klickar på OK** att skapa klustret. Mer information finns i [ställer in ett Service Fabric-kluster med en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md). Du behöver mallen du vill göra ändringarna senare, eftersom du inte kan göra några ändringar med hjälp av portalen.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Distribuera diagnostik-tillägget som en del av klustret har skapats med hjälp av Azure Resource Manager
Om du vill skapa ett kluster med hjälp av hanteraren för filserverresurser, som du behöver lägga till diagnostik konfigurationen JSON till hela klustret Resource Manager-mallen innan du skapar klustret. Vi ger en fem-VM klustret Resource Manager exempelmall diagnostik-konfiguration som lagts till som en del av våra exempel för Resource Manager-mall. Du kan se den på den här platsen i galleriet Azure-exempel: [kluster med fem noder med diagnostik Resource Manager mallen exempel](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Öppna filen azuredeploy.json för att se inställningen diagnostik i Resource Manager-mallen, och Sök efter **IaaSDiagnostics**. Om du vill skapa ett kluster med hjälp av den här mallen, Välj den **till Azure** knappen tillgänglig vid föregående länk.

Du kan hämta Resource Manager-exempel, göra ändringar, och skapa ett kluster med den ändrade mallen med hjälp av den `New-AzureRmResourceGroupDeployment` i Azure PowerShell-fönster. Se följande kod för de parametrar som du anger för att kommandot. Detaljerad information om hur du distribuerar en resursgrupp med hjälp av PowerShell finns i artikeln [distribuera en resursgrupp med Azure Resource Manager-mallen](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Distribuera tillägget diagnostik till ett befintligt kluster
Om du har ett befintligt kluster som inte har distribuerats diagnostik eller om du vill ändra en befintlig konfiguration, kan du lägga till eller uppdatera det. Ändra Resource Manager-mallen som används för att skapa det befintliga klustret eller hämta mallen från portalen, enligt beskrivningen ovan. Ändra filen template.json genom att utföra följande uppgifter.

Lägga till en ny storage-resurs i mallen genom att lägga till avsnittet resurser.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Lägg till avsnittet Parametrar precis efter storage-konto definitioner, mellan `supportLogStorageAccountName` och `vmNodeType0Name`. Ersätt platshållartexten *lagringskontonamnet här* med namnet på lagringskontot.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Sedan uppdatera den `VirtualMachineProfile` i template.json-filen genom att lägga till följande kod i en matris för tillägg. Glöm inte att lägga till ett kommatecken i början eller slutet, beroende på om det har infogats.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
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
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

När du har ändrat filen template.json enligt publicera Resource Manager-mallen. Om mallen exporterades, publicerar körs filen deploy.ps1 mallen. När du har distribuerat, se till att **ProvisioningState** är **lyckades**.

## <a name="collect-health-and-load-events"></a>Samla in hälsa och läsa in händelser

Från och med 5.4 för Service Fabric, är hälsotillstånd och Läs in mått händelser tillgängliga för samlingen. Dessa händelser återspeglar händelser som genererats av systemet eller din kod med hjälp av hälsotillstånd eller läsa in reporting API: er som [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) eller [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Detta gör för att sammanställa och visa systemhälsa över tid och aviseringar baserat på händelser hälsa eller belastningsutjämning. Visa dessa händelser i Loggboken för Visual Studio diagnostiska lägga till ”Microsoft-ServiceFabric:4:0x4000000000000008” i listan över ETW-providers.

Om du vill samla in händelser i klustret, ändra den `scheduledTransferKeywordFilter` i WadCfg av Resource Manager-mall att `4611686018427387912`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Samla in händelser för omvänd proxy

Från och med 5.7 för Service Fabric [omvänd proxy](service-fabric-reverseproxy.md) händelser finns tillgängliga för samlingen via Data & Messaging kanaler. 

Omvänd proxy skickas endast felhändelser via viktigaste Data & Messaging-kanalen - reflektion kritiska problem och fel för begäranden. Detaljerad kanalen innehåller utförlig händelser för alla begäranden som bearbetas av omvänd proxy. 

Visa felhändelser i Visual Studio diagnostiska Loggboken lägga till ”Microsoft-ServiceFabric:4:0x4000000000000010” i listan över ETW-providers. För alla begärandetelemetri uppdatera Microsoft-ServiceFabric posten i listan för ETW-provider till ”Microsoft-ServiceFabric:4:0x4000000000000020”.

För kluster som körs i Azure:

För att hämta upp spåren i viktigaste Data & Messaging kanalen, ändra den `scheduledTransferKeywordFilter` värde i WadCfg av Resource Manager-mall att `4611686018427387920`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Om du vill samla in händelser från alla begäranbearbetningen aktivera Data & Messaging - detaljerad kanal genom att ändra den `scheduledTransferKeywordFilter` värde i WadCfg av Resource Manager-mall att `4611686018427387936`.

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

Att aktivera att samla in händelser från den här detaljerade kanal resultat i en stor mängd spårningar skapas snabbt och kan använda lagringskapacitet. Endast aktivera detta när det är nödvändigt.
För detaljerad felsökning av händelser för omvänd proxy, finns det [omvänd proxy diagnostik guiden](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Samla in från den nya EventSource kanaler

Om du vill uppdatera diagnostik för att samla in loggar från nya EventSource kanaler som representerar ett nytt program som du använder om att distribuera, utföra samma kluster steg enligt beskrivningen ovan för att installationen av diagnostik för ett befintligt.

Uppdatera den `EtwEventSourceProviderConfiguration` -avsnittet i template.json att lägga till poster för de nya EventSource kanalerna innan du använder konfigurationen uppdatera med hjälp av den `New-AzureRmResourceGroupDeployment` PowerShell-kommando. Namnet på händelsekällan definieras som en del av din kod i Visual Studio-genererade ServiceEventSource.cs-filen.

Till exempel om din händelsekällan är Mina Eventsource, lägger du till följande kod för att placera händelser från Mina Eventsource i en tabell med namnet MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Om du vill samla in prestandaräknare eller händelseloggar, ändra Resource Manager-mallen med hjälp av exemplen i [skapa en virtuell Windows-dator med övervakning och diagnostik med en Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Kubfilen Resource Manager-mallen.

## <a name="collect-performance-counters"></a>Samla in prestandaräknare

Lägg till prestandaräknare till din ”WadCfg > DiagnosticMonitorConfiguration” i Resource Manager-mall för klustret om du vill samla in prestandavärden från klustret. Se [övervakning av programprestanda med BOMULLSTUSS](service-fabric-diagnostics-perf-wad.md) anvisningar om hur du ändrar dina `WadCfg` att samla in specifika prestandaräknare. Referens [prestandaräknare för Service Fabric](service-fabric-diagnostics-event-generation-perf.md) för en lista över prestanda prestandaräknare som rekommenderar vi att samla in.
  
Om du använder en Application Insights-sink enligt beskrivningen i avsnittet nedan och vill använda de här måtten ska visas i Application Insights, se till att lägga till mottagare namn i avsnittet ”sänkor” som ovan. Detta skickar automatiskt de prestandaräknare som konfigureras individuellt till Application Insights-resurs.


## <a name="send-logs-to-application-insights"></a>Skicka loggar till Application Insights

Övervakning och diagnostik data skickas till Application Insights (AI) kan göras som en del av konfigurationen BOMULLSTUSS. Om du vill använda AI för händelseanalys och visualisering läsa [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) att ställa in en AI Sink som en del av din ”WadCfg”.

## <a name="next-steps"></a>Nästa steg

När du har korrekt konfigurerat Azure diagnostics, visas data i Storage-tabeller från ETW och EventSource loggar. Om du väljer att använda OMS Kibana eller några andra analyser och visualisering dataplattform som inte har konfigurerats i Resource Manager-mallen direkt att se till att ställa in plattformen du väljer att läsa data från dessa lagringstabeller. Om du gör detta för OMS är relativt enkelt och förklaras i [händelse och logg analys genom OMS](service-fabric-diagnostics-event-analysis-oms.md). Application Insights är lite specialfall på detta sätt, eftersom den kan konfigureras som en del av diagnostik tilläggets konfiguration, så finns det [lämplig artikel](service-fabric-diagnostics-event-analysis-appinsights.md) om du väljer att använda AI.

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa de händelser som skickas till tabellen. Om du inte implementerar en process för att ta bort händelser från tabellen, i tabellen kommer att fortsätta att växa. För närvarande finns ett exempel på en rensning datatjänst som körs i den [Watchdog exempel](https://github.com/Azure-Samples/service-fabric-watchdog-service), och det rekommenderas att du skriver en själv, såvida det inte finns en anledning att lagra loggar utöver en tidsram för 30 eller 90 dagar.

* [Lär dig att samla in prestandaräknare eller loggar med hjälp av diagnostik-tillägget](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med OMS](service-fabric-diagnostics-event-analysis-oms.md)
