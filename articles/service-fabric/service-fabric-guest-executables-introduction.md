---
title: Distribuera en befintlig körbar fil till Azure Service Fabric | Microsoft Docs
description: Läs mer om paketering ett befintligt program som en körbara gästfiler, så att du kan distribuera till ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946679"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Distribuera en befintlig körbar fil till Service Fabric
Du kan köra alla typer av kod som Node.js, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric refererar till dessa typer av tjänster som körbara gästfiler.

Körbara gäster behandlas av Service Fabric som tillståndslösa tjänster. Därför kan placeras de på noder i ett kluster, baserat på tillgänglighet och andra mått. Den här artikeln beskriver hur du paketera och distribuera en körbar Gäst till ett Service Fabric-kluster med hjälp av Visual Studio eller ett kommandoradsverktyg.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Fördelar med att köra gäst körbara i Service Fabric
Det finns flera fördelar med att köra gäst körbara i ett Service Fabric-kluster:

* Hög tillgänglighet. Program som körs i Service Fabric görs med hög tillgänglighet. Service Fabric garanterar att instanser av ett program körs.
* Övervakning av hälsotillstånd. Service Fabric-hälsoövervakning identifierar om ett program körs och ger diagnostisk information om det uppstår ett fel.   
* Livscykelhantering för program. Förutom att tillhandahålla uppgraderingar utan avbrott, tillhandahåller Service Fabric automatisk återställning till föregående version om det finns en felaktig hälsohändelse som har rapporterat under en uppgradering.    
* Densitet. Du kan köra flera program i ett kluster, vilket eliminerar behovet för varje program ska köras på en egen maskinvara.
* För att göra: Med hjälp av REST kan du anropa Namngivningstjänsten för Service Fabric-tjänsten för att hitta andra tjänster i klustret. 

## <a name="samples"></a>Exempel
* [Exempel för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via tjänsten namngivning med hjälp av REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Översikt över program och tjänsten manifestfiler
Som en del av att distribuera en körbar Gäst, är det praktiskt att förstå modellen för Service Fabric-paketering och distribution enligt beskrivningen i [programmodell](service-fabric-application-model.md). Service Fabric paketering modellen är beroende av två XML-filer: manifest för program och tjänster. Schemadefinitionen för ApplicationManifest.xml och ServiceManifest.xml har installerats med Service Fabric SDK i *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Programmanifest** applikationsmanifestet används för att beskriva programmet. Den Listar de tjänster som ska utgöra den och andra parametrar som används för att definiera hur en eller flera tjänster som ska distribueras, till exempel hur många instanser.

  I Service Fabric är ett program en enhet för distribution och uppgradering. Ett program kan uppgraderas som en enhet där potentiella fel och potentiella återställningar hanteras. Service Fabric garanterar att uppgraderingsprocessen är antingen lyckas, eller om uppgraderingen misslyckas inte ger programmet i ett okänt eller instabilt tillstånd.
* **Tjänstmanifestet** tjänstmanifestet beskriver komponenterna av en tjänst. Den innehåller data, till exempel namn och typ av tjänst, och dess kod och konfiguration. Tjänstmanifestet omfattar även vissa ytterligare parametrar som kan användas för att konfigurera tjänsten när den har distribuerats.

## <a name="application-package-file-structure"></a>Filstruktur för program-paket
Om du vill distribuera ett program till Service Fabric bör programmet följa en fördefinierad katalogstruktur. Följande är ett exempel på denna struktur.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot innehåller filen applicationmanifest.XML som definierar programmet. En underkatalog för varje tjänst som ingår i programmet används för att innehålla alla artefakter som krävs för tjänsten. Dessa underkataloger är ServiceManifest.xml och vanligtvis följande:

* *Koden*. Den här katalogen innehåller koden.
* *Config*. Den här katalogen innehåller en Settings.xml-fil (och andra filer vid behov) att tjänsten kan komma åt vid körning för att hämta specifika konfigurationsinställningar.
* *Data*. Det här är en ytterligare katalog att lagra ytterligare lokala data som tjänsten kan behöva. Data bör användas för att lagra endast tillfälliga data. Service Fabric Kopiera inte eller replikera ändringar till datakatalogen om tjänsten som behöver flyttas (till exempel vid redundans).

> [!NOTE]
> Du behöver skapa den `config` och `data` kataloger om du inte behöver dem.
>
>

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för relaterad information och uppgifter.
* [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa din första körbart gästprogram med Visual Studio](quickstart-guest-app.md)
* [Exempel för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till förhandsutgåvan av verktyget paketering
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via tjänsten namngivning med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)

