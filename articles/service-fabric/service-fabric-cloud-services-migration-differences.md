---
title: "Skillnader mellan molntjänster och Service Fabric | Microsoft Docs"
description: "En översikt för att migrera program från molntjänster till Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4bb5d92cd46533b46b388d178990f230424b09dc
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Lär dig mer om skillnaderna mellan molntjänster och Service Fabric innan du migrerar program.
Microsoft Azure Service Fabric är nästa generations molnet programmet plattform för mycket skalbar, tillförlitlig hög distribuerade program. Det har många nya funktioner för paketering, distribution, uppgradera och hantera distribuerade molnprogram. 

Detta är ett inledande guide för att migrera program från molntjänster till Service Fabric. Den fokuserar huvudsakligen på arkitektur och utforma skillnaderna mellan molntjänster och Service Fabric.

## <a name="applications-and-infrastructure"></a>Program och infrastruktur
En grundläggande skillnaden mellan Cloud Services och Service Fabric är förhållandet mellan virtuella datorer, arbetsbelastningar och program. En arbetsbelastning här definieras som koden du skriver så att utföra en viss uppgift eller tillhandahålla en tjänst.

* **Molntjänster är hur du distribuerar program som virtuella datorer.** Koden du skriver är direkt kopplade till en VM-instans, till exempel en webbplats eller en Worker-rollen. Om du vill distribuera en arbetsbelastning i Cloud Services är för att distribuera en eller flera VM-instanser som körs arbetsbelastningen. Det finns ingen åtskillnad mellan program och virtuella datorer och så det finns ingen formell definition av ett program. Ett program kan betraktas som en uppsättning webb- eller Arbetsrollen instanser i en distribution för molntjänster eller en hel Cloud Services-distribution. I det här exemplet visas ett program som en uppsättning rollinstanser.

![Tjänster för molnprogram och topologi][1]

* **Service Fabric är hur du distribuerar program till befintliga virtuella datorer eller datorer som kör Service Fabric på Windows- eller Linux.** Tjänster som du skriver är helt frikopplad från den underliggande infrastruktur som representeras direkt av Service Fabric-plattformen för programmet så att ett program kan distribueras till flera miljöer. En arbetsbelastning i Service Fabric kallas ”tjänst” och en eller flera tjänster grupperas i ett formellt definierade program som körs på plattformen Service Fabric-programmet. Flera program kan distribueras till ett enda Service Fabric-kluster.

![Service Fabric-program och -topologi][2]

Service Fabric själva är plattform programnivå som körs på Windows- eller Linux, Cloud Services är ett system för att distribuera Azure-hanterade virtuella datorer med arbetsbelastningar som är anslutna.
Service Fabric-programmodell har ett antal fördelar:

* Snabb distribution gånger. Skapa VM-instanser kan ta lång tid. I Service Fabric distribuerade virtuella datorer bara när för att bilda ett kluster som är värd för programmet Service Fabric-plattformen. Från den punkten på kan programpaket bara distribueras till klustret mycket snabbt.
* Hög densitet värd. I Cloud Services, en Worker-rollen virtuell dator är värd för en arbetsbelastning. I Service Fabric är program separat från de virtuella datorerna som kör dem, vilket innebär att du kan distribuera ett stort antal program till ett litet antal virtuella datorer som kan sänka övergripande kostnader för större distributioner.
* Den Service Fabric plattform kan köras var som helst som har Windows Server- eller Linux-datorer, oavsett om Azure eller lokalt. Plattformen tillhandahåller ett Abstraktionslager över underliggande infrastruktur så att programmet kan köras på olika miljöer. 
* Hantering av distribuerade program. Service Fabric är en plattform som värd för distribuerade program, men även hjälper dig att hantera livscykeln oberoende av värd VM eller datorn livscykel.

## <a name="application-architecture"></a>Programarkitektur
Arkitekturen för en Cloud Services-program innehåller vanligtvis ett stort antal externa tjänstberoenden som Service Bus, Azure Table och Blob Storage, SQL, Redis och andra att hantera tillstånd och data för ett program och kommunikation mellan webb och Arbetsroller i en distribution med molntjänster. Ett exempel på en fullständig Cloud Services-program kan se ut så här:  

