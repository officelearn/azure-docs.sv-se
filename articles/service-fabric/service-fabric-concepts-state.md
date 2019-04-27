---
title: Hantera tillstånd i Azure Service Fabric-tjänster | Microsoft Docs
description: Lär dig mer om att definiera och hantera tjänstestatus i Service Fabric-tjänster.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870901"
---
# <a name="service-state"></a>Tjänsttillstånd
**Tjänsten tillstånd** refererar till det i minnet eller på disk som en tjänst kräver att fungera. Det omfattar till exempel datastrukturer och medlemsvariabler som tjänsten läser och skriver till fungerar. Beroende på hur tjänsten har byggts, kan den även innehålla filer eller andra resurser som är lagrade på disken. Till exempel filerna en databas använder att lagra data och transaktionsloggar.

Nu ska vi titta en kalkylator som en exempel-tjänst. En enkel kalkylator-tjänst tar två tal och returnerar deras summa. Utför den här beräkningen omfattar inga Medlemsvariabler eller annan information.

Tänk dig samma Kalkylatorn nu, men med ytterligare en metod för att lagra och returnera den sista summan det har en beräknad. Den här tjänsten är nu tillståndskänsliga. Tillståndskänslig innebär att den innehåller vissa tillstånd som den skriver till när den beräknar en ny summa och läser från när du ber den att returnera den sista beräknade summan.

I Azure Service Fabric kallas den första tjänsten en tillståndslös tjänst. Den andra tjänsten kallas en tillståndskänslig tjänst.

## <a name="storing-service-state"></a>Lagra tjänsttillstånd
Tillstånd kan vara antingen externalized eller samordnad med kod som manipulera tillståndet. Externalization av tillståndet görs normalt med hjälp av en extern databas eller annat datalager som körs på olika datorer över nätverket eller processer på samma dator. I vårt exempel Kalkylatorn kan datalagringen vara en SQL-databas eller en instans av Azure Table Store. Varje begäran att beräkna summan utför en uppdatering på dessa data och förfrågningar till tjänsten att returnera värdet resultatet i det aktuella värdet som hämtas från store. 

Tillståndet kan också vara samordnad med kod som hanterar tillstånd. Tillståndskänsliga tjänster i Service Fabric skapas vanligtvis med den här modellen. Service Fabric tillhandahåller infrastruktur för att se till att det här tillståndet har hög tillgänglighet, konsekventa och hållbar och att tjänsterna bygger på så sätt kan enkelt skala.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Partitionera Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
