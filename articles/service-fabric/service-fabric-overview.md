---
title: "Översikt över Service Fabric i Azure | Microsoft Docs"
description: "En översikt över Service Fabric där program består av många mikrotjänster att tillhandahålla skalning och återhämtning. Service Fabric är en plattform för distribuerade system används för att skapa skalbara, tillförlitliga och enkelt hanterade program för molnet."
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
ms.openlocfilehash: 8ff0d38a679b673b148dd808050eda82060cfe80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-service-fabric"></a>Översikt över Azure Service Fabric
Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och behållare. Service Fabric adresser även betydande utmaningarna med att utveckla och hantera interna molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara. Service Fabric representerar nästa generations plattform för att skapa och hantera dessa företagsklass, nivå 1, molnskala program som körs i behållare.

Den här korta videon beskriver Service Fabric och mikrotjänster:<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-overview/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="applications-composed-of-microservices"></a>Program består av mikrotjänster 
Service Fabric kan du skapa och hantera skalbara och tillförlitliga program består av mikrotjänster som körs med hög densitet på en delad pool av datorer, som kallas för ett kluster. Den innehåller en avancerade, lightweight runtime att skapa distribuerade, skalbar, tillståndslösa och tillståndskänsliga mikrotjänster som körs i behållare. Det ger också funktioner för hantering av omfattande program att etablera, distribuera, övervaka, uppgradering/korrigeringsfil och ta bort distribuerade program, bland annat av tjänster.

Service Fabric används många Microsoft-tjänster idag, inklusive Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype för företag och många viktiga Azure-tjänster.

Service Fabric är skräddarsydda för att skapa moln inbyggda tjänster som kan börja litet, vid behov och växa till massiv skala med hundratals eller tusentals datorer.

Dagens Internet-skala services bygger på mikrotjänster. Exempel på mikrotjänster är protokollet gateways, användarprofiler, shopping Datorvagnar, inventering, bearbetning, köer, och cachelagrar. Service Fabric är en plattform för mikrotjänster som ger varje mikrotjänster (eller behållaren) ett unikt namn som kan vara tillståndslösa och tillståndskänsliga.

Service Fabric ger omfattande runtime livscykel hanteringsfunktioner och program som består av dessa mikrotjänster. Den är värd för mikrotjänster i behållare som har distribuerats och aktiverats över Service Fabric-kluster. Flytta från virtuella datorer till behållare gör möjligt öka ordning i omfattning i densitet. En annan storleksordning i densitet på samma sätt blir möjligt när du flyttar från behållare till mikrotjänster i dessa behållare. Till exempel består ett enskilt kluster för Azure SQL Database av hundratals datorer med tiotusentals behållare som är värdar för totalt hundratals tusentals databaser. Varje databas är en tillståndskänslig Service Fabric-mikrotjänster. 

