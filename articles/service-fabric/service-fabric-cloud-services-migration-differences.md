---
title: Skillnader mellan Cloud Services och Service Fabric | Microsoft Docs
description: En översikt för att migrera program från Cloud Services till Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4682e47e664384a6869e1a74e3de6d9083db082b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669460"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Läs om skillnaderna mellan Cloud Services och Service Fabric innan du migrerar program.
Microsoft Azure Service Fabric är en nästa generations program molnplattform för mycket skalbara, tillförlitliga distribuerade program. Den innehåller många nya funktioner för paketering, distribution, uppgradera och hantera distribuerade molnappar. 

Det här är en inledande guide för att migrera program från Cloud Services till Service Fabric. Det handlar huvudsakligen om arkitekturen och designskillnader mellan Cloud Services och Service Fabric.

## <a name="applications-and-infrastructure"></a>Program och infrastruktur
En grundläggande skillnaden mellan Cloud Services och Service Fabric är förhållandet mellan virtuella datorer, arbetsbelastningar och program. En arbetsbelastning här definieras som koden du skriver så att utföra en viss uppgift eller tillhandahålla en tjänst.

* **Cloud Services är hur du distribuerar program som virtuella datorer.** Koden du skriver är direkt kopplade till en VM-instans, till exempel en webb- eller Worker-roll. Om du vill distribuera en arbetsbelastning i Cloud Services är för att distribuera en eller flera VM-instanser som kör arbetsbelastningen. Det finns ingen åtskillnad mellan program och virtuella datorer och så det finns inga formellt definieras för ett program. Ett program kan betraktas som en uppsättning webb- eller Arbetsroll instanser i en Cloud Services-distribution eller som en hela Cloud Services-distribution. I det här exemplet visas ett program som en uppsättning rollinstanser.

![Cloud Services-program och topologi][1]

* **Service Fabric är hur du distribuerar program till befintliga virtuella datorer eller datorer som kör Service Fabric i Windows eller Linux.** Tjänster som du skriver är helt frikopplad från den underliggande infrastrukturen som representeras direkt av Service Fabric-plattformen för program så att ett program kan bara distribueras till flera miljöer. En arbetsbelastning i Service Fabric kallas en ”tjänst” och en eller flera tjänster som är grupperade i ett formellt definieras program som körs på plattformen för Service Fabric-program. Flera program kan bara distribueras till ett enda Service Fabric-kluster.

![Service Fabric-program och topologi][2]

Service Fabric själva är ett lager för application platform som körs på Windows eller Linux, Cloud Services är ett system för att distribuera Azure-hanterade virtuella datorer med arbetsbelastningar som är anslutna.
Programmodellen Service Fabric har ett antal fördelar:

* Snabb distribution gånger. Skapa VM-instanser kan ta lång tid. I Service Fabric kan distribueras endast virtuella datorer när för att skapa ett kluster som är värd för programmet Service Fabric-plattformen. Från den punkten på kan programpaket bara distribueras till klustret mycket snabbt.
* Högdensitetsvärd. I Cloud Services, en Worker-rollen virtuell dator är värd för en arbetsbelastning. I Service Fabric är program separat från de virtuella datorer som kör dem, vilket innebär att du kan distribuera ett stort antal program till ett litet antal virtuella datorer som kan sänka övergripande kostnader för större distributioner.
* Service Fabric plattformen kan köras var som helst som har Windows Server eller Linux-datorer, oavsett om det är Azure eller lokalt. Plattformen innehåller ett Abstraktionslager över den underliggande infrastrukturen så att ditt program kan köras på olika miljöer. 
* Hantering av distribuerade program. Service Fabric är en plattform som inte bara värd för distribuerade program, utan även hjälper till att hantera livscykeln oberoende av den värdbaserade virtuella datorn eller datorn livscykel.

## <a name="application-architecture"></a>Programarkitektur
Arkitekturen för en Cloud Services-program innefattar vanligtvis ett stort antal externa tjänstens beroenden, till exempel Service Bus, Azure Table och Blob Storage, SQL, Redis och andra att hantera tillstånd och data för ett program och kommunikation mellan Web och Worker-roller i en distribution av molntjänster. Ett exempel på ett helt Cloud Services-program kan se ut så här:  

