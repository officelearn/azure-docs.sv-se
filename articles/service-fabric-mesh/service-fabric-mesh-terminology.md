---
title: Terminologi för Azure Service Fabric-nät | Microsoft Docs
description: Läs mer om vanliga termer för Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 925d97658a299bea983b16ae6b507159ef0e9e62
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979139"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric-nät terminologi

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Den här artikeln beskriver de termer som används av Azure Service Fabric nät för att förstå de termer som används i dokumentationen.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) är en distribuerad systemplattform för öppen källkod som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric är orchestrator som används av Service Fabric-nät. Service Fabric tillhandahåller alternativ för hur du kan skapa och kör dina mikrotjänstprogram. Du kan använda valfritt ramverk för att skriva dina tjänster och välj var du vill köra programmet från miljön välja mellan flera alternativ.

## <a name="application-and-service-concepts"></a>Program- och koncept

**Service Fabric-nät program**: Service Fabric-nät program beskrivs av den [Resursmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (resursfiler YAML och JSON) och kan distribueras till alla miljöer där Service Fabric kan köras.

**Service Fabric-interna program**: interna program för Service Fabric beskrivs av den [interna programmodell](/azure/service-fabric/service-fabric-application-model) (XML-baserade program och tjänstmanifest).  Interna program för Service Fabric kan inte köras i Service Fabric-nät.

**Programmet**: nät till ett Service Fabric-program är Faktureringsenhet distribution, versionshantering och livslängden för en nät-programmet. Livscykeln för varje programinstans kan hanteras oberoende av varandra.  Program består av en eller flera kodpaket och inställningar. Ett program har definierats med hjälp av Azure Resource Model (RM)-schemat.  Services beskrivs som egenskaper för programresurs i en RM-mall.  Nätverk och volymer som används av programmet refereras av programmet.  När du skapar ett program, modelleras program, tjänster, nätverk och volymer med Service Fabric-Resursmodell.

**Tjänsten**: en tjänst i ett program som representerar en mikrotjänst och utför en fullständig och fristående funktion. Varje tjänst består av en eller flera, kodpaket som beskriver allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet.  Antal tjänster i ett program kan skalas upp och ned.

**Kodpaketet**: kodpaket beskriva allt som behövs för att köra den behållaravbildning som är associerade med kodpaketet, inklusive följande:

* Behållarens namn, version och registret
* Processor- och resurser som krävs för varje behållare
* Nätverksslutpunkter
* Volymer ska monteras i behållaren som refererar till en separat volym-resurs.

## <a name="deployment-and-application-models"></a>Modeller för distribution och program 

Om du vill distribuera dina tjänster behöver du att beskriva hur de ska köras. Service Fabric har stöd för tre olika distributionsmodeller:

### <a name="resource-model"></a>Resursmodell
Service Fabric-resurser är något som kan distribueras separat till Service Fabric; inklusive program, tjänster, nätverk och volymer. Resurserna definieras med hjälp av en JSON-fil som kan distribueras till en slutpunkt för klustret.  Azure Resource Manager-schema används för Service Fabric-nät. Ett schema för YAML-fil kan också användas för att enkelt skapa definitionsfiler. Resurser kan distribueras som helst Service Fabric kan köras. Resurs-modellen är det enklaste sättet att beskriva dina Service Fabric-program. Dess huvudsakliga fokus ligger på enkel distribution och hantering av tjänster i behållare. Mer information, [introduktion till Service Fabric-Resursmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Inbyggda datamodellen
Internt programmodellen innehåller dina program med fullständig lågnivå-åtkomst till Service Fabric. Program och tjänster definieras som registrerade typer i XML-manifest-filer.

Den inbyggda datamodellen har stöd för Reliable Services-ramverk, som ger åtkomst till Service Fabric-körningen API: er och hantering av API: er i C# och Java. Den inbyggda datamodellen stöder också godtyckliga behållare och körbara filer.

Den inbyggda datamodellen stöds inte i Service Fabric-nät-miljö.  Mer information finns i [programming översikt över](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) är en del av Docker-projektet. Service Fabric har begränsat stöd för distribution av program med hjälp av Docker Compose-modellen.

## <a name="environments"></a>Miljöer

Service Fabric är en teknik för öppen källkodsplattform som flera olika tjänster och produkter som är baserade på. Microsoft tillhandahåller följande alternativ:

 - **Service Fabric-nät**: en fullständigt hanterad tjänst för att köra Service Fabric-program i Microsoft Azure.
 - **Azure Service Fabric**: Azure Service Fabric-kluster värdtjänst. Det ger integrering mellan Service Fabric och Azure-infrastrukturen, tillsammans med uppgradering och konfiguration hantering av Service Fabric-kluster.
 - **Fristående Service Fabric**: en uppsättning verktyg för installation och konfiguration att [distribuera Service Fabric-kluster var som helst](/azure/service-fabric/service-fabric-deploy-anywhere) (lokalt eller på någon annan molnleverantör). Inte hanteras av Azure.
 - **Service Fabric-kluster för utveckling**: tillhandahåller en lokal utveckling på Windows, Linux eller Mac för utveckling av Service Fabric-program.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Supportmatris för miljön, ramverk och distributionsmodellen
Olika miljöer har olika stöd för ramverk och distributionsmodeller. I följande tabell beskrivs stöds framework och distribution av modellen kombinationer.

| Typ av program | Beskrivningen av | Azure Service Fabric-nät | Azure Service Fabric-kluster (alla OS)| Lokalt kluster | Fristående kluster |
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric-nät program | Resursmodell (YAML & JSON) | Stöds |Stöds inte | Windows - stöd för Linux och Mac-inte stöds | Windows-inte stöds |
|Interna Service Fabric-program | Internt programmodell (XML) | Stöds inte| Stöds|Stöds|Windows – stöds|

I följande tabell beskrivs de olika program-modellerna och verktyg som finns för dem mot Service Fabric.

| Typ av program | Beskrivningen av | Visual Studio | Eclipse | SFCTL | AZ-CLI | PowerShell|
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric-nät program | Resursmodell (YAML & JSON) | VS 2017 |Stöds inte |Stöds inte | Stöd för – nät-miljö | Stöds inte|
|Interna Service Fabric-program | Internt programmodell (XML) | VS 2017 och VS 2015| Stöds|Stöds|Stöds|Stöds|

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).

Få svar på [vanliga frågor](service-fabric-mesh-faq.md).
