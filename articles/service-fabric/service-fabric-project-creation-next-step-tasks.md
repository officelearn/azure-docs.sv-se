---
title: Kommande steg i skapande av Service Fabric-projekt
description: Lär dig mer om programprojektet som du nyss skapade i Visual Studio.  Lär dig hur du skapar tjänster med hjälp av självstudier och lär dig mer om att utveckla tjänster för Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349403"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Ditt Service Fabric program och nästa steg
Ditt Azure Service Fabric-program har skapats. I den här artikeln beskrivs några självstudier för att prova, makeup för ditt projekt, viss mer information som du kanske är intresse rad av och potentiella nästa steg.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Kom igång med självstudier, anvisningar och exempel
Redo att sätta igång?  

Arbeta i självstudien om .NET-program. Lär dig hur du [skapar en app](service-fabric-tutorial-create-dotnet-app.md) med en ASP.net Core frontend och en tillstånds känslig Server del, [distribuerar programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md) till ett kluster, [konfigurerar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)och konfigurerar [övervakning och diagnostik](service-fabric-tutorial-monitoring-aspnet.md).

Alternativt kan du prova någon av följande steg-för-steg-anvisningar och skapa din första...
- [C#Reliable Services tjänst i Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Reliable Actors tjänst i Windows](service-fabric-reliable-actors-get-started.md) 
- [Körbar gäst tjänst i Windows](quickstart-guest-app.md) 
- [Windows-containerprogrammet](service-fabric-get-started-containers.md) 

Du kanske också är intresse rad av att prova våra [exempel program](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Har du några frågor eller feedback?  Behöver du rapportera ett problem?
Läs igenom [vanliga frågor](service-fabric-common-questions.md) och få svar på vad Service Fabric kan göra och hur det ska användas.

[Support alternativ](service-fabric-support.md) listar forum på STACKOVERFLOW och MSDN för att ställa frågor, samt alternativ för rapportering av problem, få support och skicka feedback från produkter.

## <a name="the-application-project"></a>Programprojektet
Varje nytt program innehåller ett program projekt. Det kan finnas ett eller två ytterligare projekt, beroende på vilken typ av tjänst som valts.

Programprojektet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publicerings profiler (lokal, 5-nods lokal och moln) som du kan använda för att hantera inställningar för att arbeta med olika miljöer, till exempel inställningar som är relaterade till en kluster slut punkt och om du vill utföra uppgraderings distributioner som standard.
* Tre program parameter-filer (samma som ovan) som du kan använda för att underhålla programkonfigurationer som är specifika för miljön, till exempel antalet partitioner som ska skapas för en tjänst. Lär dig hur du [konfigurerar ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).
* Ett distributions skript som du kan använda för att distribuera programmet från kommando raden eller som en del av en automatiserad pipeline för kontinuerlig integrering och distribution. Lär dig mer om att [distribuera program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md).
* Applikations manifestet som beskriver programmet. Du hittar manifestet under mappen ApplicationPackageRoot Läs mer om [program-och tjänst manifest](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Lär dig mer om programmerings modeller
Service Fabric erbjuder flera olika sätt att skriva och hantera dina tjänster.  Här är en översikt och konceptuell information om [tillstånds lösa och tillstånds känsliga Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [behållare](service-fabric-containers-overview.md), [körbara gäst program](service-fabric-guest-executables-introduction.md)och [tillstånds lösa och tillstånds känsliga ASP.net Core tjänster](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Läs mer om tjänst kommunikation
Ett Service Fabric program består av olika tjänster, där varje tjänst utför en specialiserad uppgift. Dessa tjänster kan kommunicera med varandra och det kan finnas klient program utanför klustret som ansluter till och kommunicerar med tjänster. Lär dig hur du [konfigurerar kommunikation med och mellan dina tjänster](service-fabric-connect-and-communicate-with-services.md) i Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Läs om hur du konfigurerar program säkerhet
Du kan skydda program som körs i klustret under olika användar konton. Service Fabric skyddar också resurserna som används av program vid tidpunkten för distributionen under användar konton, till exempel filer, kataloger och certifikat. Detta gör att program körs, även i en delad värd miljö, säkrare från varandra.  Lär dig hur du [konfigurerar säkerhets principer för ditt program](service-fabric-application-runas-security.md).

Ditt program kan innehålla känslig information, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text. Lär dig hur du [hanterar hemligheter i ditt program](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Läs om programmets livs cykel
Precis som med andra plattformar går ett Service Fabric-program vanligt vis igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. [Den här artikeln](service-fabric-application-lifecycle.md) innehåller en översikt över API: erna och hur de används av de olika rollerna under faserna i Service Fabric programmets livs cykel.

## <a name="next-steps"></a>Nästa steg
- [Skapa ett Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualisera klustret, inklusive distribuerade program och fysisk layout, med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Version och uppgradera dina tjänster](service-fabric-application-upgrade-tutorial.md)


