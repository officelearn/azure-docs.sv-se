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
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 79e5622dd73e53854204675b435e99d187a3ab26
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076635"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
Azure Service Fabric-nät är en fullständigt hanterad tjänst som ger utvecklare möjlighet att distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring och nätverk. Övervakning och diagnostik för Service Fabric-nät är indelade i tre typer av diagnostikdata:

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
> Du kan använda kommandot ”az nät servicereplica” för att få namnet på repliken. Namn är ökande nummer från 0.*

Här är hur detta ser ut för att visa loggar från VotingWeb.Code behållaren från röstningsprogrammet:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Nästa steg
Läs översikten om du vill veta mer om Service Fabric-nät kan:
- [Service Fabric-nät översikt](service-fabric-mesh-overview.md)
