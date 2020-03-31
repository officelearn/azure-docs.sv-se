---
title: Översikt över Service Fabric och behållare
description: En översikt över Service Fabric och användningen av behållare för att distribuera mikrotjänstprogram. Den här artikeln innehåller en översikt över hur behållare kan användas och de tillgängliga funktionerna i Service Fabric.
ms.topic: conceptual
ms.date: 8/8/2018
ms.openlocfilehash: 884cefa3d6a60f55269afac73c40b9f6b21518f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458219"
---
# <a name="service-fabric-and-containers"></a>Service tyg och behållare

## <a name="introduction"></a>Introduktion

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och containrar.

Service Fabric är Microsofts [container orchestrator](service-fabric-cluster-resource-manager-introduction.md) för distribution av mikrotjänster över ett kluster av datorer. Service Fabric drar nytta av de lärdomar som dragits under sina år som körs på Microsoft i stor skala.

Mikrotjänster kan utvecklas på många sätt, t.ex. genom att använda [programmeringsmodeller för Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) eller distribuera [valfri kod](service-fabric-guest-executables-introduction.md). Eller, om du bara vill [distribuera och hantera behållare,](service-fabric-containers-overview.md)är Service Fabric också ett utmärkt val.

Som standard distribuerar och aktiverar Service Fabric dessa tjänster som processer. Processer ger den snabbaste aktiveringen och användningen av resurser med högsta densitet i ett kluster. Service Fabric kan också distribuera tjänster i behållaravbildningar. Du kan också blanda tjänster i processer och tjänster i behållare i samma program.

Om du vill hoppa in och prova behållare på Service Fabric provar du en snabbstart, självstudiekurs eller ett exempel:  