![Arkitektur för cloud Services][9]

Service Fabric-program kan också välja att använda samma externa tjänster i hela appen. I det här exemplet molntjänster arkitektur är den enklaste migreringsvägen från Cloud Services till Service Fabric att ersätta endast Cloud Services-distribution med ett Service Fabric-program, att hålla den övergripande arkitekturen samma. Webb- och arbetsroller kan överföras till tillståndslös Service Fabric-tjänster med minimala kodändringar.

![Arkitektur för Service Fabric efter enkel migrering][10]

Systemet bör fortsätta att fungera på samma sätt som tidigare i det här skedet. Dra fördel av Service Fabric tillståndskänsliga funktioner, externa tillstånd butiker kan vara internalized när tillståndskänslig tjänsterna om tillämpligt. Det här är mer komplicerad än en enkel migrering av webb- och Worker-roller till tillståndslös Service Fabric-tjänster, eftersom den kräver att skriva anpassade tjänster med motsvarande funktioner i ditt program som externa tjänster gjorde förut. Fördelar med detta är: 

* Ta bort externa beroenden 
* Förena distributionen, hanteringen och uppgradera modeller. 

Ett exempel resulterande arkitekturen för internalizing dessa tjänster kan se ut så här:

![Arkitektur för Service Fabric efter fullständig migrering][11]

## <a name="communication-and-workflow"></a>Kommunikationen och arbetsflödet
De flesta molntjänst program består av fler än en nivå. På samma sätt kan består ett Service Fabric-program av mer än en tjänst (vanligtvis många tjänster). Två vanliga kommunikation modellerna är direkt kommunikation och via en extern varaktig lagring.

### <a name="direct-communication"></a>Direkt kommunikation
Nivåer kan kommunicera direkt via slutpunkt som exponeras av varje nivå med direkt kommunikation. I tillståndslösa miljöer Cloud Services, det här innebär att välja en instans av en VM-roll, antingen slumpmässigt och resursallokering saldo belastningen och ansluta till slutpunkten direkt.

![Cloud Services direkt kommunikation][5]

 Direkt kommunikation är en gemensam kommunikationsmodell för i Service Fabric. Den viktigaste skillnaden mellan Service Fabric och Cloud Services är den i molntjänster som du ansluter till en virtuell dator, medan i Service Fabric ansluter du till en tjänst. Det här är en viktig skillnad några skäl:

* Tjänster i Service Fabric är inte kopplade till de virtuella datorerna som är värdar för dem. tjänster kan flytta runt i klustret, och i själva verket förväntas flytta omkring av olika anledningar: Resursen belastningsutjämning, växling vid fel, program och infrastruktur uppgraderingar och placering eller läsa in villkor. Det innebär att en tjänstinstans-adress kan ändras när som helst. 
* En virtuell dator i Service Fabric kan vara värd för flera tjänster med unika slutpunkter.

Service Fabric är en service discovery mekanism som heter tjänsten namngivning, som kan användas för att lösa slutpunktsadresser av tjänster. 

![Service Fabric direkt kommunikation][6]

### <a name="queues"></a>Köer
En gemensam mekanism för kommunikation mellan nivåer i tillståndslösa miljöer, till exempel Cloud Services är att använda en extern storage-kö för att lagra varaktigt arbetsuppgifter från en nivå till en annan. Ett vanligt scenario är en webbnivå som skickar jobb till en Azure-kö eller Service Bus där Worker-rollinstanser kan ta bort från kön och bearbeta jobben.

![Cloud Services kö kommunikation][7]

Samma kommunikation modell kan användas i Service Fabric. Detta kan vara användbart när du migrerar en befintlig Cloud Services-program till Service Fabric. 

![Service Fabric direkt kommunikation][8]

## <a name="next-steps"></a>Nästa steg
Den enklaste migreringsvägen från Cloud Services till Service Fabric är att ersätta endast Cloud Services-distribution med ett Service Fabric-program, som den övergripande arkitekturen för ditt program ungefär samma. I följande artikel finns en guide för att konvertera en webb- eller Worker-roll till en tillståndslös Service Fabric-tjänst.

* [Enkel migrering: konvertera en webb- eller Worker-roll till en tillståndslös Service Fabric-tjänst](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
