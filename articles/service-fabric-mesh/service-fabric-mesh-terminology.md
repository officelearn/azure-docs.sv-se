---
title: Terminologi för Azure Service Fabric Mesh
description: I den här artikeln beskrivs den terminologi som används av Azure Service Fabric Mesh för att hjälpa dig att bättre förstå de termer som används i dokumentationen.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351980"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologi för Service Fabric Mesh

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. I den här artikeln beskrivs den terminologi som används av Azure Service Fabric Mesh för att hjälpa dig att bättre förstå de termer som används i dokumentationen.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) är en plattform för distribuerade system med öppen källkod som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric är orchestrator som driver Service Fabric Mesh. Service Fabric innehåller alternativ för hur du kan skapa och köra dina mikrotjänstprogram. Du kan använda alla ramverk för att skriva dina tjänster och välja var programmet ska köras från flera miljöval.

## <a name="application-and-service-concepts"></a>Program- och tjänstbegrepp

**Service Fabric Mesh Application**: Service Fabric Mesh Applications beskrivs av [resursmodellen](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML- och JSON-resursfiler) och kan distribueras till alla miljöer där Service Fabric körs.

**Inbyggt program**för serviceinfrastruktur : Inbyggda program för serviceinfrastruktur beskrivs av den [inbyggda programmodellen](/azure/service-fabric/service-fabric-application-model) (XML-baserade program- och tjänstmanifest).  Inbyggda program för serviceinfrastruktur kan inte köras i Service Fabric Mesh.

**Program**: Ett Service Fabric Mesh-program är enheten för distribution, versionshantering och livstid för ett Mesh-program. Livscykeln för varje programinstans kan hanteras oberoende av varandra.  Program består av ett eller flera servicekodpaket och inställningar. Ett program definieras med hjälp av Azure Resource Model (RM) schema.  Tjänster beskrivs som egenskaper för programresursen i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program modelleras programmet, tjänsterna, nätverket och volymen/volymerna med hjälp av resursmodellen Service Fabric.

**Tjänst:** En tjänst i ett program representerar en mikrotjänst och utför en komplett och fristående funktion. Varje tjänst består av ett eller flera kodpaket som beskriver allt som behövs för att köra behållaravbildningen som är associerad med kodpaketet.  Antalet tjänstrepliker i ett program kan skalas in och ut.

**Kodpaket:** Kodpaket beskriver allt som behövs för att köra behållaravbildningen som är associerad med kodpaketet, inklusive följande:

* Behållarnamn, version och register
* CPU- och minnesresurser som krävs för varje behållare
* Nätverksslutpunkter
* Volymer som ska monteras i behållaren, med hänvisning till en separat volymresurs.

## <a name="deployment-and-application-models"></a>Distributions- och programmodeller 

Om du vill distribuera dina tjänster måste du beskriva hur de ska köras. Service Fabric stöder tre olika distributionsmodeller:

### <a name="resource-model"></a>Resursmodell
Service Fabric Resources är allt som kan distribueras individuellt till Service Fabric; inklusive applikationer, tjänster, nätverk och volymer. Resurser definieras med hjälp av en JSON-fil som kan distribueras till en klusterslutpunkt.  För Service Fabric Mesh används Azure Resource Model-schemat. Ett YAML-filschema kan också användas för att lättare skapa definitionsfiler. Resurser kan distribueras överallt där Service Fabric körs. Resursmodellen är det enklaste sättet att beskriva dina Service Fabric-program. Dess huvudsakliga fokus ligger på enkel distribution och hantering av containeriserade tjänster. Mer information finns [i Introduktion till resursmodellen För serviceinfrastruktur](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Inbyggd modell
Den inbyggda programmodellen ger dina program full åtkomst på låg nivå till Service Fabric. Program och tjänster definieras som registrerade typer i XML-manifestfiler.

Den inbyggda modellen stöder reliable services-ramverket, som ger åtkomst till API:er för service fabric-körning och klusterhanterings-API:er i C# och Java. Den inbyggda modellen stöder också godtyckliga behållare och körbara filer.

Den inbyggda modellen stöds inte i service fabric mesh-miljön.  Mer information finns i [översikt över programmeringsmodeller](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Komponera 
[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric ger begränsat stöd för distribution av program med Docker Compose-modellen.

## <a name="environments"></a>Miljöer

Service Fabric är en plattformsteknik med öppen källkod som flera olika tjänster och produkter bygger på. Microsoft innehåller följande alternativ:

 - **Service Fabric Mesh**: En fullständigt hanterad tjänst för att köra Service Fabric-program i Microsoft Azure.
 - **Azure Service Fabric**: Azure-värdtjänsten Fabric-klustererbjudande. Det ger integrering mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfigurationshantering av Service Fabric-kluster.
 - **Service Fabric fristående**: En uppsättning installations- och konfigurationsverktyg för att [distribuera Service Fabric-kluster var som helst](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalt eller på vilken molnleverantör som helst). Hanteras inte av Azure.
 - **Utvecklingskluster**för serviceinfrastruktur : Ger en lokal utvecklingsupplevelse på Windows, Linux eller Mac för utveckling av Service Fabric-program.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Stödmatris för miljö-, ram- och distributionsmodell
Olika miljöer har olika stödnivå för ramverk och distributionsmodeller. I följande tabell beskrivs kombinationerna ramverk och distributionsmodell som stöds.

| Typ av program | Beskrivs av | Azure Service Fabric Mesh | Azure Service Fabric-kluster (alla operativsystem)| Lokalt kluster | Fristående kluster |
|---|---|---|---|---|---|
| Mesh-program för servicevävnad | Resursmodell (YAML & JSON) | Stöds |Stöds inte | Windows-stödda, Linux och Mac- stöds inte | Windows- stöds inte |
|Inbyggda program för serviceinfrastruktur | Inbyggd programmodell (XML) | Stöds inte| Stöds|Stöds|Windows- stöds|

I följande tabell beskrivs de olika programmodellerna och de verktyg som finns för dem mot Service Fabric.

| Typ av program | Beskrivs av | Visual Studio | Eclipse | SFCTL (SFCTL) | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Mesh-program för servicevävnad | Resursmodell (YAML & JSON) | VS 2017 |Stöds inte |Stöds inte | Stöds - Endast mesh-miljö | Stöds inte|
|Inbyggda program för serviceinfrastruktur | Inbyggd programmodell (XML) | VS 2017 och VS 2015| Stöds|Stöds|Stöds|Stöds|

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).