[Snabbstart: Distribuera ett Linux-behållarprogram till Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Snabbstart: Distribuera ett Windows-behållarprogram till Service Fabric](service-fabric-quickstart-containers.md)  
[Använd en befintlig .NET-app i container](service-fabric-host-app-in-a-container.md)  
[Exempel på container för Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Vad är behållare

Behållare löser problemet med att köra program på ett tillförlitligt sätt i olika datormiljöer genom att tillhandahålla en oföränderlig miljö för programmet att köra i. Behållare radbrytar ett program och alla dess beroenden, till exempel bibliotek och konfigurationsfiler, i sin egen isolerade "låda" som innehåller allt som behövs för att köra programvaran inuti behållaren. Var helst behållaren körs, programmet inuti den har alltid allt den behöver för att köras såsom rätt versioner av dess beroende bibliotek, alla konfigurationsfiler, och allt annat den behöver för att köra.

Behållare körs direkt ovanpå kärnan och har en isolerad vy av filsystemet och andra resurser. Ett program i en behållare har ingen kunskap om andra program eller processer utanför behållaren. Varje program och dess körning, beroenden och systembibliotek körs i en behållare med fullständig, privat åtkomst till behållarens egen isolerade vy av operativsystemet. Förutom att göra det enkelt att tillhandahålla alla ditt programs beroenden som det behöver för att köras i olika datormiljöer, är säkerhet och resursisolering viktiga fördelar med att använda behållare med Service Fabric - som annars kör tjänster i en Process.

Jämfört med virtuella datorer har behållare följande fördelar:

* **Liten**: Behållare använder ett enda lagringsutrymme och lagerversioner och uppdateringar för att öka effektiviteten.
* **Snabb:** Behållare behöver inte starta ett helt operativsystem, så att de kan starta mycket snabbare - vanligtvis på några sekunder.
* **Portabilitet**: En behållarprogramavbildning kan portas för att köras i molnet, lokalt, inuti virtuella datorer eller direkt på fysiska datorer.
* **Resursstyrning**: En behållare kan begränsa de fysiska resurser som den kan använda på värden.

### <a name="container-types-and-supported-environments"></a>Behållartyper och miljöer som stöds

Service Fabric stöder behållare på både Linux och Windows och stöder Hyper-V-isoleringsläge i Windows.

#### <a name="docker-containers-on-linux"></a>Docker behållare på Linux

Docker tillhandahåller API:er för att skapa och hantera behållare ovanpå Linux-kärnbehållare. Docker Hub tillhandahåller en central lagringsplats för att lagra och hämta behållaravbildningar.
En Linux-baserad självstudiekurs finns i [Skapa ditt första Service Fabric-behållarprogram på Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server-containrar

Windows Server 2016 innehåller två olika typer av behållare som skiljer sig åt efter isoleringsnivå. Windows Server-behållare och Docker-behållare är likartade eftersom båda har namnområde och isolering av filsystem, samtidigt som kärnan delas med den värd de körs på. På Linux har den här isoleringen traditionellt tillhandahållits av cgroups och namespaces, och Windows Server-behållare fungerar på samma sätt.

Windows-behållare med Hyper-V-stöd ger mer isolering och säkerhet eftersom ingen behållare delar operativsystemkärnan med någon annan behållare eller med värden. Med den här högre säkerhetsisoleringen är Hyper-V-aktiverade behållare inriktade på potentiellt fientliga scenarier med flera innehavare.
En Windows-baserad självstudiekurs finns i [Skapa ditt första service fabric-behållarprogram i Windows](service-fabric-get-started-containers.md).

Följande bild visar de olika typerna av virtualiserings- och isoleringsnivåer som är tillgängliga.
![Service Fabric-plattform][Image1]

## <a name="scenarios-for-using-containers"></a>Scenarier för användning av behållare

Här är typiska exempel där en behållare är ett bra val:

* **IIS-lyft och skift:** Du kan placera en befintlig [ASP.NET MVC-app](https://www.asp.net/mvc) i en behållare i stället för att migrera den till ASP.NET Core. Dessa ASP.NET MVC-appar är beroende av Internet Information Services (IIS). Du kan paketera dessa program i behållaravbildningar från den förskapade IIS-avbildningen och distribuera dem med Service Fabric. Mer information om Windows-behållare [finns i Behållaravbildningar på Windows Server.](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)

* **Blanda behållare och service fabric-mikrotjänster:** Använd en befintlig behållaravbildning för en del av programmet. Du kan till exempel använda [NGINX-behållaren](https://hub.docker.com/_/nginx/) för webbfronten av ditt program och tillståndskänsliga tjänster för den mer intensiva backend-beräkningen.

* **Minska effekten av "bullriga grannar"-tjänster:** Du kan använda resursstyrningsmöjligheten för behållare för att begränsa de resurser som en tjänst använder på en värd. Om tjänster kan förbruka många resurser och påverka andras prestanda (till exempel en tidskrävande, frågeliknande åtgärd) kan du överväga att placera dessa tjänster i behållare som har resursstyrning.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-stöd för containrar

Service Fabric stöder distributionen av Docker-behållare på Linux och Windows Server-behållare på Windows Server 2016, tillsammans med stöd för Hyper-V-isoleringsläge. 

Service Fabric tillhandahåller en [programmodell](service-fabric-application-model.md) där en behållare representerar en programvärd där flera tjänstrepliker placeras. Service Fabric stöder också ett [körbart gästscenario](service-fabric-guest-executables-introduction.md) där du inte använder de inbyggda programmeringsmodellerna för Service Fabric utan paketerar ett befintligt program, skrivet med valfritt språk eller ramverk, i en behållare. Det här scenariot är det vanliga användningsfallet för behållare.

Du kan också köra [Service Fabric-tjänster i en behållare](service-fabric-services-inside-containers.md). Stöd för att köra Service Fabric-tjänster inuti behållare är för närvarande begränsat.

Service Fabric innehåller flera behållarfunktioner som hjälper dig att skapa program som består av behållarmikrotjänster, till exempel:

* Distribution och aktivering av behållaravbildning.
* Resursstyrning inklusive inställning av resursvärden som standard i Azure-kluster.
* Databasautentisering.
* Behållarport som värd för portmappning.
* Identifiering och kommunikation från behållare till behållare.
* Möjlighet att konfigurera och ställa in miljövariabler.
* Möjlighet att ange säkerhetsreferenser på behållaren.
* Ett urval av olika nätverkslägen för behållare.

En omfattande översikt över behållarsupport på Azure, till exempel hur du skapar ett Kubernetes-kluster med Azure Kubernetes-tjänsten, hur du skapar ett privat Docker-register i Azure Container Registry med mera finns i [Azure for Containers](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om den support Service Fabric som tillhandahåller för att köra behållare. Därefter kommer vi att gå igenom exempel på var och en av funktionerna för att visa dig hur du använder dem.

[Skapa din första Service Fabric-containerapp i Linux](service-fabric-get-started-containers-linux.md)  
[Skapa din första Service Fabric-containerapp i Windows](service-fabric-get-started-containers.md)  
[Läs mer om Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png