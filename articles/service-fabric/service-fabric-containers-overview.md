---
title: Översikt över Service Fabric och behållare | Microsoft Docs
description: En översikt över Service Fabric och användning av behållare för att distribuera mikrotjänster program. Den här artikeln innehåller en översikt över hur behållare kan användas och de tillgängliga funktionerna i Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/21/2018
ms.author: msfussell
ms.openlocfilehash: fe6db569c0f70362676251a9413fa859f27f5046
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641922"
---
# <a name="service-fabric-and-containers"></a>Service Fabric och behållare

## <a name="introduction"></a>Introduktion
Azure Service Fabric är en [orchestrator](service-fabric-cluster-resource-manager-introduction.md) av tjänster i ett kluster på datorer med års användnings- och optimering i massiv skala tjänster på Microsoft. Tjänster kan utvecklas på många sätt, från att använda den [Service Fabric programmeringsmodeller](service-fabric-choose-framework.md) till att distribuera [gäst körbara filer](service-fabric-guest-executables-introduction.md). Som standard Service Fabric distribuerar och aktiverar de här tjänsterna som processer. Processer ger snabbast aktivering och den högsta densitet användning av resurser i ett kluster. Service Fabric kan också distribuera tjänster i behållaren bilder. Allt kan du blanda tjänster i processer och tjänster i behållare i samma program.   

## <a name="what-are-containers"></a>Vad är behållare?
Behållare är inkapslade, individuellt distribuerbara komponenter som körs som testisolerade instanser på samma kerneln för att dra nytta av virtualisering som innehåller ett operativsystem. Därför körs varje program och dess runtime, beroenden och systemets bibliotek i en behållare med fullständig, privat åtkomst till vyn behållarens egna isolerade för konstruktionerna för operativsystemet. Den här grad av säkerhets- och isolering är den största fördelen för användning av behållare med Service Fabric som annars kör tjänster i processer tillsammans med mobilitet och.

Behållare är en virtualiseringsteknik som virtualiserar det underliggande operativsystemet från program. Behållare tillhandahålla en miljö som inte ändras för program att köras med olika typer av isolering. Behållare körs direkt ovanpå kerneln och har en isolerad vy av filsystemet och andra resurser. Behållare har jämfört med virtuella datorer följande fördelar:

* **Liten**: behållare använder ett enda lagringsutrymme och layer-versioner och uppdateringar för att öka effektiviteten.
* **Snabb**: behållare behöver inte starta en hela operativsystemet så att de kan börja mycket snabbare vanligtvis i sekunder.
* **Överföring av**: en avbildning av programmet kan överföras för att köras i molnet och lokalt i virtuella datorer eller direkt på fysiska datorer.
* **Resurs-styrning**: en behållare kan begränsa de fysiska resurser som den kan använda på värden.

## <a name="container-types-and-supported-environments"></a>Behållartyper av och miljöer som stöds
Service Fabric stöder behållare på Linux- och Windows och stöder även Hyper-V-isoleringsläge på denna. 

### <a name="docker-containers-on-linux"></a>Docker behållare på Linux
Docker ger avancerade API: er för att skapa och hantera behållare ovanpå Linux kernel-behållare. Docker-Hubbnamnrymd är en central databas för att lagra och hämta avbildningar för behållaren.
En självstudiekurs finns [distribuera en dockerbehållare till Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Windows Server-behållare
Windows Server 2016 innehåller två olika typer av behållare som skiljer sig i nivån av angivna isolering. Windows Server-behållare och Docker-behållare är lika eftersom båda har namnområdet och filen system isolering som delar kernel med värden som de körs på. På Linux för, denna isolering traditionellt har angetts av `cgroups` och `namespaces`, och Windows Server-behållare fungerar på samma sätt.

Windows-behållare med stöd för Hyper ger mer isolering och säkerhet, eftersom varje behållare inte delar operativsystemets kärna med andra behållare eller värden. Med den här högre, säkerhetsisolering riktas Hyper-V aktiverat behållare mot skadliga, flera scenarier.
En självstudiekurs finns [distribuera en Windows-behållare till Service Fabric](service-fabric-get-started-containers.md).

Följande bild visar de olika typerna av virtualisering och isolering som finns i operativsystemet.
![Service Fabric-plattformen][Image1]

## <a name="scenarios-for-using-containers"></a>Scenarier för användning av behållare
Här följer vanliga exempel där en behållare är ett bra alternativ:

* **IIS lyfta och flytta**: Om du har befintliga [ASP.NET MVC](https://www.asp.net/mvc) appar som du vill fortsätta att använda, placera dem i en behållare i stället för att migrera dem till ASP.NET Core. Apparna ASP.NET MVC beror på Internet Information Services (IIS). Du kan paketera programmen i behållaren bilder från precreated IIS-avbildning och distribuera dem med Service Fabric. Se [behållare bilder på Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server) information om Windows-behållare.
* **Blanda behållare och Service Fabric mikrotjänster**: Använd en befintlig behållare avbildning för en del av ditt program. Du kan till exempel använda den [NGINX-behållaren](https://hub.docker.com/_/nginx/) för webbklientservern för programmet och tillståndskänsliga tjänster för mer intensiva backend-beräkningen.
* **Minska påverkan av ”störningar grannar” services**: du kan använda resursen styrning möjligheten för behållare för att begränsa de resurser som en tjänst används på en värd. Om services kan använda många resurser och påverka prestanda för andra (till exempel en tidskrävande, fråga-liknande åtgärd) kan du placera dessa tjänster i behållare som har resurs styrning.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-stöd för behållare
Service Fabric stöder distribution av Docker-behållare i Linux och Windows Server-behållare på Windows Server 2016 tillsammans med stöd för Hyper-V-isoleringsläge. 

I Service Fabric [programmodell](service-fabric-application-model.md), en behållare representerar en programvärd i vilken flera tjänst repliker placeras. Service Fabric kan köra behållare och scenariot liknar den [gäst körbara scenariot](service-fabric-guest-executables-introduction.md), där du paketerar ett befintligt program i en behållare. Det här scenariot är vanligt användningsfall för behållare och exempel kör ett program som skrivits med valfritt språk och ramverk, men inte använder inbyggda programmeringsmodeller för Service Fabric.

Dessutom kan du köra [Service Fabric-tjänster i behållare](service-fabric-services-inside-containers.md) samt. Stöd för Service Fabric-tjänster som körs i behållare är för närvarande begränsad och att förbättras i kommande versioner.

Service Fabric har flera behållare funktioner som hjälper dig skapa program som består av mikrotjänster som är behållare. Service Fabric innehåller följande funktioner för container tjänster:

* Distribution av avbildningar för behållaren och aktivering.
* Resurs-styrning inklusive inställningsvärden resursen som standard i Azure-kluster.
* Autentisering för databasen.
* Behållaren port till portmappning för värden.
* Identifiering av behållare till en annan och kommunikation.
* Möjlighet att konfigurera och ange miljövariabler.
* Möjlighet att ange autentiseringsuppgifterna för behållaren.
* Välja mellan olika nätverk lägen för behållare.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om behållare, Service Fabric är en behållare för orchestrator, och den Service Fabric har funktioner som stöder behållare. Vi ska gå över exempel på var och en av funktionerna som visar hur du använder dem som ett nästa steg.

[Skapa din första Service Fabric-behållarprogram i Windows](service-fabric-get-started-containers.md)

[Skapa din första Service Fabric-behållarprogram på Linux](service-fabric-get-started-containers-linux.md)

[Mer information om Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
