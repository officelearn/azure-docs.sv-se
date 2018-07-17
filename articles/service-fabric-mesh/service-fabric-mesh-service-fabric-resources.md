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
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076483"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduktion till Service Fabric-Resursmodell

Service Fabric-Resursmodell beskriver en enkel metod för att definiera resurser som utgör ett nät för Service Fabric-program. Följande typer av resurser stöds för närvarande i den här modellen:

- Program
- Tjänster
- Volymer
- Nätverk

Varje resurs beskrivs deklarativt i en resursfil, vilket är en enkel YAML eller JSON-dokument som beskriver programmet nät och etableras av Service Fabric-plattformen.

## <a name="resource-overview"></a>Resursöversikt

### <a name="applications-and-services"></a>Program och tjänster

En resurs för en är Faktureringsenhet distribution, versionshantering och livslängden för en nät-programmet. Den består av en eller flera resurser som representerar en mikrotjänst. Varje tjänstresurs i sin tur består av en eller flera, kodpaket som beskriver allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet, inklusive följande:

- Behållarens namn, version och registret
- Processor- och resurser som krävs för varje behållare
- Nätverksslutpunkter
- Volymer ska monteras i behållaren som refererar till en separat volym-resurs.

Alla kodpaket som har definierats som en del av en tjänstresurs distribueras och aktiveras tillsammans som en grupp. Tjänstresursen beskriver också hur många instanser av tjänsten körs och även hänvisar till andra resurser (till exempel nätverksresurs) som den är beroende.

Om en nät program består av mer än en tjänst, säkert inte att köra tillsammans på samma nod. Dessutom under en uppgradering av programmet leder misslyckades med att uppgradera en enskild tjänst till alla tjänster som att de återställs till sina tidigare version.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Eftersom alluded tidigare kan livscykeln för varje programinstans hanteras oberoende av varandra. Till exempel en programinstans kan uppgraderas separat från andra programinstanser. Normalt kan behåller du antal tjänster i ett program liten och som fler tjänster du lägga till ett program, den svårare blir det för att hantera respektive tjänst oberoende av varandra.

### <a name="networks"></a>Nätverk

Nätverksresurs är individuellt distribuerbar resurs, oberoende av en resurs för programmet eller tjänsten som kan referera till den som deras beroende. Den används för att skapa ett privat nätverk för dina program. Flera tjänster från olika program kan inte ingå i samma nätverk.

> [!NOTE]
> Den aktuella förhandsversionen stöder bara en en-till-en-mappning mellan program och nätverk

### <a name="volumes"></a>Volymer

Volymer är kataloger som monteras i dina behållarinstanser som du kan använda för att bevara tillstånd. Resurs-volym är en deklarativ metod för att beskriva hur en katalog är monterad och datalagret för den.

## <a name="programming-models"></a>Programmeringsmodeller
Tjänstresurs kräver endast en behållaravbildning för att köra, som refereras i kod-paket som hör till resursen. Du kan köra valfri kod skrivna på valfritt språk, med valfritt ramverk i behållaren utan för att känna till eller använda Service Fabric-nät specifika API: er. 

Programkoden förblir bärbar även utanför Service Fabric-nät och distribution av program förblir konsekventa oavsett språk eller ramverk som används för att implementera dina tjänster. Om ditt program är ASP.NET Core, Go eller en uppsättning metoder och skript, densamma Service Fabric nät resursdistributionsmodell. 

## <a name="deployment"></a>Distribution

När du distribuerar till Service Fabric-nät, som resurser distribueras som en Azure Resource Manager-mall till Azure via HTTP eller Azure CLI. 


## <a name="next-steps"></a>Nästa steg 
Läs översikten om du vill veta mer om Service Fabric-nät kan:
- [Service Fabric-nät översikt](service-fabric-mesh-overview.md)
