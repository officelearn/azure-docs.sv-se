---
title: Introduktion till Azure Service Fabric-Resursmodell | Microsoft Docs
description: Läs mer om Service Fabric-Resource-modellen, en förenklad metod för att definiera program med Service Fabric-nät.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810718"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduktion till Service Fabric-Resursmodell

Service Fabric-Resursmodell beskriver en enkel metod för att definiera resurser som utgör ett nät för Service Fabric-program. Enskilda resurser kan distribueras till vilken miljö som Service Fabric.  Service Fabric-Resursmodell är kompatibelt med Azure Resource Manager-modellen. Följande typer av resurser stöds för närvarande i den här modellen:

- Program och tjänster
- Nätverk
- Gateways
- Hemligheter och hemligheter/värden
- Volymer

Varje resurs beskrivs deklarativt i en resursfil, vilket är en enkel YAML eller JSON-dokument som beskriver programmet nät och etableras av Service Fabric-plattformen.

## <a name="applications-and-services"></a>Program och tjänster

En resurs för en är Faktureringsenhet distribution, versionshantering och livslängden för en nät-programmet. Den består av en eller flera, Service-resurser som representerar en mikrotjänst. Varje tjänstresurs i sin tur består av en eller flera, kodpaket som beskriver allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet.

![Appar och tjänster][Image1]

En tjänstresurs deklarerar du följande:

- Behållarens namn, version och registret
- Processor- och resurser som krävs för varje behållare
- Nätverksslutpunkter
- Referenser till andra resurser, till exempel nätverk, volymer och hemligheter 

Alla kodpaket som har definierats som en del av en tjänstresurs distribueras och aktiveras tillsammans som en grupp. Tjänstresursen beskriver också hur många instanser av tjänsten körs och även hänvisar till andra resurser (till exempel nätverksresurs) som den är beroende.

Om en nät program består av mer än en tjänst, säkert inte att köra tillsammans på samma nod. Dessutom under en uppgradering av programmet leder misslyckades med att uppgradera en enskild tjänst till alla tjänster som att de återställs till sina tidigare version.

Eftersom alluded tidigare kan livscykeln för varje programinstans hanteras oberoende av varandra. Till exempel en programinstans kan uppgraderas separat från andra programinstanser. Normalt kan behåller du antal tjänster i ett program liten och som fler tjänster du lägga till ett program, den svårare blir det för att hantera respektive tjänst oberoende av varandra.

## <a name="networks"></a>Nätverk

Nätverksresurs är individuellt distribuerbar resurs, oberoende av en resurs för programmet eller tjänsten som kan referera till den som deras beroende. Den används för att skapa ett nätverk för dina program. Flera tjänster från olika program kan inte ingå i samma nätverk.  Mer information finns i avsnittet om [nätverksfunktionerna i Service Fabric-nät program](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Den aktuella förhandsversionen stöder bara en en-till-en-mappning mellan program och nätverk

![Nätverk och gateway][Image2]

## <a name="gateways"></a>Gateways
En resurs för gatewayen ansluter två nätverk och dirigerar trafik.  En gateway kan dina tjänster för att kommunicera med externa klienter och tillhandahåller en ingående om dina tjänster.  En gateway kan också användas för att ansluta nät appen med din egen, befintligt virtuellt nätverk. Mer information finns i avsnittet om [nätverksfunktionerna i Service Fabric-nät program](service-fabric-mesh-networks-and-gateways.md).

![Nätverk och gateway][Image2]

## <a name="secrets"></a>Hemligheter

Hemligheter resurser är distribueras oberoende av ett program eller en tjänstresurs som kan referera till den som deras beroende. Används för att leverera hemligheter på ett säkert sätt till dina program. Flera tjänster från olika program kan referera till värden för samma hemlighet.

## <a name="volumes"></a>Volymer

Behållare tillgängliggöra ofta temporära diskar. Temporära diskar är dock tillfälliga, så du får en ny tillfällig disk och förlora informationen när en behållare kraschar. Det är också svårt att dela information om temporära diskar med andra behållare. Volymer är kataloger som monteras i dina behållarinstanser som du kan använda för att bevara tillstånd. Volymer ger dig allmänna fillagring och gör att du kan läsa/skriva filer med hjälp av normal disk-i/o-fil som API: er. Resurs-volym är en deklarativ metod för att beskriva hur en katalog är monterad och datalagret för den (Azure filer volym eller Service Fabric tillförlitliga volym).  Mer information finns i [lagra tillstånd](service-fabric-mesh-storing-state.md#volumes).

![Volymer][Image3]

## <a name="programming-models"></a>Programmeringsmodeller
Tjänstresurs kräver endast en behållaravbildning för att köra, som refereras i kod-paket som hör till resursen. Du kan köra valfri kod skrivna på valfritt språk, med valfritt ramverk i behållaren utan för att känna till eller använda Service Fabric-nät specifika API: er. 

Programkoden förblir bärbar även utanför Service Fabric-nät och distribution av program förblir konsekventa oavsett språk eller ramverk som används för att implementera dina tjänster. Om ditt program är ASP.NET Core, Go eller en uppsättning metoder och skript, densamma Service Fabric nät resursdistributionsmodell. 

## <a name="packaging-and-deployment"></a>Paketering och distribution

Service Fabric-nät program baserat på vilken resursmodell som är packade som Docker-behållare.  Service Fabric-nät är en delad miljö med flera innehavare och behållare ger dig en hög nivå av isolering.  Dessa program beskrivs med hjälp av JSON-format eller en YAML-format (som sedan konverteras till JSON). När du distribuerar ett nät program till Azure Service Fabric-nät är en Azure Resource Manager-mall i JSON som används för att beskriva programmet. Resurser har mappats till Azure-resurser.  När du distribuerar en nät programmet till ett Service Fabric-kluster (fristående eller Azure-värdbaserade), den JSON som används för att beskriva programmet är ett format som liknar en Azure Resource Manager-mall.  När distribuerats kan nät program hanteras via HTTP-gränssnitt eller Azure CLI. 


## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
