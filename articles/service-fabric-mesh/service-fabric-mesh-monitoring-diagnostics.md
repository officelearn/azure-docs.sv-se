---
title: Övervakning och diagnostik i Azure Service Fabric Mesh-appar
description: Lär dig mer om övervakning och diagnos av program i Service Fabric Mesh på Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498155"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Övervakning och diagnostik för Service Fabric Mesh kategoriseras i tre huvudtyper av diagnostikdata:

- Programloggar - dessa definieras som loggar från dina containerprogram, baserat på hur du har instrumenterat ditt program (t.ex. dockerloggar)
- Plattformshändelser - händelser från Mesh-plattformen som är relevant för din behållaråtgärd, inklusive behållareaktivering, avaktivering och avslutning.
- Behållarmått - resursutnyttjande och prestandamått för dina behållare (dockerstatistik)

I den här artikeln beskrivs alternativen för övervakning och diagnostik för den senaste förhandsversionen.

## <a name="application-logs"></a>Programloggar

Du kan visa dina dockerloggar från dina distribuerade behållare, per behållare. I programmodellen Service Fabric Mesh är varje behållare ett kodpaket i ditt program. Så här visar du de associerade loggarna med ett kodpaket:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Du kan använda kommandot "az mesh service-replica" för att hämta repliknamnet. Repliknamn ökar heltalen från 0.

Här är vad detta ser ut för att se loggarna från VotingWeb.Code behållaren från röstningsprogrammet:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Behållarmått 

Mesh-miljön visar en handfull mått som anger hur dina behållare fungerar. Följande mått är tillgängliga via Azure-portalen och Azure monitor CLI:

| Mått | Beskrivning | Enheter|
|----|----|----|
| CpuUtilization (CpuUtilization) | ActualCpu/AllocatedCpu i procent | % |
| Minnesututbyggning | ActualMem/AllocatedMem i procent | % |
| AllocatedCpu | Processor allokerad enligt Azure Resource Manager-mall | Millicores (millicore) |
| Allokerad minne | Minne allokerat enligt Azure Resource Manager-mall | MB |
| ActualCpu (1990) | CPU-användning | Millicores (millicore) |
| FaktiskaMinne | Minnesanvändning | MB |
| BehållareStatus | 0 - Ogiltig: Behållarstatusen är okänd <br> 1 - Väntande: Behållaren har planerat att starta <br> 2 - Start: Behållaren är i färd med att starta <br> 3 - Startade: Behållaren har startats <br> 4 - Stoppa: Behållaren stoppas <br> 5 - Stoppad: Behållaren har stoppats | Ej tillämpligt |
| ProgramStatus | 0 - Okänd: Statusen kan inte hämtas <br> 1 - Klar: Programmet körs framgångsrikt <br> 2 - Uppgradering: Det pågår en uppgradering <br> 3 - Skapa: Programmet skapas <br> 4 - Ta bort: Programmet tas bort <br> 5 - Misslyckades: Programmet kunde inte distribueras | Ej tillämpligt |
| Tjänststatus | 0 - Ogiltig: Tjänsten har för närvarande ingen hälsostatus <br> 1 - Ok: Tjänsten är hälsosam  <br> 2 - Varning: Det kan vara något fel som kräver utredning <br> 3 - Fel: Det är något fel som behöver undersökas <br> 4 - Okänd: Statusen kan inte hämtas | Ej tillämpligt |
| ServiceReplicaStatus | 0 - Ogiltig: Repliken har för närvarande ingen hälsostatus <br> 1 - Ok: Tjänsten är hälsosam  <br> 2 - Varning: Det kan vara något fel som kräver utredning <br> 3 - Fel: Det är något fel som behöver undersökas <br> 4 - Okänd: Statusen kan inte hämtas | Ej tillämpligt | 
| RestartCount(RestartCount) | Antal omstarter av behållare | Ej tillämpligt |

> [!NOTE]
> Värdena ServiceStatus och ServiceReplicaStatus är desamma som [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) i service fabric. 

Varje mått är tillgängligt på olika dimensioner så att du kan se aggregat på olika nivåer. Den aktuella dimensionslistan är följande:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName (CodePackageName)

> [!NOTE]
> CodePackageName-dimensionen är inte tillgänglig för Linux-program. 

Varje dimension motsvarar olika komponenter i [serviceprogramprogrammet](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>AZURE-övervakare CLI

En fullständig lista över kommandon finns i [Azure Monitor CLI-dokument,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) men vi har inkluderat några användbara exempel nedan 

I varje exempel följer resurs-ID det här mönstret

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-användning av behållarna i ett program

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Minnesanvändning för varje tjänstreplik
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Startar om för varje behållare i ett 1-timmarsfönster 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Genomsnittlig CPU-användning för tjänster som heter "VotingWeb" i ett 1-timmarsfönster
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Statistikutforskare

Metrics Explorer är ett blad i portalen där du kan visualisera alla mått för ditt Mesh-program. Det här bladet är tillgängligt på programmets sida i portalen och Azure-övervakarbladet, varav det senare kan du använda för att visa mått för alla dina Azure-resurser som stöder Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Nästa steg
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Om du vill veta mer om azure monitor-måttkommandona kan du läsa [Azure Monitor CLI-dokumenten](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
