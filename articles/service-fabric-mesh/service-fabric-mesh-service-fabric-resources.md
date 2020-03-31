---
title: Introduktion till Azure Service Fabric Resource Model
description: Lär dig mer om Service Fabric Resource Model, en förenklad metod för att definiera Service Fabric Mesh-program.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259141"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduktion till Service Fabric Resource Model

Service Fabric Resource Model beskriver en enkel metod för att definiera resurser som utgör ett Service Fabric Mesh-program. Enskilda resurser kan distribueras till alla Service Fabric-miljöer.  Service Fabric Resource Model är också kompatibel med Azure Resource Manager-modellen. Följande typer av resurser stöds för närvarande i den här modellen:

- Program och tjänster
- Nätverk
- Gateways
- Hemligheter och hemligheter / Värden
- Volymer

Varje resurs beskrivs deklarativt i en resursfil, vilket är ett enkelt YAML- eller JSON-dokument som beskriver meshprogrammet och som etableras av Service Fabric-plattformen.

## <a name="applications-and-services"></a>Program och tjänster

En programresurs är enheten för distribution, versionshantering och livstid för ett Mesh-program. Den består av en eller flera tjänstresurser som representerar en mikrotjänst. Varje tjänstresurs består i sin tur av ett eller flera kodpaket som beskriver allt som behövs för att köra behållaravbildningen som är associerad med kodpaketet.

![Appar och tjänster][Image1]

En tjänstresurs deklarerar följande:

- Behållarnamn, version och register
- CPU- och minnesresurser som krävs för varje behållare
- Nätverksslutpunkter
- Referenser till andra resurser som nätverk, volymer och hemligheter 

Alla kodpaket som definierats som en del av en tjänstresurs distribueras och aktiveras tillsammans som en grupp. Serviceresursen beskriver också hur många instanser av tjänsten som ska köras och refererar även till andra resurser (till exempel Nätverksresurs) som den är beroende av.

Om ett mesh-program består av mer än en tjänst garanteras de inte att köras tillsammans på samma nod. Under en uppgradering av programmet kommer det dessutom inte att leda till att alla tjänster återställs till den tidigare versionen under en uppgradering av programmet.

Som nämnts tidigare kan livscykeln för varje programinstans hanteras oberoende av varandra. En programinstans kan till exempel uppgraderas oberoende av de andra programinstanserna. Vanligtvis håller du antalet tjänster i ett program ganska liten, eftersom ju fler tjänster du lägger in i ett program, desto svårare blir det att hantera varje tjänst självständigt.

## <a name="networks"></a>Nätverk

Nätverksresursen är individuellt distributionsbar resurs, oberoende av en program- eller tjänstresurs som kan referera till den som sitt beroende. Det används för att skapa ett nätverk för dina program. Flera tjänster från olika program kan ingå i samma nätverk.  Mer information finns [i om nätverk i Service Fabric Mesh-program](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Den aktuella förhandsversionen stöder bara en mappning mellan program och nätverk

![Nätverk och gateway][Image2]

## <a name="gateways"></a>Gateways
En gateway-resurs ansluter två nätverk och dirigerar trafik.  En gateway gör att dina tjänster kan kommunicera med externa klienter och ger en inträngning i dina tjänster.  En gateway kan också användas för att ansluta ditt Mesh-program med ditt eget, befintliga virtuella nätverk. Mer information finns [i om nätverk i Service Fabric Mesh-program](service-fabric-mesh-networks-and-gateways.md).

![Nätverk och gateway][Image2]

## <a name="secrets"></a>Hemligheter

Hemligheter resurser kan distribueras oberoende av ett program eller tjänst resurs som kan hänvisa till det som sitt beroende. Det används för att säkert leverera hemligheter till dina program. Flera tjänster från olika program kan referera till värden för samma hemlighet.

## <a name="volumes"></a>Volymer

Behållare gör ofta tillfälliga diskar tillgängliga. Tillfälliga diskar är dock tillfälliga, så du får en ny tillfällig disk och förlorar informationen när en behållare kraschar. Det är också svårt att dela information om tillfälliga diskar med andra behållare. Volymer är kataloger som monteras i behållarinstanserna som du kan använda för att bevara tillståndet. Volymer ger dig allmänt ändamål fillagring och kan du läsa / skriva filer med hjälp av vanliga disk I / O-fil API: er. Volymresursen är ett deklarativt sätt att beskriva hur en katalog är monterad och bakgrundslagring för den (antingen Azure Files Volume eller Service Fabric Reliable Volume).  Mer information finns [i lagringstillståndet](service-fabric-mesh-storing-state.md#volumes).

![Volymer][Image3]

## <a name="programming-models"></a>Programmeringsmodeller
Tjänstresurs kräver bara en behållaravbildning som ska köras, som refereras i kodpaketen som är associerade med resursen. Du kan köra vilken kod som helst, skriven på vilket språk som helst, med valfri ramverk i behållaren utan att behöva känna till eller använda specifika API:er för Service Fabric Mesh. 

Programkoden förblir portabel även utanför Service Fabric Mesh och dina programdistributioner förblir konsekventa oavsett vilket språk eller ramverk som används för att implementera dina tjänster. Oavsett om ditt program är ASP.NET Core, Go eller bara en uppsättning processer och skript förblir distributionsmodellen för Service Fabric Mesh Resource densamma. 

## <a name="packaging-and-deployment"></a>Förpackning och distribution

Service Fabric Mesh-program baserade på resursmodellen är paketerade som Docker-behållare.  Service Fabric Mesh är en delad miljö med flera innehavare och behållare ger dig en hög isoleringsnivå.  Dessa program beskrivs med ett JSON-format eller ett YAML-format (som sedan konverteras till JSON). När du distribuerar ett Mesh-program till Azure Service Fabric Mesh är JSON som används för att beskriva programmet en Azure Resource Manager-mall. Resurser mappas till Azure-resurser.  När du distribuerar ett Mesh-program till ett Service Fabric-kluster (fristående eller Azure-värd) är JSON som används för att beskriva programmet ett format som liknar en Azure Resource Manager-mall.  När mesh-program har distribuerats kan de hanteras via HTTP-gränssnitt eller Azure CLI. 


## <a name="next-steps"></a>Nästa steg 
Mer information om Service Fabric Mesh finns i översikten:
- [Service Fabric Mesh-översikt](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
