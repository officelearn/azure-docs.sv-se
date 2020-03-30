---
title: Händelseaggregering med Windows Azure Diagnostics
description: Lär dig mer om hur du samlar in och samlar in händelser med WAD för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282502"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Händelseaggregering och samling med Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att ha loggarna på en central plats kan du analysera och felsöka problem i klustret eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda WAD-tillägget (Windows Azure Diagnostics), som överför loggar till Azure Storage, och även har möjlighet att skicka loggar till Azure Application Insights eller Event Hubs. Du kan också använda en extern process för att läsa händelser från lagring och placera dem i en analysplattformsprodukt, till exempel [Azure Monitor-loggar](../log-analytics/log-analytics-service-fabric.md) eller en annan loggtolkningslösning.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Följande verktyg används i den här artikeln:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Plattformshändelser för Service Fabric
Service Fabric ställer in dig med några [färdiga loggningskanaler,](service-fabric-diagnostics-event-generation-infra.md)varav följande kanaler är förkonfigurerade med tillägget för att skicka övervaknings- och diagnostikdata till en lagringstabell eller någon annanstans:
  * [Drifthändelser](service-fabric-diagnostics-event-generation-operational.md): åtgärder på högre nivå som Service Fabric-plattformen utför. Exempel på detta är att skapa program och tjänster, nodtillståndsändringar och uppgraderingsinformation. Dessa avges som ETW-loggar (Event Tracing for Windows)
  * [Tillförlitliga aktörer programmering modell händelser](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programmeringsmodellhändelser](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Distribuera diagnostiktillägget via portalen
Det första steget i att samla in loggar är att distribuera diagnostiktillägget på den virtuella datorns skalningsuppsättningsnoder i Service Fabric-klustret. Tillägget Diagnostik samlar in loggar på varje virtuell dator och överför dem till det lagringskonto som du anger. I följande steg beskrivs hur du åstadkommer detta för nya och befintliga kluster via Azure-portalen och Azure Resource Manager-mallarna.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuera diagnostiktillägget som en del av klusterskapande via Azure Portal
När du skapar klustret expanderar du de valfria inställningarna i klusterkonfigurationssteget och kontrollerar att diagnostik är inställt **på På** (standardinställningen).

![Azure Diagnostics-inställningar i portalen för att skapa kluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Vi rekommenderar starkt att du laddar ned mallen **innan du klickar på Skapa** i det sista steget. Mer information finns i [Konfigurera ett Service Fabric-kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md). Du behöver mallen för att göra ändringar på vilka kanaler (som visas ovan) för att samla in data från.

![Klustermall](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu när du samlar in händelser i Azure Storage [konfigurerar du Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md) för att få insikter och frågar dem i Azure Monitor-loggportalen

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller trimma de händelser som skickas till tabellerna. Om du inte implementerar en process för att ta bort händelser från tabellen fortsätter tabellen att växa (standardtaket är 50 GB). Instruktioner om hur du ändrar detta finns [längre ner i den här artikeln](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Dessutom finns det ett exempel på en data grooming tjänst som körs i [Watchdog provet](https://github.com/Azure-Samples/service-fabric-watchdog-service), och det rekommenderas att du skriver en för dig själv också, om det inte finns en bra anledning för dig att lagra loggar utöver en 30 eller 90 dagars tidsram.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Distribuera diagnostiktillägget via Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Skapa ett kluster med diagnostiktillägget
Om du vill skapa ett kluster med Hjälp av Resource Manager måste du lägga till diagnostikkonfigurationen JSON i den fullständiga Resource Manager-mallen. Vi tillhandahåller ett exempel på en resurshanterare för fem virtuella datorer med diagnostikkonfiguration som läggs till som en del av våra Resource Manager-mallexempel. Du kan se den på den här platsen i azure [samples-galleriet: Femnodskluster med exempel på diagnostikresurshanteraren](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Om du vill visa diagnostikinställningen i mallen Resurshanteraren öppnar du filen azuredeploy.json och söker efter **IaaSDiagnostics**. Om du vill skapa ett kluster med den här mallen väljer du knappen **Distribuera till Azure** som är tillgänglig på föregående länk.

Du kan också hämta exempelt Resource Manager, göra ändringar i det och skapa `New-AzResourceGroupDeployment` ett kluster med den ändrade mallen med kommandot i ett Azure PowerShell-fönster. Se följande kod för de parametrar som du skickar in till kommandot. Detaljerad information om hur du distribuerar en resursgrupp med hjälp av PowerShell finns i artikeln [Distribuera en resursgrupp med Azure Resource Manager-mallen](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Lägga till diagnostiktillägget i ett befintligt kluster
Om du har ett befintligt kluster som inte har diagnostik distribuerat kan du lägga till eller uppdatera det via klustermallen. Ändra den Resource Manager-mall som används för att skapa det befintliga klustret eller hämta mallen från portalen enligt beskrivningen tidigare. Ändra filen template.json genom att utföra följande uppgifter:

Lägg till en ny lagringsresurs i mallen genom att lägga till i resursavsnittet.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Lägg sedan till i parameteravsnittet strax efter `supportLogStorageAccountName`lagringskontodefinitionerna mellan . Ersätt namnet på platshållarens *textlagringskonto som finns här* med namnet på det lagringskonto du vill ha.

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
Uppdatera sedan `VirtualMachineProfile` avsnittet i filen template.json genom att lägga till följande kod i tilläggsmatrisen. Var noga med att lägga till ett kommatecken i början eller slutet, beroende på var det är infogat.

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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

När du har ändrat filen template.json enligt beskrivningen publicerar du om resurshanterarens mall. Om mallen exporterades publiceras mallen genom att köra filen deploy.ps1. När du har **distribuerat,** se till att **ProvisioningState** har lyckats .

> [!TIP]
> Om du ska distribuera behållare till klustret aktiverar du WAD för att hämta dockerstatistik genom att lägga till den i avsnittet **WadCfg > DiagnosticMonitorConfiguration.**
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Uppdatera lagringskvot

Eftersom tabellerna som fylls i i tillägget växer tills kvoten har nåtts kan du överväga att minska kvotstorleken. Standardvärdet är 50 GB och kan konfigureras `overallQuotaInMB` i mallen under fältet under`DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfigurationer för logginsamling
Loggar från ytterligare kanaler är också tillgängliga för insamling, här är några av de vanligaste konfigurationerna du kan göra i mallen för kluster som körs i Azure.

* Operativ kanal - Bas: Aktiverad som standard, åtgärder på hög nivå som utförs av Service Fabric och klustret, inklusive händelser för en nod som kommer upp, ett nytt program som distribueras eller en uppgraderingsåterläpning, etc. En lista över händelser finns i [Operational Channel Events](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Operativ kanal - Detaljerad: Detta inkluderar hälsorapporter och beslut om belastningsutjämning, plus allt i basens operativa kanal. Dessa händelser genereras av antingen systemet eller din kod med hjälp av api:er för hälso- eller inläsningsrapportering, till exempel [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) eller [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Om du vill visa dessa händelser i Visual Studios diagnostikhändelsevisaren lägger du till "Microsoft-ServiceFabric:4:0x4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data- och meddelandekanal - Bas: Kritiska loggar och händelser som genereras i meddelanden (för närvarande endast ReverseProxy) och datasökvägen, förutom detaljerade operativa kanalloggar. Dessa händelser är begäran bearbetning fel och andra kritiska problem i ReverseProxy, samt begäranden bearbetas. **Detta är vår rekommendation för omfattande avverkning.** Om du vill visa dessa händelser i Visual Studios diagnostikhändelsevisare lägger du till "Microsoft-ServiceFabric:4:0x4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Messaging Channel - Detaljerad: Utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret och den detaljerade operativa kanalen. Detaljerad felsökning av alla omvända proxyhändelser finns i [guiden för omvänd proxydiagnostik](service-fabric-reverse-proxy-diagnostics.md).  Om du vill visa dessa händelser i Visual Studios diagnostikhändelsevisare lägger du till "Microsoft-ServiceFabric:4:0x4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Den här kanalen har en mycket hög mängd händelser, vilket möjliggör händelseinsamling från den här detaljerade kanalen resulterar i att många spår genereras snabbt och kan förbruka lagringskapacitet. Slå bara på detta om det är absolut nödvändigt.


För att aktivera **basenheten** vår rekommendation för omfattande loggning `EtwManifestProviderConfiguration` med `WadCfg` minsta möjliga buller, Skulle I mallen se ut så här:

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

## <a name="collect-from-new-eventsource-channels"></a>Samla in från nya EventSource-kanaler

Om du vill uppdatera diagnostik för att samla in loggar från nya EventSource-kanaler som representerar ett nytt program som du ska distribuera utför du samma steg som tidigare beskrivits för installationen av diagnostik för ett befintligt kluster.

Uppdatera `EtwEventSourceProviderConfiguration` avsnittet i filen template.json om du vill lägga till poster för de `New-AzResourceGroupDeployment` nya EventSource-kanalerna innan du installerar konfigurationsuppdateringen med kommandot PowerShell. Namnet på händelsekällan definieras som en del av koden i den Visual Studio-genererade ServiceEventSource.cs filen.

Om händelsekällan till exempel heter My-Eventsource lägger du till följande kod för att placera händelserna från Min-Eventsource i en tabell med namnet MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Om du vill samla in prestandaräknare eller händelseloggar ändrar du Resource Manager-mallen med hjälp av exemplen i [Skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av en Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Publicera sedan om mallen Resurshanteraren.

## <a name="collect-performance-counters"></a>Samla in prestandaräknare

Om du vill samla in prestandamått från klustret lägger du till prestandaräknarna i "WadCfg > DiagnosticMonitorConfiguration" i Resource Manager-mallen för klustret. Se [Prestandaövervakning med WAD](service-fabric-diagnostics-perf-wad.md) för `WadCfg` steg om hur du ändrar din för att samla in specifika prestandaräknare. Referensservice [fabric-prestandaräknare](service-fabric-diagnostics-event-generation-perf.md) för en lista över prestandaräknare som vi rekommenderar att samla in.
  
Om du använder en programstatistikmottagare, som beskrivs i avsnittet nedan, och vill att dessa mått ska visas i Application Insights, kontrollerar du att sink-namnet läggs till i avsnittet "sänkor" enligt ovan. Detta skickar automatiskt prestandaräknarna som är individuellt konfigurerade till application insights-resursen.


## <a name="send-logs-to-application-insights"></a>Skicka loggar till Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurera programinsikter med WAD

>[!NOTE]
>Detta gäller för närvarande endast Windows-kluster.

Det finns två huvudsakliga sätt att skicka data från WAD till Azure Application Insights, vilket uppnås genom att lägga till en Application Insights-diskho i WAD-konfigurationen, via Azure-portalen eller via en Azure Resource Manager-mall.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Lägga till en instrumenteringsnyckel för programinsikter när du skapar ett kluster i Azure-portalen

![Lägga till en AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

När du skapar ett kluster, om diagnostik är aktiverat ,, visas ett valfritt fält för att ange en instrumenteringsnyckel för programinsikter. Om du klistrar in programstatistiknyckeln här konfigureras programstatistikmottagaren automatiskt för dig i resurshanteraren-mallen som används för att distribuera klustret.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Lägga till programstatistikmottagaren i resource manager-mallen

I "WadCfg" i resource manager-mallen lägger du till en "Sink" genom att inkludera följande två ändringar:

1. Lägg till handfänkens konfiguration `DiagnosticMonitorConfiguration` direkt efter att deklarerat har slutförts:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Inkludera `DiagnosticMonitorConfiguration` handfatet i genom att `DiagnosticMonitorConfiguration` lägga `WadCfg` till följande `EtwProviders` rad i (precis innan deklareras):

    ```json
    "sinks": "applicationInsights"
    ```

I båda kodavsnitten före användes namnet "applicationInsights" för att beskriva diskhon. Detta är inte ett krav och så länge namnet på diskbänken ingår i "sänkor", kan du ställa in namnet på valfri sträng.

Loggar från klustret visas för närvarande som **spårningar** i Application Insights loggvisare. Eftersom de flesta av de spår som kommer från plattformen är av nivå "Informativ", kan du också överväga att ändra diskbänken konfigurationen för att bara skicka loggar av typen "Varning" eller "Fel". Detta kan göras genom att lägga till "Kanaler" i diskbänken, vilket visas i [den här artikeln](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Om du använder en felaktig Application Insights-nyckel antingen i portalen eller i resource manager-mallen måste du ändra nyckeln manuellt och uppdatera klustret/distribuera om den.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azure-diagnostik på rätt sätt ser du data i dina lagringstabeller från ETW- och EventSource-loggarna. Om du väljer att använda Azure Monitor-loggar, Kibana eller någon annan dataanalys- och visualiseringsplattform som inte är direkt konfigurerad i Resource Manager-mallen, se till att ställa in valfri plattform för att läsa i data från dessa lagringstabeller. Att göra detta för Azure Monitor-loggar är relativt trivialt och förklaras i [händelse- och logganalys](service-fabric-diagnostics-event-analysis-oms.md). Application Insights är lite av ett specialfall i den här meningen, eftersom det kan konfigureras som en del av diagnostiktilläggskonfigurationen, så se [lämplig artikel](service-fabric-diagnostics-event-analysis-appinsights.md) om du väljer att använda AI.

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller trimma de händelser som skickas till tabellen. Om du inte implementerar en process för att ta bort händelser från tabellen fortsätter tabellen att växa. För närvarande finns det ett exempel på en data grooming tjänst som körs i [Watchdog provet](https://github.com/Azure-Samples/service-fabric-watchdog-service), och det rekommenderas att du skriver en för dig själv också, om det inte finns en bra anledning för dig att lagra loggar utöver en 30 eller 90 dagars tidsram.

* [Lär dig hur du samlar in prestandaräknare eller loggar med hjälp av tillägget Diagnostik](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Händelseanalys och visualisering med programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)
* [Händelseanalys och visualisering med programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)
