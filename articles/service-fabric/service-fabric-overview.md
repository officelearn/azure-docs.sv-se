---
title: "Översikt över Service Fabric i Azure | Microsoft Docs"
description: "En översikt över Service Fabric där program sammanställs av flera mikrotjänster för att ge skalning och återhämtning. Service Fabric är en plattform med distribuerade system som används för att skapa skalbara, tillförlitliga och lätthanterade program för molnet."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: aab15e6981e4f5f3c69ea6a85995fd2db69ff8b8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="overview-of-azure-service-fabric"></a>Översikt över Azure Service Fabric
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och behållare. Service Fabric tar också itu med betydande utmaningar vid utveckling och hantering av inbyggda molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara. Service Fabric representerar nästa generations plattform för att skapa och hantera dessa molskalningsprogram i företagsklass på nivå 1 som körs i behållare.

Denna korta video presenterar Service Fabric och mikrotjänster: <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Program som består av mikrotjänster 
Med Service Fabric kan du skapa och hantera skalbara och tillförlitliga program som består av mikrotjänster och som körs med hög densitet på en delad pool med datorer, som kallas ett kluster. Det ger en avancerad och lättviktig körning för att skapa distribuerade, skalbara, tillståndslösa och tillståndskänsliga mikrotjänster som körs i behållare. Det ger även omfattande funktioner för programhantering för att etablera, distribuera, övervaka, uppgradera/korrigera och ta bort distribuerade program, inklusive tjänster i behållare.

Service Fabric används idag för många Microsoft-tjänster, inklusive Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype for Business och många fler Azure-tjänster.

Service Fabric har skräddarsytts för att skapa inbyggda molntjänster som kan vara små inledningsvis och sedan växa efter behov till enorm skala med hundratals eller tusentals datorer.

Dagens tjänster med Internetskala består av mikrotjänster. Exempel på mikrotjänster är protokollgatewayar, användarprofiler, kundvagnar, lagerbearbetning, köer och cacheminnen. Service Fabric är en mikrotjänstplattform som ger alla mikrotjänster (eller behållare) ett unikt namn som kan vara antingen tillståndslöst eller tillståndskänsligt.

Service Fabric ger omfattande funktioner för körning och livscykelhantering i program som består av dessa mikrotjänster. Det fungerar som värd för mikrotjänster i behållare som distribueras och aktiveras i Service Fabric-klustret. Genom att flytta från virtuella datorer till behållare blir det möjligt att få en densitetsökning i storleksordning. På samma sätt blir en annan densitet i storleksordning möjlig när du övergår från behållare till mikrotjänster i dessa behållare. Ett enda kluster för Azure SQL Database kan till exempel bestå av hundratals datorer som kör tiotusentals behållare som är värdar för hundratusentals databaser. Varje databas är en tillståndskänslig Service Fabric-mikrotjänst. 

Mer information om att använda mikrotjänster finns i [Why a microservices approach to building applications?](service-fabric-overview-microservices.md) (Varför använda mikrotjänster för att bygga program?).

## <a name="container-deployment-and-orchestration"></a>Distribution och orkestrering i behållare
Service Fabric är Microsofts [behållarinitierare](service-fabric-cluster-resource-manager-introduction.md) som distribuerar mikrotjänster i ett kluster med datorer. Mikrotjänster kan utvecklas på många sätt, t.ex. genom att använda [programmeringsmodeller för Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) eller distribuera [valfri kod](service-fabric-deploy-existing-app.md). Det viktiga är att du kan blanda både tjänster i processer och tjänster i behållare i samma program. Om du bara vill [distribuera och hantera behållare](service-fabric-containers-overview.md) är Service Fabric ett utmärkt val som behållarinitierare.

## <a name="any-os-any-cloud"></a>Alla OS, alla moln
Service Fabric kan köras överallt. Du kan skapa kluster för Service Fabric i många miljöer, inklusive Azure eller lokalt, på Windows Server eller på Linux. Du kan till och med skapa kluster i andra offentliga moln. Dessutom är utvecklingsmiljön i SDK **identisk** med produktionsmiljön, helt utan emulatorer. Det innebär med andra ord att det som körs i det lokala distributionsklustret distribueras till klustren i andra miljöer.

![Service Fabric-plattform][Image1]

För Windows-utveckling är Service Fabric .NET SDK integrerad med Visual Studio och Powershell. Se [Förbereda utvecklingsmiljön i Windows](service-fabric-get-started.md). För Linux-utveckling är Service Fabric Java SDK integrerad med Eclipse, och Yeoman används för att generera mallar för Java, .NET Core och behållarprogram. Se [Förbereda utvecklingsmiljön i Linux](service-fabric-get-started.md)

