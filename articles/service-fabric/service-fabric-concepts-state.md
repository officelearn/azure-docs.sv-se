---
title: "Definine och hantera tillstånd i Azure mikrotjänster | Microsoft Docs"
description: "Definiera och hantera i Service Fabric-tjänstens tillstånd"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 103fd6c3d536bc11f4e39444043a332a1d8f6c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-state"></a>Tillstånd för tjänsten
**Tjänsten tillstånd** refererar till det i minnet eller på disk som en tjänst kräver att fungera. Det innehåller till exempel datastrukturer och medlemsvariabler som tjänsten läser och skriver till fungerar. Beroende på hur tjänsten är konstruerad, kan den också innehålla filer eller andra resurser som är lagrade på disken. Till exempel filerna en-databas skulle använda att lagra data och transaktionsloggarna.

Nu ska vi titta Kalkylatorn som en exempel-tjänst. En enkel kalkylator tjänst tar två tal och returnerar sina summan. Utför den här beräkningen innebär att inga Medlemsvariabler eller annan information.

Tänk dig samma Kalkylatorn nu, men det har beräknats med ytterligare en metod för att lagra och returnera den sista summan. Den här tjänsten är nu tillståndskänsliga. Stateful innebär att den innehåller vissa tillstånd som skrivs till när den beräknar en ny summa och läser från när du begär att den returnerar senaste beräknade summan.

I Azure Service Fabric kallas den första tjänsten tillståndslösa tjänsten. Den andra tjänsten kallas en tillståndskänslig service.

## <a name="storing-service-state"></a>Spara tjänstens tillstånd
Tillstånd kan antingen externalized eller samordnad med kod som ändra tillståndet. Externalization i tillståndet normalt görs med hjälp av en extern databas eller ett datalager som körs på olika datorer via nätverket eller utanför processen på samma dator. I vårt exempel Kalkylatorn kan datalagret vara en SQL-databas eller en instans av Azure Table Store. Varje begäran att beräkna summan utför en uppdatering på dessa data och förfrågningar till tjänsten för att returnera värdet resultat i det aktuella värdet som hämtas från arkivet. 

Tillståndet kan också vara samordnad med kod som hanterar tillståndet. Tillståndskänsliga tjänster i Service Fabric skapas vanligtvis med hjälp av den här modellen. Service Fabric innehåller en infrastruktur för att säkerställa att det här tillståndet har hög tillgänglighet, konsekvent och varaktig och att tjänsterna skapat det här sättet kan enkelt skala.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

* [Tillgänglighet för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarheten för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Partitionering Service Fabric-tjänster](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
