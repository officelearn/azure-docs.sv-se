---
title: Översikt över Service Fabric-programmeringsmodell
description: 'Service Fabric erbjuder två ramverk för att bygga tjänster: aktörsramverket och tjänsteramverket. De erbjuder distinkta kompromisser i enkelhet och kontroll.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 11e32c9d1290227e638a314ed8417b1bed906842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749527"
---
# <a name="service-fabric-programming-model-overview"></a>Översikt över Service Fabric-programmeringsmodell

Service Fabric erbjuder flera sätt att skriva och hantera dina tjänster. Tjänster kan välja att använda Api:erna för service fabric för att dra full nytta av plattformens funktioner och programramverk. Tjänster kan också vara alla kompilerade körbara program som är skrivna på valfritt språk eller kod som körs i en behållare som finns på ett Service Fabric-kluster.

## <a name="guest-executables"></a>Körbara gästfiler

En [körbar gäst](service-fabric-guest-executables-introduction.md) är en befintlig, godtycklig körbar (skriven på alla språk) som kan köras som en tjänst i ditt program. Körbara gästar inte Service Fabric SDK-API:erna direkt. Men de drar fortfarande nytta av funktioner som plattformen erbjuder, till exempel tjänstupptäcktbarhet, anpassad hälso- och belastningsrapportering genom att anropa REST-API:er som exponeras av Service Fabric. De har också fullständigt stöd för programmets livscykel.

Kom igång med körbara gästar genom att distribuera ditt första [körbara gästprogram](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Containrar

Som standard distribuerar och aktiverar Service Fabric tjänster som processer. Service Fabric kan också distribuera tjänster i [behållare](service-fabric-containers-overview.md). Service Fabric stöder distribution av Linux-behållare och Windows-behållare på Windows Server 2016 och senare. Behållaravbildningar kan hämtas från alla behållararkiv och distribueras till datorn. Du kan distribuera befintliga program som gäst körbara filer, Service Fabric tillståndslösa eller tillståndskänsliga Tillförlitliga tjänster eller tillförlitliga aktörer i behållare, och du kan blanda tjänster i processer och tjänster i behållare i samma program.

[Läs mer om att behålla dina tjänster i Windows eller Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services är ett lätt ramverk för att skriva tjänster som integreras med Service Fabric-plattformen och dra nytta av alla plattformsfunktioner. Reliable Services tillhandahåller en minimal uppsättning API:er som gör att Service Fabric-körningen kan hantera livscykeln för dina tjänster och som gör att dina tjänster kan interagera med körningen. Programramverket är minimalt, vilket ger dig full kontroll över design- och implementeringsalternativ och kan användas för att vara värd för alla andra programramverk, till exempel ASP.NET Core.

Reliable Services kan vara tillståndslösa, liknande de flesta tjänstplattformar, till exempel webbservrar, där varje instans av tjänsten skapas lika och tillstånd kvarstår i en extern lösning, till exempel Azure DB eller Azure Table Storage.

Exklusivt för Service Fabric, tillförlitliga tjänster kan också vara tillståndskänsliga, där tillståndet kvarstår direkt i själva tjänsten med hjälp av tillförlitliga samlingar. Tillstånd görs mycket tillgängligt via replikering och distribueras genom partitionering, alla hanteras automatiskt av Service Fabric.

[Läs mer om tillförlitliga tjänster](service-fabric-reliable-services-introduction.md) eller kom igång genom att skriva din första reliable [service](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core är ett ramverk med öppen källkod för att skapa moderna molnbaserade Internetanslutna program, till exempel webbappar, IoT-appar och mobila serverdelar. Service Fabric integreras med ASP.NET Core så att du kan skriva både tillståndslösa och tillståndskänsliga ASP.NET Core-program som utnyttjar Reliable Collections och Service Fabrics avancerade orkestreringsfunktioner.

[Läs mer om ASP.NET Core i Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) eller kom igång genom att skriva ditt första ASP.NET Core Service [Fabric-program](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Reliable Actor-ramverket bygger på tillförlitliga tjänster och är ett programramverk som implementerar mönstret för den [virtuella aktören,](https://research.microsoft.com/en-us/projects/orleans/) baserat på den [beräkningsaktörsmodellen](https://en.wikipedia.org/wiki/Actor_model). Reliable Actor-ramverket använder oberoende enheter för beräkning och tillstånd med entrådiga körning som kallas *aktörer*. Reliable Actor-ramverket tillhandahåller inbyggd kommunikation för aktörer och förinställda tillståndsbeständighet och utskalningskonfigurationer.

Eftersom Reliable Actors är ett programramverk som bygger på reliable services är det helt integrerat med Service Fabric-plattformen och drar nytta av alla funktioner som plattformen erbjuder.

[Läs mer om reliable actors](service-fabric-reliable-actors-introduction.md) eller kom igång genom att skriva din första tjänst för pålitlig [skådespelare](service-fabric-reliable-actors-get-started.md)

[Skapa en klientdelstjänst med ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Nästa steg

[Service Fabric och behållare översikt](service-fabric-containers-overview.md)

[Översikt över Reliable Services](service-fabric-reliable-services-introduction.md)

[Översikt över Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service fabric och ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
