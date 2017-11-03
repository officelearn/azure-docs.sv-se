---
title: "Avancerad användning av Reliable Services | Microsoft Docs"
description: "Mer information om avancerad användning av Service Fabric Reliable Services för ökad flexibilitet i dina tjänster."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: a87924faaf5c6c43716b06b6d70ab5100c61f097
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Avancerad användning av tjänsterna tillförlitliga programmeringsmodellen
Azure Service Fabric gör det enklare att skriva och hantera tillförlitliga tillståndslösa och tillståndskänsliga tjänster. Den här guiden handlar om avancerad användningar av Reliable Services för att få mer kontroll och flexibilitet över dina tjänster. Innan du läser den här guiden kan du bekanta dig med [Reliable Services programmeringsmodell](service-fabric-reliable-services-introduction.md).

Både tillståndskänsliga och tillståndslösa tjänster har två primära startpunkter för användarkod:

* `RunAsync(C#) / runAsync(Java)`är en generell startpunkt för koden för tjänsten.
* `CreateServiceReplicaListeners(C#)`och `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` är för att öppna kommunikationslyssnarna för klientbegäranden.

Dessa två startpunkter räcker för de flesta tjänster. I sällsynta fall när mer kontroll över livscykeln för en tjänst krävs är ytterligare Livscykelhändelser tillgängliga.

## <a name="stateless-service-instance-lifecycle"></a>Tillståndslösa tjänsten instans livscykel
Tillståndslösa tjänsten livscykel är mycket enkelt. Tillståndslösa tjänsten kan bara öppnas, stängts eller avbrutits. `RunAsync`i en tillståndslös tjänst körs när en instans av tjänsten öppnas och avbröts när en tjänstinstans stängts eller avbrutits.

Även om `RunAsync` bör vara tillräckligt nästan alla fall, öppna, Stäng och Avbryt händelser i en tillståndslös tjänst är också tillgängliga:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java`OnOpenAsync kallas när instansen för tillståndslösa tjänsten ska användas. Uppgifter för initiering av utökade tjänsten kan startas just nu.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java`OnCloseAsync anropas när instansen för tillståndslösa tjänsten kommer att smidigt att stänga av. Detta kan inträffa när tjänstens kod uppgraderas, tjänstinstansen flyttas på grund av belastningsutjämning eller ett tillfälligt fel har identifierats. OnCloseAsync kan användas för att på ett säkert sätt Stäng några resurser stoppa behandling i bakgrunden, Slutför sparar externa tillstånd eller stänga ned befintliga anslutningar.
* `void OnAbort() - C# / void onAbort() - Java`OnAbort anropas när instansen för tillståndslösa tjänsten tvång stängs. Detta kallas vanligtvis när ett permanent fel identifieras på noden, eller när Service Fabric inte kan på ett tillförlitligt sätt hantera livscykeln för instansen för tjänsten på grund av ett internt fel.

## <a name="stateful-service-replica-lifecycle"></a>Tillståndskänslig service replik livscykel

> [!NOTE]
> Tillståndskänsliga reliable services stöds inte i Java ännu.
>
>

En tillståndskänslig service replik livscykel är mycket mer komplexa än en instans av tillståndslösa tjänsten. Dessutom för att öppna, stänga och avbryta händelser, görs en tillståndskänslig service replik ändringar i rollen under dess livslängd. När en tillståndskänslig service replik ändras rollen, de `OnChangeRoleAsync` händelsen utlöses:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`OnChangeRoleAsync anropas när repliken tillståndskänslig service är rollen ändras, till exempel till primär eller sekundär. Primära repliker får skrivstatus (ska kunna skapa och skriva till tillförlitliga samlingar). Sekundära repliker ges Lässtatus (kan bara läsa från befintliga tillförlitliga samlingar). De flesta arbetsobjekt i en tillståndskänslig service utförs på den primära repliken. Sekundära repliker kan utföra skrivskyddade validering rapportgenerering, datautvinning eller andra skrivskyddade jobb.

I en tillståndskänslig service bara den primära repliken har skrivbehörighet till tillstånd och är därför vanligtvis när tjänsten utförs verkligt arbete. Den `RunAsync` metoden i en tillståndskänslig service körs endast när repliken tillståndskänslig service är primär. Den `RunAsync` metoden avbryts när en primär replik rollen ändras från primära samt under händelserna Stäng och ITransaction::Abort.

Med den `OnChangeRoleAsync` händelsen kan du utföra arbetet beroende på replikroll även som svar på rolländring.

En tillståndskänslig service innehåller också samma fyra livscykel händelser som en tillståndslös tjänst, med samma semantik och användningsområden:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Nästa steg
Mer avancerade ämnen som är relaterade till Service Fabric, finns i följande artiklar:

* [Konfigurera tillståndskänsliga Reliable Services](service-fabric-reliable-services-configuration.md)
* [Service Fabric hälsa introduktion](service-fabric-health-introduction.md)
* [Med hjälp av rapporter om hälsotillstånd för felsökning](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Konfigurera Services med Service Fabric klustret Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)
