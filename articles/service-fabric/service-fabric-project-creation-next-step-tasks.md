---
title: "Nästa steg för Service Fabric-projekt skapa | Microsoft Docs"
description: "Den här artikeln innehåller länkar till en uppsättning core development uppgifter för Service Fabric"
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
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: e04f9e57c65da42da73a5ee6a0b601dcbb318aaa
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric-programmet och nästa steg
Azure Service Fabric-programmet har skapats. Den här artikeln beskriver makeup för ditt projekt och vissa potentiella nästa steg.

## <a name="your-application"></a>Ditt program
Alla nya program innehåller ett projekt för programmet. Det kan finnas en eller två ytterligare projekt, beroende på vilken sorts tjänst som är valt.

### <a name="the-application-project"></a>Application-projekt
Projektet för konsolprogrammet består av:

* En uppsättning referenser till de tjänster som utgör ditt program.
* Tre publicera profiler (1-nod lokala 5-nod lokala och moln) som du kan använda för att underhålla inställningar för att arbeta med olika miljöer, till exempel göra inställningar för en kluster-slutpunkt och om du vill utföra uppgraderingen distributioner som standard.
* Tre program parametern filer (samma som ovan) som du kan använda för att underhålla miljö-specifika programkonfigurationer, till exempel antalet partitioner för att skapa för en tjänst.
* Ett skript för distribution som du kan använda för att distribuera ditt program från kommandoraden eller som en del av en automatisk kontinuerlig integrering och distribution pipeline.
* Applikationsmanifestet som beskriver programmet. Manifestet hittar du under mappen ApplicationPackageRoot.

### <a name="stateless-service"></a>Tillståndslösa tjänsten
När du lägger till en ny tjänst för tillståndslösa Visual Studio lägger till en service-projekt i lösningen som innehåller en typ som underordnade `StatelessService`. Tjänsten ökar en lokal variabel i en räknare.

### <a name="stateful-service"></a>Tillståndskänslig service
När du lägger till en ny tjänst för tillståndskänsliga Visual Studio lägger till en service-projekt i lösningen som innehåller en typ som underordnade `StatefulService`. Tjänsten ökar en räknare i dess `RunAsync` metod och lagrar resultatet i ett `ReliableDictionary`.

### <a name="actor-service"></a>Aktörstjänst
När du lägger till en ny tillförlitliga aktören Visual Studio lägger till två projekten i din lösning: en aktören projektet och ett gränssnitt.

Aktören projektet innehåller metoder för inställningen och på ett tillförlitligt sätt att hämta värdet på en räknare som sparas i den aktörstillstånd. Gränssnittet projektet tillhandahåller ett gränssnitt som andra tjänster kan använda för att anropa aktören.

### <a name="stateless-web-api"></a>Tillståndslösa webb-API
Tillståndslösa Web API-projektet innehåller en grundläggande webbtjänst som du kan använda för att öppna programmet till externa klienter. Mer information om hur projektet strukturerad, finns [Service Fabric Web API-tjänster med OWIN värd själv](service-fabric-reliable-services-communication-webapi.md).


### <a name="aspnet-core"></a>ASP.NET core
Service Fabric-SDK innehåller samma uppsättning ASP.NET Core mallar som är tillgängliga för fristående ASP.NET Core projekt: tom [Web API][aspnet-webapi], och [webbprogram] [aspnet-webapp].

### <a name="guest-executables-and-guest-containers"></a>Gästen körbara filer och Gäst-behållare

Ett Service Fabric ”gäst” är en tjänst som inte är inbyggd med plattformens programmeringsmodeller. Du kan även Paketera om binärfilerna för gäst antingen [direkt i programpaketet](service-fabric-deploy-existing-app.md) eller [via en behållare avbildning](service-fabric-deploy-container.md). I båda fallen Visual Studio skapar nödvändiga artefakter i den **ApplicationPackageRoot** mappen i projektet för konsolprogrammet. Visual Studio kommer inte att skapa ett nytt service-projekt eftersom koden redan finns någon annanstans. Om du vill hantera projektet gäst tillsammans med Service Fabric application-projekt kan du lägga till dem i samma Visual Studio-lösning.

## <a name="next-steps"></a>Nästa steg
### <a name="create-an-azure-cluster"></a>Skapa ett Azure-kluster
Service Fabric-SDK tillhandahåller ett lokala kluster för utveckling och testning. Om du vill skapa ett kluster i Azure, se [ställa in ett Service Fabric-kluster från Azure portal][create-cluster-in-portal].

### <a name="publish-your-application-to-azure"></a>Publicera programmet till Azure
Du kan publicera programmet direkt från Visual Studio till ett Azure-kluster. Mer information finns i avsnittet [publicering av programmet till Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Använda Service Fabric Explorer för att visualisera ditt kluster
Service Fabric Explorer erbjuder ett enkelt sätt att visualisera ditt kluster, inklusive distribuerade program och fysiska struktur. Läs mer i [visualisera ditt kluster med hjälp av Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Version och uppgradera dina tjänster
Service Fabric kan oberoende versionshantering och uppgradering av oberoende tjänster i ett program. Läs mer i [versionshantering och uppgradera dina tjänster][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Konfigurera kontinuerlig integrering med Visual Studio Team Services
Information om hur du ställer in en kontinuerlig integrationsprocess för Service Fabric-program finns [konfigurera kontinuerlig integrering med Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-manage-application-in-visual-studio.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
