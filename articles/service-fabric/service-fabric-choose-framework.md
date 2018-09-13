---
title: Service Fabric programming model översikt | Microsoft Docs
description: 'Service Fabric erbjuder två ramverk för att skapa tjänster: ramverket och tjänstramverket. De ger distinkta kompromisser i enkelhet och kontroll.'
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: f37e1ed2c7dd720e4a77076c8587fdf540b29ca5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713960"
---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric programming model-översikt
Service Fabric finns flera sätt att skriva och hantera dina tjänster. Tjänster kan du använda Service Fabric-API: er för att dra full nytta av plattformens funktioner och ramverk för programmet. Tjänster kan också vara alla kompilerade körbara program som skrivits i valfritt språk eller kod som körs i en behållare som ett Service Fabric-kluster som värd.

## <a name="guest-executables"></a>Körbara gästfiler
En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig skadlig körbar fil (skrivna på valfritt språk) som kan köras som en tjänst i ditt program. Körbara gäster anropa inte Service Fabric SDK-API: er direkt. Men de fortfarande dra nytta av funktioner som plattformen erbjuder, till exempel tjänsten synlighet, anpassade hälsotillstånd och läsa in rapportering genom att anropa REST API: er visas av Service Fabric. De har också fullständig programmets livscykel för support.

Kom igång med gästfiler genom att distribuera din första [körbart gästprogram](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containrar
Som standard, Service Fabric distribuerar och aktiverar tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016. Behållaravbildningar kan hämtas från en lagringsplats för behållare och distribueras till datorn. Du kan distribuera befintliga program som körbara gäster, Service Fabric tillståndslösa eller tillståndskänsliga Reliable services eller Reliable Actors i behållare och du kan blanda tjänster i processer och tjänster i behållare i samma program.

[Mer information om användning av dina tjänster i Windows eller Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services är en lätt ramverk för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av den fullständiga uppsättningen funktioner. Reliable Services tillhandahåller en minimal uppsättning API: er som gör det Service Fabric-körning för att hantera livscykeln för dina tjänster och som gör att dina tjänster för att interagera med körningsmiljön. Application framework är minimal, vilket ger dig fullständig kontroll över alternativ för design och implementering, och kan användas som värd för alla andra programramverket, till exempel ASP.NET Core.

Reliable Services kan vara tillståndslösa, ungefär som i de flesta service-plattformar, till exempel webbservrar, där varje instans av tjänsten är skapade på samma sätt och tillstånd sparas i en extern lösning, till exempel Azure DB eller Azure Table Storage.

Reliable Services kan också vara tillståndskänslig uteslutande till Service Fabric, där tillståndet bevaras direkt i tjänsten själva med hjälp av tillförlitliga samlingar. Tillstånd har gjorts med hög tillgänglighet via replikering och distribueras via partitionering, allt hanteras automatiskt av Service Fabric.

[Läs mer om Reliable Services](service-fabric-reliable-services-introduction.md) eller Kom igång genom att [skriva din första tillförlitlig tjänst](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core är en ny öppen källkod och plattformsoberoende ramverk för att skapa moderna molnbaserade Internet-anslutna program, till exempel webbappar, IoT-appar och mobila serverdelar. Service Fabric integreras med ASP.NET Core så att du kan skriva både tillståndslösa och tillståndskänsliga ASP.NET Core program som drar nytta av tillförlitliga samlingar och Service Fabric avancerade orchestration-funktioner.

[Läs mer om ASP.NET Core i Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) eller Kom igång genom att [skriva din första ASP.NET Core Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors
Skapad utifrån Reliable Services och är Reliable Actor-ramverket ett programramverk som implementerar mönstret virtuella aktören baserat på designmönstret aktör. Reliable Actor-framework använder oberoende enheter av beräknings- och tillstånd med Enkeltrådig körning kallas aktörer. Reliable Actor-ramverk tillhandahåller inbyggd kommunikation för aktörer och förinställda tillstånd persistence och skala ut konfigurationer.

Eftersom Reliable Actors är ett programramverk som bygger på Reliable Services, är det helt integrerat med Service Fabric-plattformen och fördelarna med en fullständig uppsättning funktioner som erbjuds av plattformen.

[Läs mer om Reliable Actors](service-fabric-reliable-actors-introduction.md) eller Kom igång genom att [skriva din första Reliable Actor-tjänst](service-fabric-reliable-actors-get-started.md)


[En frontend-tjänst med ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Nästa steg
[Översikt över Service Fabric och behållare](service-fabric-containers-overview.md)

[Översikt över Reliable Services](service-fabric-reliable-services-introduction.md)

[Översikt över Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric och ASP.NET Core ](service-fabric-reliable-services-communication-aspnetcore.md)




