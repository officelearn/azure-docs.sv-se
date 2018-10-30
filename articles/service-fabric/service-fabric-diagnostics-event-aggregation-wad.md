---
title: Azure Service Fabric händelse aggregering med Windows Azure-diagnostik | Microsoft Docs
description: Läs mer om sammanställa och samla in händelser med WAD för övervakning och diagnostik för Azure Service Fabric-kluster.
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
ms.openlocfilehash: d670b90404d441876727336fc50a848965082de5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232503"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Händelsen aggregering och samling med Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggar från alla noder i en central plats. Med loggarna på en central plats hjälper dig att analysera och felsöka problem i ditt kluster eller problem i program och tjänster som körs i klustret.

Ett sätt att överföra och samla in loggar är att använda tillägg för Windows Azure Diagnostics SÄKERHETSSPECIFIKA som överför loggar till Azure Storage och har också möjlighet att skicka loggarna till Azure Application Insights eller Event Hubs. Du kan också använda en extern process för att läsa händelser från storage och placera dem i en plattform produkten analys som [Log Analytics](../log-analytics/log-analytics-service-fabric.md) eller en annan lösning för parsning av loggen.

## <a name="prerequisites"></a>Förutsättningar
Följande verktyg används i den här artikeln:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric-plattformshändelser
Service Fabric ställer du in med ett fåtal [out-of the box loggning kanaler](service-fabric-diagnostics-event-generation-infra.md), av vilka följande kanaler som är förkonfigurerad med tillägget för att skicka övervakning och diagnostikdata i en lagringstabell eller någon annanstans:
  * [Drifthändelser](service-fabric-diagnostics-event-generation-operational.md): på högre nivå åtgärder som utförs av Service Fabric-plattformen. Exempel innefattar skapandet av program och tjänster, noden tillståndsändringar och information om uppgradering. Dessa genereras som för Windows ETW (Event Tracing) loggar
  * [Reliable Actors programming modellhändelser](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programming modellhändelser](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Distribuera diagnostiktillägget via portalen
Det första steget i att samla in loggar är att distribuera diagnostiktillägget på VM scale set noder i Service Fabric-klustret. Diagnostiktillägget samlar in loggar på varje virtuell dator och överför dem till det lagringskonto som du anger. Följande steg beskriver hur du gör detta för nya och befintliga kluster via Azure portal och Azure Resource Manager-mallar.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuera diagnostiktillägget som en del av Skapa kluster via Azure-portalen
När du skapar ditt kluster i konfigurationssteg för klustret, expandera valfria inställningar och kontrollera att diagnostik är inställd på **på** (standardinställningen).

![Azure Diagnostics-inställningar i portalen för att skapa kluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Vi rekommenderar starkt att du har hämtat mallen **innan du klickar på Skapa** i det sista steget. Mer information finns att [konfigurera ett Service Fabric-kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md). Du måste göra ändringar på vilka kanaler (listade ovan) för att samla in data från mallen.

![Kluster-mall](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu när du aggregering av händelser i Azure Storage, [konfigurera Log Analytics](service-fabric-diagnostics-oms-setup.md) att få insikter och skicka frågor till dem i Log Analytics-portalen

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa de händelser som skickas till tabeller. Om du inte har implementerat en process för att ta bort händelser från tabellen, tabellen fortsätter att växa (standard fästpunkten är 50 GB). Instruktioner om hur du ändrar det här är [ytterligare nedan i den här artikeln](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Det finns också ett exempel på en rensning tjänst som körs i den [Watchdog exempel](https://github.com/Azure-Samples/service-fabric-watchdog-service), och vi rekommenderar att du skriver en själv, om det inte finns en anledning att lagra loggar utöver en tidsram för 30 eller 90 dagar.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Distribuera det via Azure Resource Manager-diagnostiktillägget

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Skapa ett kluster med diagnostics-tillägg
Om du vill skapa ett kluster med hjälp av Resource Manager, som du behöver lägga till diagnostikkonfigurationen JSON till den fullständiga Resource Manager-mallen innan du skapar klustret. Vi tillhandahåller ett exempel fem VM-kluster Resource Manager-mall med diagnostiky har lagts till som en del av vår Resource Manager-exempelmallar. Du kan se dem på den här platsen i Azure-exempelgalleri: [kluster med fem noder med exempel för diagnostik Resource Manager-mall](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Om du vill visa inställningen för diagnostik i Resource Manager-mallen, öppna filen azuredeploy.JSON och Sök efter **IaaSDiagnostics**. Om du vill skapa ett kluster med hjälp av den här mallen, Välj den **distribuera till Azure** knappen som är tillgängliga på den föregående länken.

Du kan också du kan hämta exemplet för Resource Manager, göra ändringar i den och skapa ett kluster med den ändrade mallen med hjälp av den `New-AzureRmResourceGroupDeployment` i en Azure PowerShell-fönster. Se följande kod för parametrar som du anger i kommandot. Detaljerad information om hur du distribuerar en resursgrupp med hjälp av PowerShell finns i artikeln [distribuera en resursgrupp med Azure Resource Manager-mallen](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Lägg till diagnostiktillägget i ett befintligt kluster
Om du har ett befintligt kluster som inte har distribuerats diagnostik kan du lägga till eller uppdatera det. via mallen kluster. Ändra Resource Manager-mallen som används för att skapa det befintliga klustret eller ladda ned mallen från portalen enligt beskrivningen ovan. Ändra filen template.json genom att utföra följande uppgifter:

Lägg till en ny storage-resurs i mallen genom att lägga till resurserna.

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

 Lägg till parameteravsnittet precis efter definitioner för storage-konto, mellan `supportLogStorageAccountName`. Ersätt platshållartexten *lagringskontonamn här* med namnet på det lagringskonto som du vill ha.

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
Sedan uppdaterar den `VirtualMachineProfile` i filen template.json genom att lägga till följande kod i matrisen tillägg. Glöm inte att lägga till ett kommatecken i början eller slutet, beroende på där den infogas.

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

När du har ändrat filen template.json enligt publicera om Resource Manager-mallen. Om mallen exporterades, publicerar körs filen deploy.ps1 mallen. När du distribuerar kan du se till att **ProvisioningState** är **lyckades**.

> [!TIP]
> Om du ska distribuera behållare till klustret, aktivera WAD att den använder docker statistik genom att lägga till den till din **WadCfg > DiagnosticMonitorConfiguration** avsnittet.
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

Eftersom de tabeller som har lagts till av tillägget växer tills kvoten uppnås, kan du överväga att minska kvotstorleken. Standardvärdet är 50 GB och kan konfigureras i mallen under den `overallQuotainMB` fältet under `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfigurationer för log-samling
Loggar från ytterligare kanaler finns också tillgängliga för samlingen, här är några av de vanligaste konfigurationerna som du kan göra i mallen för kluster som körs i Azure.

* Drift kanal - bas: Aktiverat som standard, avancerade åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod som kommer upp kan ett nytt program som distribueras, eller en uppgradering återställning osv. En lista över händelser som avser [drifthändelser för kanal](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Drift kanaler – detaljerad: Detta inkluderar rapporter om hälsotillstånd och belastningsutjämning beslut, plus allt i den grundläggande operativa kanalen. Dessa händelser genereras av systemet eller din kod med hjälp av hälsotillstånd eller läsa in reporting API: er som [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) eller [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Visa dessa händelser i Loggboken i Visual Studio-diagnostik Lägg till ”Microsoft-ServiceFabric:4:0x4000000000000008” i listan över ETW-leverantörer.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data och Messaging, kanal - bas: kritiska loggar och händelser som genererats i meddelanden (för närvarande endast ReverseProxy) och datasökväg, dessutom till detaljerad användningskanal loggar. Dessa händelser är behandling av fel och andra viktiga problem i ReverseProxy, samt begäranden som bearbetas. **Det här är våra rekommendationer för omfattande loggning**. Du kan visa dessa händelser i Loggboken i Visual Studio-diagnostik genom att lägga till ”Microsoft-ServiceFabric:4:0x4000000000000010” i listan över ETW-leverantörer.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Messaging, kanal - detaljerad: utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret och detaljerad operativa kanalen. För detaljerad felsökning för alla händelser för omvänd proxy kan referera till den [omvänd proxy diagnostik guiden](service-fabric-reverse-proxy-diagnostics.md).  Om du vill visa dessa händelser i Visual Studio diagnostiska Loggboken, lägger du till ”Microsoft-ServiceFabric:4:0x4000000000000020” i listan över ETW-leverantörer.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Den här kanalen har ett mycket stort antal händelser, hur du aktiverar händelseinsamling från den här detaljerade kanal resultat i en massa spårningar som skapas snabbt och kan använda lagringskapacitet. Endast aktivera detta om det är absolut nödvändigt.


Att aktivera den **Base drift kanal** våra rekommendationer för omfattande loggning med minsta möjliga störningar, för den `EtwManifestProviderConfiguration` i den `WadCfg` av mallen skulle se ut så här:

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
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Samla in från den nya EventSource kanaler

Om du vill uppdatera diagnostik för att samla in loggar från nya EventSource kanaler som representerar ett nytt program som att du är om att distribuera, utför samma kluster steg som tidigare angivits för installation av diagnostik för ett befintligt.

Uppdatera den `EtwEventSourceProviderConfiguration` avsnitt i filen template.json lägga till poster för de nya EventSource kanalerna innan du tillämpar konfigurationen uppdatera med hjälp av den `New-AzureRmResourceGroupDeployment` PowerShell-kommando. Namnet på händelsekällan definieras som en del av din kod i Visual Studio-genererade ServiceEventSource.cs-filen.

Exempel: om din händelsekälla heter Min Eventsource, lägger du till följande kod för att placera händelser från min Eventsource i en tabell med namnet MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Om du vill samla in prestandaräknare eller händelseloggar, ändra Resource Manager-mallen med hjälp av de exempel som tillhandahålls i [skapa en virtuell Windows-dator med övervakning och diagnostik med en Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sedan publicera Resource Manager-mallen.

## <a name="collect-performance-counters"></a>Samla in prestandaräknare

Lägg till prestandaräknare till din ”WadCfg > DiagnosticMonitorConfiguration” i Resource Manager-mall för klustret om du vill samla in prestandavärden från klustret. Se [prestandaövervakning med WAD](service-fabric-diagnostics-perf-wad.md) anvisningar om hur du ändrar din `WadCfg` att samla in specifika prestandaräknare. Referens [prestandaräknare för Service Fabric](service-fabric-diagnostics-event-generation-perf.md) för en lista över prestanda prestandaräknare som rekommenderar vi att samla in.
  
Om du använder en Application Insights-mottagare, enligt beskrivningen i avsnittet nedan och vill att de här måtten ska visas i Application Insights, se till att lägga till namnet på mottagaren i avsnittet ”mottagare” enligt ovan. Prestandaräknare som konfigureras individuellt skickas automatiskt till Application Insights-resursen.


## <a name="send-logs-to-application-insights"></a>Skicka loggar till Application Insights

Övervakning och diagnostik data skickas till Application Insights (AI) kan göras som en del av konfigurationen av WAD. Om du väljer att använda AI för händelseanalys och visualisering, kan du läsa [hur du ställer in en AI-sink](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template) som en del av din ”WadCfg”.

## <a name="next-steps"></a>Nästa steg

När du har korrekt konfigurerat Azure-diagnostik, visas data i ditt Storage-tabeller från ETW- och EventSource loggarna. Om du väljer att använda Log Analytics, Kibana eller andra data analys och visualisering plattformar som inte har konfigurerats i Resource Manager-mallen direkt, se till att konfigurera plattform för att läsa data från lagringstabellerna. Gör detta för Log Analytics är relativt enkelt och förklaras i [händelse och log analysis](service-fabric-diagnostics-event-analysis-oms.md). Application Insights är en del specialfall i detta avseende, eftersom den kan konfigureras som en del av konfigurationen av Diagnostiktillägget, så finns det [lämplig artikeln](service-fabric-diagnostics-event-analysis-appinsights.md) om du väljer att använda AI.

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa de händelser som skickas till tabellen. Om du inte implementerar en process för att ta bort händelser från tabellen, i tabellen kommer att fortsätta att växa. För närvarande finns ett exempel på en rensning tjänst som körs i den [Watchdog exempel](https://github.com/Azure-Samples/service-fabric-watchdog-service), och vi rekommenderar att du skriver en själv, om det inte finns en anledning att lagra loggar utöver en tidsram för 30 eller 90 dagar.

* [Lär dig att samla in prestandaräknare eller loggar med hjälp av Diagnostics-tillägg](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