![Cloud Services-arkitektur][9]

Service Fabric-program kan också välja att använda samma externa tjänster i en fullständig ansökan. I det här exemplet molntjänster arkitektur är den enklaste migreringsvägen från molntjänster till Service Fabric att ersätta molntjänster distributionen med ett Service Fabric-program, hålla övergripande arkitektur samma. Webb- och arbetsroller kan överföras till Service Fabric tillståndslösa tjänster med minimala kodändringar.

![Arkitektur för Service Fabric efter enkel migrering][10]

I det här skedet bör systemet fortsätta att fungera på samma sätt som innan. Dra fördel av Service Fabric tillståndskänslig funktioner, externa tillstånd lagrar kan vara internalized som stateful services tillämpliga. Detta är mer komplicerat än en enkel migrering av webb- och arbetsroller till Service Fabric tillståndslösa tjänster, eftersom den kräver att skriva anpassade tjänster som erbjuder motsvarande funktioner för tillämpningsprogrammet som externa tjänster gjorde före. Fördelarna med detta inkluderar: 

* Ta bort externa beroenden 
* Enhetlig distribution, hantering och uppgradera modeller. 

Ett exempel resulterande arkitektur internalizing dessa tjänster kan se ut så här:

![Arkitektur för Service Fabric efter fullständig migrering][11]

## <a name="communication-and-workflow"></a>Kommunikation och arbetsflöde
De flesta Molntjänsten program består av fler än en nivå. På liknande sätt kan består ett Service Fabric-program av mer än en tjänst (vanligtvis många tjänster). Två vanliga kommunikation modeller är direkt kommunikation och via en extern beständig lagring.

### <a name="direct-communication"></a>Direkt kommunikation
Med direkt kommunikation kommunicera nivåer direkt med slutpunkten som exponeras av varje nivå. I miljöer som molntjänster, detta innebär att välja en instans av en VM-roll, antingen slumpmässigt eller resursallokering för att utjämna belastningen och ansluta till sin slutpunkt direkt med tillståndslös.

![Cloud Services direkt kommunikation][5]

 Direkt kommunikation är en gemensam kommunikationsmodell i Service Fabric. Den viktigaste skillnaden mellan Service Fabric och Cloud Services är den i molntjänster som du ansluter till en virtuell dator medan i Service Fabric ansluta till en tjänst. Detta är en viktig skillnad några skäl:

* Tjänster i Service Fabric kan inte bindas till virtuella datorer som är värdar för dem. tjänster kan flytta runt i klustret och i själva verket förväntas flytta av olika skäl: resurs belastningsutjämning, redundans, program och infrastrukturen uppgraderingar och placering eller läsa in villkor. Detta innebär en tjänstinstans adress kan ändras när som helst. 
* En virtuell dator i Service Fabric kan vara värd för flera tjänster med unika slutpunkter.

Service Fabric innehåller en service discovery mekanism som kallas tjänsten namngivning, som kan användas för att lösa slutpunkts-adresser av tjänster. 

![Service Fabric direkt kommunikation][6]

### <a name="queues"></a>Köer
En gemensam mekanism för kommunikation mellan nivåer i tillståndslös miljöer, till exempel molntjänster är att använda en extern lagringsenhet kö för att lagra varaktigt arbetsuppgifter från en nivå till en annan. Ett vanligt scenario är en webbnivå som skickar jobb till en Azure eller Service Bus där Arbetsrollen instanser kan status Created och bearbeta jobben.

![Cloud Services kön kommunikation][7]

Samma kommunikation modell kan användas i Service Fabric. Detta kan vara användbart när du migrerar ett befintligt Cloud Services-program till Service Fabric. 

![Service Fabric direkt kommunikation][8]

## <a name="next-steps"></a>Nästa steg
Den enklaste migreringsvägen från molntjänster till Service Fabric är att ersätta molntjänster distributionen med ett Service Fabric-program, hålla övergripande arkitektur i ditt program ungefär samma. Följande artikel innehåller en guide för att omvandla en webbplats eller en Worker-rollen till en tillståndslös Service Fabric-tjänst.

* [Enkel migrering: konvertera en webbplats eller en Worker-rollen till en tillståndslös Service Fabric-tjänst](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
