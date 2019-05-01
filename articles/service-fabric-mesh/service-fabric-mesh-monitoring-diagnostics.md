---
title: Övervakning och diagnostik i Azure Service Fabric nät program | Microsoft Docs
description: Lär dig om att övervaka och diagnostisera program i Service Fabric nät på Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939826"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Övervakning och diagnostik för Service Fabric-nät är indelade i tre typer av diagnostikdata:

- Programloggar - dessa definieras som loggar från dina behållarbaserade program, baserat på hur du har instrumenterade ditt program (t.ex. docker loggar)
- – Plattformshändelser av nät-plattformen som är relevanta för din behållaråtgärden, inklusive för närvarande behållare aktivering, inaktivering och uppsägning.
- Behållarmätvärden - mätvärden för resurs användning och prestanda för dina behållare (docker statistik)

Den här artikeln beskriver alternativ för övervakning och diagnostik för den senaste förhandsversionen.

## <a name="application-logs"></a>Programloggar

Du kan visa din dockerloggar från dina distribuerade behållare på basis av per behållare. I programmodellen Service Fabric-nät är varje behållare ett kodpaket i ditt program. Om du vill se de associerade loggarna med ett kodpaket, använder du följande kommando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Du kan använda kommandot ”az nät service och repliken” för att få namnet på repliken. Namn är ökande heltal från 0.

Här är hur detta ser ut för att visa loggar från VotingWeb.Code behållaren från röstningsprogrammet:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Behållarmätvärden 

Nät miljön Exponerar en handfull mått som anger hur dina behållare utför. Följande mått är tillgängliga via Azure portal och Azure kan du övervaka CLI:

| Mått | Beskrivning | Enheter|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu i procent | % |
| MemoryUtilization | ActualMem/AllocatedMem i procent | % |
| AllocatedCpu | CPU som allokerats enligt Azure Resource Manager-mall | Millicores |
| AllocatedMemory | Minne som allokerats enligt Azure Resource Manager-mall | MB |
| ActualCpu | Processoranvändning | Millicores |
| ActualMemory | Minnesanvändning | MB |
| ContainerStatus | 0 - ogiltig: Status för container är okänd <br> 1 - väntar: Behållaren har schemalagts att starta <br> 2 – startar: Behållaren håller startar <br> 3 – igång: Behållaren har startat <br> 4 – stoppar: Behållaren stoppas <br> 5 – stoppats: Behållaren har stoppats | Gäller inte |
| ApplicationStatus | 0 - Unknown: Status kan inte hämtas <br> 1 - klar: Programmet körs <br> 2 – uppgradera: Det finns en uppgradering pågår <br> 3 – skapa: Programmet håller på att skapas <br> 4 – tar bort: Programmet tas bort <br> 5 – misslyckades: Det gick inte att distribuera programmet | Gäller inte |
| ServiceStatus | 0 - ogiltig: Tjänsten har inte en hälsostatus <br> 1 - ok: Tjänsten är felfri  <br> 2 – varning Det kan vara något fel som kräver undersökning <br> 3 - fel: Det är något fel som behöver undersökning <br> 4 – okänt: Status kan inte hämtas | Gäller inte |
| ServiceReplicaStatus | 0 - ogiltig: Repliken har inte en hälsostatus <br> 1 - ok: Tjänsten är felfri  <br> 2 – varning Det kan vara något fel som kräver undersökning <br> 3 - fel: Det är något fel som behöver undersökning <br> 4 – okänt: Status kan inte hämtas | Gäller inte | 
| RestartCount | Antal behållare startas om | Gäller inte |

> [!NOTE]
> Värdena som ServiceStatus och ServiceReplicaStatus är samma som den [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) i Service Fabric. 

Varje mått är tillgängliga på olika dimensioner så att du kan se aggregeringar på olika nivåer. Den aktuella listan över dimensioner är följande:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Dimensionen CodePackageName är inte tillgänglig för Linux-program. 

Varje dimension motsvarar de olika komponenterna i den [modellen för Service Fabric-program](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

En fullständig lista över kommandon är tillgängliga i den [Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) men vi har lagt till några bra exempel nedan 

I varje exempel följer resurs-ID detta mönster

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-belastningen för behållare i ett program

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Minnesanvändning för varje tjänst-replik
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Omstart för varje behållare i ett fönster för 1 timme 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Genomsnittlig CPU-användningen över tjänster med namnet ”VotingWeb” i ett fönster för 1 timme
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Måttutforskaren

Metrics explorer är ett blad i portalen där du kan visualisera alla mått för programmets nät. Det här bladet är tillgänglig i programmets sida i portalen och Azure monitor-bladet, det senare som du kan använda för att visa mått för alla dina Azure-resurser som har stöd för Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Nästa steg
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Om du vill veta mer om Azure Monitor metrics-kommandon kan ta en titt på [Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
