---
title: Översikt över Service Fabric och behållare | Microsoft Docs
description: En översikt över Service Fabric och användningen av behållare för att distribuera mikrotjänstprogram. Den här artikeln innehåller en översikt över hur du kan använda behållare och de tillgängliga funktionerna i Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: aljo, msfussell
ms.openlocfilehash: 5344f34e0e35d4d47b032b660726a4d70a4f1987
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807022"
---
# <a name="service-fabric-and-containers"></a>Service Fabric och behållare

## <a name="introduction"></a>Introduktion

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och containrar.

Service Fabric är Microsofts [behållarinitierare](service-fabric-cluster-resource-manager-introduction.md) för att distribuera mikrotjänster i ett kluster med datorer. Service Fabric-förmåner på erfarenheter under dess år kör tjänsterna på Microsoft i massiv skala.

Mikrotjänster kan utvecklas på många sätt, t.ex. genom att använda [programmeringsmodeller för Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) eller distribuera [valfri kod](service-fabric-guest-executables-introduction.md). Eller, om du bara vill [distribuera och hantera behållare](service-fabric-containers-overview.md), Service Fabric är också ett bra alternativ.

Som standard, Service Fabric distribuerar och aktiverar de här tjänsterna som processer. Processer ger snabbast aktivering och högsta densitet användning av resurser i ett kluster. Service Fabric kan också distribuera tjänster i behållaravbildningar. Du kan även blanda tjänster i processer och tjänster i behållare i samma program.

Prova Snabbstart, självstudier och exempel för att sätta och prova att använda behållare i Service Fabric:  

