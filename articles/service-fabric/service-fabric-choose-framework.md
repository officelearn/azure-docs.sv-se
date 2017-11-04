---
title: "Service Fabric programming modellen översikt | Microsoft Docs"
description: "Service Fabric erbjuder två ramverk för att skapa tjänster: aktören framework och ramverk för tjänster. De erbjuder distinkta avvägningarna i enkelhet och kontroll."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.openlocfilehash: 98e75e11b54aea677b8ca1b6f303a48190c6fce8
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric programming översikt över säkerhetsmodell
Service Fabric finns flera sätt att skriva och hantera dina tjänster. Tjänster kan du använda Service Fabric-API: er för att dra full nytta av funktioner och ramverk för programmet för plattformen. Tjänster kan också vara alla kompilerade körbara program som skrivits i valfritt språk eller en kod som körs i en behållare finns bara på ett Service Fabric-kluster.

## <a name="guest-executables"></a>Gästen körbara filer
En [gäst körbara](service-fabric-deploy-existing-app.md) är en befintlig godtycklig körbar fil (skrivs på olika språk) som kan köras som en tjänst i ditt program. Gästen körbara filer anropar inte API: er för Service Fabric SDK direkt. Men de fortfarande dra nytta av funktioner som plattformen erbjuder, till exempel tjänsten synlighet, anpassade hälsa och läsa in rapportering genom att anropa REST-API: er för Service Fabric. De kan också ha fullständig programmets livscykeln för support.

Kom igång med gäst körbara filer genom att distribuera din första [gäst körbara programmet](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Behållare
Som standard Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Service Fabric stöder distribution av behållare för Linux och Windows-behållare på Windows Server 2016. Behållaren avbildningar kan hämtas från databasen för alla behållare och distribueras till datorn. Du kan distribuera befintliga program som gäst exectuables, Service Fabric tillståndslösa och tillståndskänsliga Reliable services eller Reliable Actors i behållare, och du kan blanda tjänster i processer och tjänster i behållare i samma program.

[Mer information om containerizing dina tjänster i Windows eller Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services är ett ramverk för inaktiverat alternativ för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av en fullständig uppsättning funktioner. Reliable Services ger en minimal uppsättning API: er som tillåter Service Fabric runtime att hantera livscykeln för dina tjänster och som gör att dina tjänster att interagera med körningsmiljön. Application framework är minimal, vilket ger dig fullständig kontroll över alternativ för design och implementering och kan användas som värd för alla andra programramverk, till exempel ASP.NET Core.

Reliable Services kan vara tillståndslös, liknar de flesta service-plattformar, till exempel webbservrar, där varje instans av tjänsten har skapats lika och tillstånd sparas i en extern lösning, till exempel Azure DB eller Azure Table Storage.

Reliable Services kan också vara tillståndsbaserad exklusivt för Service Fabric, där tillståndet sparas direkt i tjänsten sig själv med hjälp av tillförlitliga samlingar. Tillståndet gjort hög tillgänglighet via replikering och distribueras via partitionering, allt hanteras automatiskt av Service Fabric.

[Mer information om Reliable Services](service-fabric-reliable-services-introduction.md) eller Kom igång genom att [skriva din första tillförlitlig tjänst](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Tillförlitliga aktören framework är byggda på Reliable Services ett programramverk som implementerar virtuella aktören mönster, baserat på aktören designmönstret. Tillförlitliga aktören framework används oberoende enheter beräkning och tillstånd med Enkeltrådig körning kallas aktörer. Ramverket för tillförlitlig aktören innehåller inbyggd kommunikation för aktörer och förinställda tillstånd beständighet och skalbar konfigurationer.

Reliable Actors själva är ett programramverk som bygger på Reliable Services, är den helt integrerade med Service Fabric-plattformen och fördelarna med en fullständig uppsättning funktioner som erbjuds av plattformen.

[Mer information om Reliable Actors](service-fabric-reliable-actors-introduction.md) eller Kom igång genom att [skriva din första tillförlitliga aktören-tjänst](service-fabric-reliable-actors-get-started.md)

## <a name="aspnet-core"></a>ASP.NET Core
Service Fabric kan integreras med [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) för bygga webb- och API-tjänster som kan vara en del av ditt program. 

[Skapa en frontend-tjänst med ASP.NET Core](service-fabric-add-a-web-frontend.md)

## <a name="next-steps"></a>Nästa steg
[Översikt över Service Fabric och behållare](service-fabric-containers-overview.md)

[Översikt över Reliable Services](service-fabric-reliable-services-introduction.md)

[Översikt över tillförlitliga aktörer](service-fabric-reliable-actors-introduction.md)

[Service Fabric och ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)




