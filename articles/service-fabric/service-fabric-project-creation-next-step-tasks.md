---
title: Nästa steg för Service Fabric-projekt skapas | Microsoft Docs
description: Läs mer om application-projekt som du skapade i Visual Studio.  Lär dig att skapa tjänster med hjälp av självstudier och lära dig mer om hur du utvecklar Service Fabric-tjänster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: atsenthi
ms.openlocfilehash: e5371cd3ea9de1993f0f824325f6cbf1e25343d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773051"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Dina Service Fabric-program och nästa steg
Azure Service Fabric-programmet har skapats. Den här artikeln beskriver några självstudier för att testa, makeup av ditt projekt och lite mer information som du kanske vill potentiella nästa steg.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Kom igång med självstudier, genomgångar och exempel
Redo att sätta igång?  

Gå igenom självstudiekursen för .NET-program. Lär dig hur du [skapa en app](service-fabric-tutorial-create-dotnet-app.md) med en ASP.NET Core-klientdel och en tillståndskänslig backend-server, [distribuera programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md) till ett kluster, [konfigurera CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), och [konfigurera övervakning och diagnostik](service-fabric-tutorial-monitoring-aspnet.md).

Eller prova att använda något av följande genomgångar och skapa din första...
- [C#Reliable Services-tjänsten på Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Reliable Actors-tjänst på Windows](service-fabric-reliable-actors-get-started.md) 
- [Körbar gästtjänst på Windows](quickstart-guest-app.md) 
- [Windows-containerprogrammet](service-fabric-get-started-containers.md) 

Du kanske också är intresserad av att prova vår [programexempel](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Har frågor eller kommentarer?  Behovet av att rapportera ett problem?
Läs igenom [vanliga frågor](service-fabric-common-questions.md) och få svar på vad Service Fabric kan göra och hur den ska användas.

[Supportalternativ](service-fabric-support.md) visar en lista över forum på StackOverflow och MSDN för att ställa frågor samt alternativ för rapportering problem, få support och skicka feedback om produkten.

## <a name="the-application-project"></a>Programprojektet
Varje nytt program innehåller ett programprojekt. Det kan finnas en eller två ytterligare projekt, beroende på vilken typ av tjänst som är valt.

Programprojektet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publiceringsprofiler (1 nod lokal 5-nods lokal och moln) som du kan använda för att underhålla inställningar för att arbeta med olika miljöer, till exempel göra inställningar för en slutpunkt och om du vill utföra uppgraderingen distributioner som standard.
* Tre program parametern-filer (samma som ovan) som du kan använda för att underhålla miljöspecifika programkonfigurationer, till exempel hur många partitioner som ska skapas för en tjänst. Lär dig hur du [konfigurera ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).
* Ett distributionsskript som du kan använda för att distribuera ditt program från kommandoraden eller som en del av en automatiserad kontinuerlig integrering och distribution pipeline. Läs mer om [distribution av program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md).
* Applikationsmanifestet, som beskriver programmet. Du hittar manifestet under mappen ApplicationPackageRoot. Läs mer om [program och tjänstens manifest](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Läs mer om programmeringsmodeller
Service Fabric finns flera sätt att skriva och hantera dina tjänster.  Här är översikt och konceptuell information om [tillståndslösa och tillståndskänsliga Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [behållare](service-fabric-containers-overview.md), [körbara gäster ](service-fabric-guest-executables-introduction.md), och [tillståndslösa och tillståndskänsliga tjänster för ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Lär dig mer om tjänstkommunikation
Ett Service Fabric-program består av olika tjänster, där varje tjänst utför en särskild aktivitet. De här tjänsterna kan kommunicera med varandra och det kan finnas program utanför klustret som ansluter till och kommunicera med tjänster. Lär dig hur du [konfigurerar kommunikationen med och mellan dina tjänster](service-fabric-connect-and-communicate-with-services.md) i Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Läs om hur du konfigurerar programsäkerhet
Du kan skydda program som körs i klustret under olika användarkonton. Service Fabric kan du också skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Detta gör program som körs, även i en delad miljö, säkrare från varandra.  Lär dig hur du [ställer in säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

Ditt program kan innehålla känslig information, till exempel storage-anslutningssträngar, lösenord och andra värden som inte ska hanteras i oformaterad text. Lär dig hur du [hantera hemligheter i ditt program](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Lär dig mer om programmets livscykel
Som med andra plattformar, ett Service Fabric-program vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradera, underhållet och borttagningen. [Den här artikeln](service-fabric-application-lifecycle.md) innehåller en översikt över API: er och hur de används av de olika rollerna i hela faserna i livscykeln för Service Fabric-program.

## <a name="next-steps"></a>Nästa steg
- [Skapa en Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualisera ditt kluster, inklusive distribuerade program och fysiska layout med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Version och uppgradera dina tjänster](service-fabric-application-upgrade-tutorial.md)