[Snabbstart: Distribuera ett program för Linux-behållare till Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Snabbstart: Distribuera ett program för Windows-behållare till Service Fabric](service-fabric-quickstart-containers.md)  
[Använd en befintlig .NET-app i behållare](service-fabric-host-app-in-a-container.md)  
[Exempel på container för Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Vad är behållare

Behållare problemet program som körs på ett tillförlitligt sätt i olika datormiljöer genom att tillhandahålla en miljö som inte kan ändras för att programmet ska köras. Behållare omsluta ett program och alla dess beroenden, till exempel bibliotek och konfigurationsfiler, i sin egen isolerad ”ruta” som innehåller allt som behövs för att köra programvara i behållaren. Oavsett var att behållaren körs har programmet i den alltid allt som behövs, till exempel rätt versioner av dess beroende bibliotek, alla konfigurationsfiler och allt annat det måste köras.

Behållare körs direkt ovanpå kernel och har en isolerad vy av filsystemet och andra resurser. Ett program i en behållare har ingen information om andra program eller processer utanför dess behållare. Varje program och dess runtime, beroenden och system bibliotek kör du i en behållare med fullständig, privat åtkomst till vyn behållarens egna isolerade av operativsystemet. Förutom att göra det enkelt att ge alla ditt programs beroenden som måste köras i olika datormiljöer, säkerhet och resursisolering är viktiga fördelar med att använda behållare med Service Fabric – som annars körs services i en processen.

Jämfört med virtuella datorer, har behållare följande fördelar:

* **Små**: Behållare använder ett enda lagringsutrymme och layer-versioner och uppdateringar för att öka effektiviteten.
* **Snabb**: Behållare har inte att starta ett helt operativsystem, så att de kan börja mycket snabbare – vanligtvis på några sekunder.
* **Portabilitet**: En avbildning av program i behållare kan överföras för att köras i molnet, lokalt, i virtuella datorer eller direkt på fysiska datorer.
* **Resursstyrning**: En behållare kan begränsa de fysiska resurser som den kan använda på dess värd.

### <a name="container-types-and-supported-environments"></a>Behållartyper av och miljöer som stöds

Service Fabric har stöd för behållare på både Linux och Windows och har stöd för Hyper-V-isoleringsläget på Windows.

#### <a name="docker-containers-on-linux"></a>Docker-behållare på Linux

Docker innehåller API: er för att skapa och hantera behållare på Linux-kernel-behållare. Docker Hub ger en central databas för att lagra och hämta behållaravbildningarna.
Se en självstudie som Linux-baserade [skapa din första Service Fabric-behållarapp i Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server-containrar

Windows Server 2016 innehåller två olika typer av behållare som skiljer sig av nivå av isolering. Windows Server-behållare och Docker-behållare liknar eftersom har båda namnområde och filen system isolering, samtidigt som de delar kernel med värden som de körs på. Denna isolering traditionellt har angetts av cgroups och namnområden på Linux och Windows Server-behållare fungerar på liknande sätt.

Windows-behållare med Hyper-V-stöd ger mer isolering och säkerhet eftersom någon behållare delar operativsystemets kernel andra behållare eller med värden. Med den här högre nivån säkerhetsisolering riktas aktiverat Hyper-V-behållare mot potentiellt fientlig scenarion med flera innehavare.
En självstudiekurs om Windows-baserade finns i [skapa ditt första Service Fabric-behållarprogram på Windows](service-fabric-get-started-containers.md).

Följande bild visar de olika typerna av virtualisering och isolering nivåer som är tillgängliga.
![Service Fabric-plattformen][Image1]

## <a name="scenarios-for-using-containers"></a>Scenarier för användning av behållare

Här följer exempel där en behållare är ett bra alternativ:

* **IIS lyfta och flytta**: Du kan placera en befintlig [ASP.NET MVC](https://www.asp.net/mvc) app i en behållare i stället för att migrera den till ASP.NET Core. Apparna ASP.NET MVC beror på Internet Information Services (IIS). Du kan paketera dessa program i behållaravbildningar från införande IIS-avbildningen och distribuera dem med Service Fabric. Se [Behållaravbildningar på Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) information om Windows-behållare.

* **Blanda behållare och mikrotjänster för Service Fabric**: Använd en befintlig behållaravbildning för en del av ditt program. Du kan till exempel använda den [NGINX-behållaren](https://hub.docker.com/_/nginx/) för klientdelen av ditt program och tillståndskänsliga tjänster för intensivare backend-beräkningen.

* **Minska effekten av ”bort störande grannar” services**: Du kan använda möjligheten för resurs-styrning av behållare för att begränsa de resurser som en tjänst används på en värd. Om services kan använda många resurser och påverka prestanda för andra (till exempel en tidskrävande, fråga-liknande åtgärd) kan du överväga att placera dessa tjänster i behållare som har resursstyrning.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-stöd för behållare

Service Fabric stöder distribution av Docker-behållare på Linux och Windows Server-behållare på Windows Server 2016, tillsammans med stöd för Hyper-V-isoleringsläget. 

Service Fabric tillhandahåller en [programmodell](service-fabric-application-model.md) i som en behållare som representerar en programvärd i vilken flera tjänst repliker placeras. Service Fabric stöder också en [gäst körbara scenariot](service-fabric-guest-executables-introduction.md) i som du inte använder den inbyggda Service Fabrics programmeringsmodeller men i stället paketera ett befintligt program som skrivits med valfritt språk eller ramverk, inuti en behållaren. Det här scenariot är vanligt användningsfall för behållare.

Du kan också köra [Service Fabric-tjänster i en behållare](service-fabric-services-inside-containers.md). Stöd för Service Fabric-tjänster som körs i behållare är för närvarande begränsad.

Service Fabric tillhandahåller flera behållare-funktioner som hjälper dig att bygga program som består av behållaranpassade mikrotjänster som:

* Distribution av behållare och aktivering.
* Resursstyrning inklusive inställningsvärden resursen som standard i Azure-kluster.
* Databasautentisering.
* Behållaren port till värd portmappning.
* Identifiering av behållare till en annan och kommunikation.
* Möjlighet att konfigurera och ange miljövariabler.
* Möjlighet att ange dina inloggningsuppgifter för behållaren.
* Ett urval av olika nätverkslägen för behållare.

En heltäckande översikt över behållare support på Azure, till exempel hur du skapar ett Kubernetes-kluster med Azure Kubernetes Service, hur du skapar ett privat Docker-register i Azure Container Registry och mycket mer, se [Azure for Containers](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om support Service Fabric tillhandahåller för att köra behållare. Nu ska gå vi över exempel på var och en av funktionerna som visar hur du använder dem.

[Skapa din första Service Fabric-behållarapp i Linux](service-fabric-get-started-containers-linux.md)  
[Skapa ditt första Service Fabric-behållarprogram på Windows](service-fabric-get-started-containers.md)  
[Mer information om Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png