Mer information om att skapa kluster finns i [skapa ett kluster i Windows Server eller Linux](service-fabric-deploy-anywhere.md) eller för Azure att skapa kluster [via Azure Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Med Service Fabric kan du skapa program som består av mikrotjänster eller behållare. Tillståndslösa mikrotjänster (till exempel protokollgatewayar och webbproxyservrar) har inte ett föränderligt tillstånd utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, kundvagnar och köer) har ett föränderligt och auktoritärt tillstånd bortom begäran och svaret. Dagens program med Internetskala består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En viktig skillnad med Service Fabric är dess stora fokus på att skapa tillståndskänsliga tjänster, antingen med de [inbyggda programmeringsmodellerna](service-fabric-choose-framework.md) eller med tillståndskänsliga tjänster i behållare. [Programscenarier](service-fabric-application-scenarios.md) beskriver scenarier där tillståndskänsliga tjänster används.


## <a name="application-lifecycle-management"></a>Application Lifecycle Management
Service Fabric har stöd för hela programlivscykeln och CI/CD för molnprogram, inklusive behållare. Den här livscykeln omfattar allt från utveckling och distribution, daglig hantering och underhåll till eventuell inaktivering.

Hanteringsfunktioner för programlivscykel i Service Fabric gör det möjligt för programadministratörer och IT-personal att använda enkla arbetsflöden med låg insats för att etablera, distribuera, korrigera och övervaka program. Dessa inbyggda arbetsflöden innebär en stor minskning av arbetsbelastningen för IT-personal när det gäller att se till att programmen ständigt är tillgängliga.

De flesta program består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster, behållare och andra körbara funktioner som distribueras tillsammans. Genom att ha starka typer i programmen gör Service Fabric det möjligt att distribuera flera programinstanser. Varje instans hanteras och uppgraderas separat. En viktig funktion är att Service Fabric kan distribuera behållare och eventuella körbara filer och göra dem tillförlitliga. Service Fabric kan till exempel distribuera .NET, ASP.NET Core, node.js, Windows-behållare, Linux-behållare, virtuella Java-datorer, skript, Angular – i princip allt som programmet består av.

Service Fabric är integrerat med CI/CD-verktyg som [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) och [Octopus Deploy](https://octopus.com/), och kan användas med valfritt annat populärt CI/CD-verktyg.

Mer information om hantering av programlivscykel finns i [Programlivscykel](service-fabric-application-lifecycle.md). Mer information om hur du distribuerar valfri kod finns i [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>De viktigaste funktionerna
Med hjälp av Service Fabric kan du:

* Distribuera till Azure eller lokala datacenter som kör Windows eller Linux helt utan kodändringar. Skriv en gång och distribuera sedan vart du vill i valfritt Service Fabric-kluster.
* Utveckla skalbara program som består av mikrotjänster genom att använda programmeringsmodeller, behållare eller valfri kod i Service Fabric.
* Utveckla mycket pålitliga tillståndslösa och tillståndskänsliga mikrotjänster. Förenkla utformningen av programmet med hjälp av tillståndskänsliga mikrotjänster. 
* Använd den nya programmeringsmodellen Reliable Actors för att skapa molnobjekt med självständig kod och tillstånd.
* Distribuera och samordna behållare som omfattar Windows- och Linux-behållare. Service Fabric är en datamedveten, tillståndskänslig behållarinitierare.
* Distribuera program på några sekunder med hög densitet och hundratals eller tusentals program eller behållare per dator.
* Distribuera olika versioner av samma program sida vid sida, och uppgradera programmen oberoende av varandra.
* Hantera livscykeln för programmen utan driftavbrott, inklusive avbrytande och ej avbrytande uppgraderingar.
* Skala ut eller skala in antalet noder i ett kluster. När du skalar noder skalas programmen automatiskt.
* Övervaka och diagnostisera hälsotillståndet för programmen och ange principer för att utföra automatiska reparationer.
* Titta på resursutjämnaren och samordna omfördelning av program i klustret. Service Fabric återställs från fel och optimerar fördelningen av belastningen baserat på tillgängliga resurser.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* Mer information:
  * [Varför använda mikrotjänster för att bygga program?](service-fabric-overview-microservices.md)
  * [Översikt över terminologi](service-fabric-technical-overview.md)
* Konfigurera [Windows-utvecklingsmiljön](service-fabric-get-started.md)  
* Konfigurera [Linux-utvecklingsmiljön](service-fabric-get-started-linux.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