Mer information om metoden mikrotjänster läsa [varför en mikrotjänster metod för att bygga program?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Distribution i behållare och orchestration
Service Fabric är Microsofts [behållare orchestrator](service-fabric-cluster-resource-manager-introduction.md) distribuera mikrotjänster i ett kluster på datorer. Mikrotjänster kan utvecklas på många sätt från att använda den [Service Fabric programmeringsmodeller](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), distribuera [någon kod önskat](service-fabric-deploy-existing-app.md). Allt kan du blanda både tjänster i processer och tjänster i behållare i samma program. Om du bara vill [distribuera och hantera behållare](service-fabric-containers-overview.md), Service Fabric är det perfekta valet som en behållare för orchestrator.

## <a name="any-os-any-cloud"></a>Någon OS alla moln
Service Fabric körs överallt. Du kan skapa kluster för Service Fabric i många miljöer, inklusive Azure eller lokalt, på Windows Server eller på Linux. Du kan även skapa kluster på andra offentliga moln. Utvecklingsmiljö i SDK är dessutom **identiska** till produktionsmiljön med ingen emulatorerna ingår. Med andra ord distribuerar vad körs på klustret för lokal utveckling till kluster i andra miljöer.

![Service Fabric-plattformen][Image1]

Service Fabric .NET SDK är integrerad med Visual Studio och Powershell för Windows-utveckling. Se [förbereda din utvecklingsmiljö i Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md). Service Fabric Java SDK är integrerad med Eclipse för Linux-utveckling och Yeoman används för att skapa mallar för Java, .NET Core och behållarprogram. Se [förbereda din utvecklingsmiljö på Linux](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started.md)

Mer information om hur du skapar kluster [skapar ett kluster på Windows Server- eller Linux](service-fabric-deploy-anywhere.md) eller för att skapa ett kluster Azure [via Azure portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Tillståndslösa och tillståndskänsliga mikrotjänster för Service Fabric
Service Fabric kan du skapa program som består av mikrotjänster eller behållare. Tillståndslösa mikrotjänster (till exempel protokollet gateways och webbproxyservrar) har inte tillståndet föränderliga utanför en begäran och svaret från tjänsten. Arbetsroller i Azure Cloud Services är ett exempel på en tillståndslös tjänst. Tillståndskänsliga mikrotjänster (till exempel användarkonton, databaser, enheter, shoppingvagnar och köer) Underhåll tillståndet föränderliga, auktoritär utöver begäran och sitt svar. Dagens Internet-skala program består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster. 

En nyckel differentiering med Service Fabric är starkt fokus på bygga tillståndskänsliga tjänster, antingen med den [inbyggda programmeringsmodeller ](service-fabric-choose-framework.md) eller med container tillståndskänsliga tjänster. Den [Programscenarier](service-fabric-application-scenarios.md) beskrivs scenarier där tillståndskänsliga tjänster används.


## <a name="application-lifecycle-management"></a>Livscykel för programhantering
Service Fabric stöder CI/CD-skivan för molnprogram, inklusive behållare och fullständiga programmet livscykel. Den här livscykeln innehåller utveckling till distribution, dagliga hantering och underhåll för inaktivering av eventuell.

Funktioner för hantering av Service Fabric application livscykel administratörer och IT-ansvariga att använda enkla, smidig arbetsflöden för att etablera, distribuera, uppdatera och övervaka program. De här inbyggda arbetsflödena minska avsevärt belastningen på IT-ansvariga för att hålla program kontinuerligt tillgänglig.

De flesta program består av en kombination av tillståndslösa och tillståndskänsliga mikrotjänster, behållare och andra körbara filer som har distribuerats tillsammans. Genom att ha starkt typer på program, kan Service Fabric du distribuera flera instanser av programmet. Varje instans hanteras och uppgradera separat. Allt kan Service Fabric distribuera behållare eller alla körbara filer och gör dem tillförlitliga. Service Fabric kan till exempel distribuera .NET, ASP.NET Core, node.js, Windows-behållare, Linux behållare, Java virtuella datorer, skript, Angular eller bokstavligt något som utgör ditt program.

Service Fabric är integrerat med CI/CD-verktyg som [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html), och [bläckfisk distribuera](https://octopus.com/) och kan användas med andra populära verktyg CI/CD.

Mer information om programhantering livscykel [programmet livscykel](service-fabric-application-lifecycle.md). Mer information om hur du distribuerar någon kod finns [distribuera en körbar fil gäst](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>De viktigaste funktionerna
Med hjälp av Service Fabric kan du:

* Distribuera till Azure eller lokalt datacenter som kör Windows eller Linux med noll kodändringar. Skriv en gång och sedan distribuera var som helst till Service Fabric-klustret.
* Utveckla skalbara program som består av mikrotjänster med hjälp av Service Fabric programming modeller, behållare eller någon kod.
* Utveckla mycket pålitlig tillståndslösa och tillståndskänsliga mikrotjänster. Förenkla designen av ditt program genom att använda tillståndskänsliga mikrotjänster. 
* Använd nya Reliable Actors programmeringsmiljö för att skapa molnobjekt med självständiga koden och tillstånd.
* Distribuera och samordna behållare som är behållare för Windows och Linux-behållare. Service Fabric är en data-medveten, tillståndsbaserad behållaren orchestrator.
* Distribuera program i sekunder som hög densitet med hundratals eller tusentals program eller behållare per dator.
* Distribuera olika versioner av samma program sida vid sida och uppgradera varje program oberoende av varandra.
* Hantera livscykeln för dina program utan driftavbrott, dela och hårt uppgraderingar.
* Skala ut eller skala antalet noder i ett kluster. När du skalar noder skalas dina program automatiskt.
* Övervaka och diagnostisera hälsotillståndet för dina program och ange principer för att utföra automatiska reparationer.
* Titta på resursutjämnaren samordna omfördelning av program i klustret. Service Fabric återställs från fel och optimerar distribution av belastningen baserat på tillgängliga resurser.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* Mer information:
  * [Varför en mikrotjänster-lösning för att bygga program?](service-fabric-overview-microservices.md)
  * [Terminologi: översikt](service-fabric-technical-overview.md)
* Konfigurera din [Windows-utvecklingsmiljö](service-fabric-get-started.md)  
* Konfigurera din [Linux utvecklingsmiljö](service-fabric-get-started-linux.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
