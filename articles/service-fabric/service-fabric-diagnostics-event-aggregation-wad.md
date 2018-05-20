---
title: Azure Service Fabric händelse aggregeringen med Windows Azure-diagnostik | Microsoft Docs
description: Läs mer om sammanställa och samlar in händelser med hjälp av BOMULLSTUSS för övervakning och diagnostik av Azure Service Fabric-kluster.
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
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 38a026e8995bb7384c866dcd2f12588ca816009f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Aggregering av händelse och med Windows Azure-diagnostik
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggar från alla noder i en central plats. Med loggarna på en central plats hjälper dig att analysera och felsöka problem i klustret eller problem i program och tjänster som körs i klustret.

Ett sätt att överföra och samla in loggar är att använda Windows Azure Diagnostics (BOMULLSTUSS)-tillägget, som överför loggar till Azure Storage och har även möjlighet att skicka loggar till Azure Application Insights eller Händelsehubbar. Du kan också använda en extern process för att läsa händelser från lagring och placera dem i en analys plattform produkt som [logganalys](../log-analytics/log-analytics-service-fabric.md) eller en annan lösning för parsning av loggen.

## <a name="prerequisites"></a>Förutsättningar
Följande verktyg som används i den här artikeln:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric-plattformen händelser
Service Fabric ställer du in med ett fåtal [out box loggning kanaler](service-fabric-diagnostics-event-generation-infra.md), av vilka följande kanaler som är förkonfigurerad med tillägget att skicka övervakning och diagnostikdata till en tabell för lagring eller någon annanstans:
  * [Funktionshändelser](service-fabric-diagnostics-event-generation-operational.md): att åtgärder som utförs av Service Fabric-plattformen. Exempel: skapa program och tjänster, nod tillståndsändringar och information om uppgradering. Dessa orsakat som loggar händelsen ETW Tracing for Windows)
  * [Reliable Actors programmering modellen händelser](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programming modellen händelser](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Distribuera diagnostik tillägget via portalen
Det första steget i att samla in loggar är att distribuera diagnostik tillägget på virtuella skala uppsättning noder i Service Fabric-klustret. Diagnostik-tillägget samlar in loggar på varje virtuell dator och överför dem till lagringskontot som du anger. Följande steg beskriver hur du gör detta för nya och befintliga kluster via Azure-portalen och Azure Resource Manager-mallar.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuera diagnostik-tillägget som en del av klustret har skapats via Azure portal
När du skapar klustret i konfigurationssteget klustret, expandera valfria inställningar och kontrollera att diagnostik är **på** (standardinställningen).

![Azure diagnostikinställningar för klustret skapas i portalen](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Vi rekommenderar starkt att du hämtar mallen **innan du klickar på Skapa** i det sista steget. Mer information finns i [ställer in ett Service Fabric-kluster med en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md). Du måste göra ändringar på vilka kanaler (ovan) samla in data från mallen.

![Kluster-mall](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu när du samla händelser i Azure Storage, [konfigurera logganalys](service-fabric-diagnostics-oms-setup.md) att få insikter och fråga dem i logganalys-portalen

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa de händelser som skickas till tabeller. Om du inte implementerar en process för att ta bort händelser från tabellen, i tabell fortsätter att växa (standard fästpunkten är 50 GB). Instruktioner om hur du ändrar det här är [ytterligare nedan i den här artikeln](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Det finns också ett exempel på en rensning datatjänst som körs i den [Watchdog exempel](https://github.com/Azure-Samples/service-fabric-watchdog-service), och det rekommenderas att du skriver en själv, såvida det inte finns en anledning att lagra loggar utöver en tidsram för 30 eller 90 dagar.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Distribuera diagnostik tillägget via Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Skapa ett kluster med filnamnstillägget diagnostik
Om du vill skapa ett kluster med hjälp av hanteraren för filserverresurser, som du behöver lägga till diagnostik konfigurationen JSON till fullständig Resource Manager-mallen innan du skapar klustret. Vi ger en fem-VM klustret Resource Manager exempelmall diagnostik-konfiguration som lagts till som en del av våra exempel för Resource Manager-mall. Du kan se den på den här platsen i galleriet Azure-exempel: [kluster med fem noder med diagnostik Resource Manager mallen exempel](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Öppna filen azuredeploy.json för att se inställningen diagnostik i Resource Manager-mallen, och Sök efter **IaaSDiagnostics**. Om du vill skapa ett kluster med hjälp av den här mallen, Välj den **till Azure** knappen tillgänglig vid föregående länk.

Du kan hämta Resource Manager-exempel, göra ändringar, och skapa ett kluster med den ändrade mallen med hjälp av den `New-AzureRmResourceGroupDeployment` i Azure PowerShell-fönster. Se följande kod för de parametrar som du anger för att kommandot. Detaljerad information om hur du distribuerar en resursgrupp med hjälp av PowerShell finns i artikeln [distribuera en resursgrupp med Azure Resource Manager-mallen](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Lägga till filnamnstillägget diagnostik till ett befintligt kluster
Om du har ett befintligt kluster som inte har distribuerats diagnostik, kan du lägga till eller uppdatera det. via mallen klustret. Ändra Resource Manager-mallen som används för att skapa det befintliga klustret eller hämta mallen från portalen, enligt beskrivningen ovan. Ändra template.json-filen genom att utföra följande uppgifter:

Lägga till en ny storage-resurs i mallen genom att lägga till avsnittet resurser.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Lägg till avsnittet Parametrar precis efter storage-konto definitioner, mellan `supportLogStorageAccountName`. Ersätt platshållartexten *lagringskontonamnet här* med namnet på det lagringskonto som du vill.

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
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
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

> [!TIP]
> Om du ska distribuera behållare till klustret, aktivera BOMULLSTUSS för docker stats genom att lägga till den till din **WadCfg > DiagnosticMonitorConfiguration** avsnitt.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Uppdatera lagringskvoten

Eftersom de tabeller som fylls i av tillägget växer tills kvoten träffar, du kanske vill minska kvotstorleken. Standardvärdet är 50 GB och kan konfigureras i mallen under den `overallQuotainMB` fältet under `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Logga samling konfigurationer
Loggar från ytterligare kanaler finns också tillgängliga för samlingen, här är några av de vanligaste konfigurationerna som du kan göra i mallen för kluster som körs i Azure.

* Drift kanal - Base: Aktiverad som standard, övergripande åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod, kommer ett nytt program som distribueras, eller en uppgradering återställning osv. En lista över händelser som avser [drifthändelser för kanal](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Drift kanaler – detaljerad: Detta inkluderar hälsorapporter och belastningsutjämning beslut plus allt i den grundläggande operativa kanalen. Dessa händelser genereras av systemet eller din kod med hjälp av hälsotillstånd eller läsa in reporting API: er som [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) eller [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Visa dessa händelser i Loggboken för Visual Studio diagnostiska lägga till ”Microsoft-ServiceFabric:4:0x4000000000000008” i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data och Messaging kanal - Base: kritiska loggar och händelser som genererats i messaging (för närvarande endast ReverseProxy) och sökvägen, dessutom till detaljerad operativa kanalen loggar. Dessa händelser är fel och andra viktiga problem i ReverseProxy för begäranden som begäranden som bearbetas. **Det här är våra rekommendationer för omfattande loggning**. Om du vill visa dessa händelser i Loggboken i Visual Studio-diagnostik, lägger du till ”Microsoft-ServiceFabric:4:0x4000000000000010” i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Messaging kanaler – detaljerad: utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret och detaljerad operativa kanalen. För detaljerad felsökning av alla händelser för omvänd proxy, referera till den [omvänd proxy diagnostik guiden](service-fabric-reverse-proxy-diagnostics.md).  Om du vill visa dessa händelser i Loggboken för Visual Studio diagnostiska lägger du till ”Microsoft-ServiceFabric:4:0x4000000000000020” i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Den här kanalen har en mycket stor volym med händelser, aktiverar händelseinsamling från den här detaljerade kanal resultat i en stor mängd spårningar skapas snabbt och kan använda lagringskapacitet. Endast aktivera detta om det är absolut nödvändigt.


Så här aktiverar du den **grundläggande Data och Messaging kanal** våra rekommendationer för omfattande loggning, den `EtwManifestProviderConfiguration` i den `WadCfg` av mallen som ser ut som följande:

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
                "scheduledTransferKeywordFilter": "4611686018427387928",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

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

Övervakning och diagnostik data skickas till Application Insights (AI) kan göras som en del av konfigurationen BOMULLSTUSS. Om du vill använda AI för händelseanalys och visualisering läsa [hur du ställer in en AI sink](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template) som en del av din ”WadCfg”.

## <a name="next-steps"></a>Nästa steg

När du har korrekt konfigurerat Azure diagnostics, visas data i Storage-tabeller från ETW och EventSource loggar. Om du väljer att använda Log Analytics Kibana eller några andra analyser och visualisering dataplattform som inte har konfigurerats i Resource Manager-mallen direkt att se till att ställa in plattformen du väljer att läsa data från dessa lagringstabeller. På så vis för Log Analytics är relativt trivial och förklaras i [händelse och logga analysis](service-fabric-diagnostics-event-analysis-oms.md). Application Insights är lite specialfall på detta sätt, eftersom den kan konfigureras som en del av diagnostik tilläggets konfiguration, så finns det [lämplig artikel](service-fabric-diagnostics-event-analysis-appinsights.md) om du väljer att använda AI.

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa de händelser som skickas till tabellen. Om du inte implementerar en process för att ta bort händelser från tabellen, i tabellen kommer att fortsätta att växa. För närvarande finns ett exempel på en rensning datatjänst som körs i den [Watchdog exempel](https://github.com/Azure-Samples/service-fabric-watchdog-service), och det rekommenderas att du skriver en själv, såvida det inte finns en anledning att lagra loggar utöver en tidsram för 30 eller 90 dagar.

* [Lär dig att samla in prestandaräknare eller loggar med hjälp av diagnostik-tillägget](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med logganalys](service-fabric-diagnostics-event-analysis-oms.md)
