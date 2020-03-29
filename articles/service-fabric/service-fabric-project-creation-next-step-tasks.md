---
title: Skapa service fabric-projekt nästa steg
description: Läs mer om det programprojekt som du just skapade i Visual Studio.  Lär dig hur du skapar tjänster med hjälp av självstudier och lär dig mer om att utveckla tjänster för Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349403"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Ditt Service Fabric-program och nästa steg
Ditt Azure Service Fabric-program har skapats. I den här artikeln beskrivs några självstudier att prova, projektets sammansättning, lite mer information som du kan vara intresserad av och potentiella nästa steg.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Kom igång med självstudier, genomgångar och exempel
Redo att sätta igång?  

Arbeta igenom .NET-programhandledningen. Lär dig hur du [skapar en app](service-fabric-tutorial-create-dotnet-app.md) med en frontend-version av ASP.NET Core och en tillståndskänslig serverdel, [distribuerar programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md) till ett kluster, [konfigurerar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)och [konfigurerar övervakning och diagnostik](service-fabric-tutorial-monitoring-aspnet.md).

Eller prova en av följande genomgångar och skapa din första ...
- [C# Tjänsten Reliable Services i Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors-tjänsten i Windows](service-fabric-reliable-actors-get-started.md) 
- [Körbar tjänst för gäst i Windows](quickstart-guest-app.md) 
- [Windows-containerprogrammet](service-fabric-get-started-containers.md) 

Du kan också vara intresserad av att prova våra [exempel applikationer](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Har du några frågor eller feedback?  Behöver du rapportera ett problem?
Läs igenom [vanliga frågor](service-fabric-common-questions.md) och hitta svar på vad Service Fabric kan göra och hur den ska användas.

[Supportalternativ](service-fabric-support.md) listar forum på StackOverflow och MSDN för att ställa frågor samt alternativ för att rapportera problem, få support och skicka produktfeedback.

## <a name="the-application-project"></a>Ansökningsprojektet
Varje nytt program innehåller ett programprojekt. Det kan finnas ytterligare ett eller två projekt, beroende på vilken typ av tjänst som valts.

Ansökningsprojektet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publiceringsprofiler (1-Node Local, 5-Node Local och Cloud) som du kan använda för att underhålla inställningar för att arbeta med olika miljöer – till exempel inställningar som är relaterade till en klusterslutpunkt och om du vill utföra uppgraderingsdistributioner som standard.
* Tre programparameterfiler (samma som ovan) som du kan använda för att underhålla miljöspecifika programkonfigurationer, till exempel antalet partitioner som ska skapas för en tjänst. Lär dig hur du [konfigurerar programmet för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).
* Ett distributionsskript som du kan använda för att distribuera ditt program från kommandoraden eller som en del av en automatisk pipeline för kontinuerlig integrering och distribution. Läs mer om [hur du distribuerar program med PowerShell](service-fabric-deploy-remove-applications.md).
* Ansökan manifest, som beskriver ansökan. Du hittar manifestet under mappen ApplicationPackageRoot. Läs mer om [program- och tjänstmanifest](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Läs mer om programmeringsmodellerna
Service Fabric erbjuder flera sätt att skriva och hantera dina tjänster.  Här är översikt och begreppsmässig information om [tillståndslösa och tillståndskänsliga Reliable Services,](service-fabric-reliable-services-introduction.md) [Reliable Actors](service-fabric-reliable-actors-introduction.md), [behållare,](service-fabric-containers-overview.md) [gästblansabler](service-fabric-guest-executables-introduction.md)och [tillståndslösa och tillståndskänsliga ASP.NET Core-tjänster](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Läs mer om servicekommunikation
Ett Service Fabric-program består av olika tjänster, där varje tjänst utför en specialiserad uppgift. Dessa tjänster kan kommunicera med varandra och det kan finnas klientprogram utanför klustret som ansluter till och kommunicerar med tjänster. Läs om hur du [konfigurerar kommunikation med och mellan dina tjänster](service-fabric-connect-and-communicate-with-services.md) i Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Läs mer om att konfigurera programsäkerhet
Du kan skydda program som körs i klustret under olika användarkonton. Service Fabric hjälper också till att skydda de resurser som används av program vid tidpunkten för distributionen under användarkontona - till exempel filer, kataloger och certifikat. Detta gör program som körs, även i en delad värdmiljö, säkrare från varandra.  Lär dig hur du [konfigurerar säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

Programmet kan innehålla känslig information, till exempel lagringsanslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. Läs om hur du [hanterar hemligheter i ditt program](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Läs mer om programmets livscykel
Precis som med andra plattformar går ett Service Fabric-program vanligtvis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. [Den här artikeln](service-fabric-application-lifecycle.md) innehåller en översikt över API:erna och hur de används av de olika rollerna under faserna i programlivscykeln för Service Fabric.

## <a name="next-steps"></a>Nästa steg
- [Skapa ett Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualisera klustret, inklusive distribuerade program och fysisk layout, med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Version och uppgradera dina tjänster](service-fabric-application-upgrade-tutorial.md)


