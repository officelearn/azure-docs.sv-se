---
title: "Nästa steg för Service Fabric-projekt skapa | Microsoft Docs"
description: "Mer information om programprojektet som du skapade i Visual Studio.  Lär dig hur du skapar tjänster med hjälp av självstudier och lära dig mer om hur du utvecklar Service Fabric-tjänster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 1e5b8523422aae00655b003bf15103d5467b4177
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric-programmet och nästa steg
Azure Service Fabric-programmet har skapats. Den här artikeln beskriver vissa självstudiekurser för att testa, makeup av projektet, mer information du kan vara intresserad och potentiella nästa steg.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Kom igång med självstudier, praktiska och exempel
Redo att sätta igång?  

Gå igenom kursen för .NET-program. Lär dig hur du [skapa en app](service-fabric-tutorial-create-dotnet-app.md) med en ASP.NET Core frontend och en tillståndskänslig serverdel [distribuera programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md) till ett kluster, [konfigurera CI/CD-](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), och [ställa in övervaknings- och diagnostikfunktionerna](service-fabric-tutorial-monitoring-aspnet.md).

Kan också prova att använda något av följande praktiska och skapa din första...
- [C# Reliable Services-tjänsten på Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors service i Windows](service-fabric-reliable-actors-get-started.md) 
- [Gästen körbara service i Windows](quickstart-guest-app.md) 
- [Windows-behållarprogrammet](service-fabric-get-started-containers.md) 

Du kanske även är intresserad provat vår [programexempel](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Har några frågor eller feedback?  Behöver du rapportera problem?
Läs igenom [vanliga frågor](service-fabric-common-questions.md) och få svar på vad Service Fabric kan göra och hur den ska användas.

[Supportalternativ](service-fabric-support.md) visar-forum på StackOverflow och MSDN för ställa frågor samt alternativ för rapportering problem, få support och skicka produktfeedback.

## <a name="the-application-project"></a>Application-projekt
Alla nya program innehåller ett projekt för programmet. Det kan finnas en eller två ytterligare projekt, beroende på vilken sorts tjänst som är valt.

Projektet för konsolprogrammet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publicera profiler (1-nod lokala 5-nod lokala och moln) som du kan använda för att underhålla inställningar för att arbeta med olika miljöer, till exempel göra inställningar för en kluster-slutpunkt och om du vill utföra uppgraderingen distributioner som standard.
* Tre program parametern filer (samma som ovan) som du kan använda för att underhålla miljö-specifika programkonfigurationer, till exempel antalet partitioner för att skapa för en tjänst. Lär dig hur du [konfigurera ditt program för miljöer med flera](service-fabric-manage-multiple-environment-app-configuration.md).
* Ett skript för distribution som du kan använda för att distribuera ditt program från kommandoraden eller som en del av en automatisk kontinuerlig integrering och distribution pipeline. Lär dig mer om [distribuera program med hjälp av PowerShell](service-fabric-deploy-remove-applications.md).
* Applikationsmanifestet som beskriver programmet. Manifestet hittar du under mappen ApplicationPackageRoot. Lär dig mer om [applikationen eller tjänsten manifesten](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Mer information om programmeringsmodeller
Service Fabric finns flera sätt att skriva och hantera dina tjänster.  Här är översikt och grundläggande information om [tillståndslösa och tillståndskänsliga Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [behållare](service-fabric-containers-overview.md), [gäst körbara filer ](service-fabric-guest-executables-introduction.md), och [tillståndslösa och tillståndskänsliga ASP.NET Core services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Lär dig mer om service-kommunikation
Ett Service Fabric-program består av olika tjänster, där varje tjänst utför en särskild aktivitet. Dessa tjänster kan kommunicera med varandra och det kan finnas klientprogram utanför klustret som kan ansluta till och kommunicera med tjänster. Lär dig hur du [ställa in kommunikationen med och mellan dina tjänster](service-fabric-connect-and-communicate-with-services.md) i Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Lär dig mer om hur du konfigurerar programsäkerhet
Du kan skydda program som körs i kluster under olika användarkonton. Service Fabric hjälper också till att skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Det gör att program som körs, även i en delad värdmiljö säkrare från varandra.  Lär dig hur du [konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md).

Programmet kan innehålla känslig information, till exempel storage-anslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. Lär dig hur du [hantera hemligheter i programmet](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Lär dig mer om programmet livscykel
Som med andra plattformar, ett Service Fabric-program vanligtvis går igenom följande faser: design, utveckling, testning, distribution, uppgradering, underhåll och borttagning. [Den här artikeln](service-fabric-application-lifecycle.md) ger en översikt över de API: er och hur de används av olika roller i faserna i livscykeln för Service Fabric-programmet.

## <a name="next-steps"></a>Nästa steg
- [Skapa ett Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualisera ditt kluster, inklusive distribuerade program och fysiska struktur med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Version och uppgradera dina tjänster](service-fabric-application-upgrade-tutorial.md)


