---
title: Distribuera en befintlig körbar fil till Azure Service Fabric | Microsoft Docs
description: Läs mer om paketera ett befintligt program som gäst körbara, så den kan användas för Service Fabric-klustret.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: mfussell
ms.openlocfilehash: cdaf3dae12c2c9da1f6bcbebbff560b98e62bade
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Distribuera en befintlig körbar fil till Service Fabric
Du kan köra alla typer av kod, exempelvis Node.js, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric refererar till dessa typer av tjänster som gäst körbara filer.

Gästen körbara filer behandlas av Service Fabric som tillståndslösa tjänster. Detta innebär att de är placerade på noder i ett kluster, baserat på tillgänglighet och andra mått. Den här artikeln beskriver hur du paketet och distribuerar gäst körbara till Service Fabric-kluster med hjälp av Visual Studio eller ett kommandoradsverktyg.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Fördelar med att köra gäst körbara i Service Fabric
Det finns flera fördelar med att köra gäst körbara i ett Service Fabric-kluster:

* Hög tillgänglighet. Program som körs i Service Fabric görs högtillgänglig. Service Fabric säkerställer att instanser av ett program körs.
* Övervakning av hälsotillstånd. Service Fabric-hälsoövervakning känner av om ett program körs och innehåller diagnostisk information om det uppstår ett fel.   
* Livscykeln för hantering. Förutom att tillhandahålla uppgraderingar utan avbrott, ger automatisk återställning till den tidigare versionen av Service Fabric om det finns en felaktig hälsohändelse som rapporteras vid en uppgradering.    
* Densitet. Du kan köra flera program i ett kluster, vilket eliminerar behovet av att varje program körs i sin egen maskinvara.
* Synlighet: Med hjälp av REST kan du anropa namngivning av Service Fabric-tjänsten för att hitta andra tjänster i klustret. 

## <a name="samples"></a>Exempel
* [Exempel för förpackning och distribution av en gäst körbar fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via Naming service med hjälp av REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Översikt över programmet och service manifest-filer
Som del av distributionen av en gäst körbar fil är det bra att förstå Service Fabric paketering och distribution av modellen som beskrivs i [programmodell](service-fabric-application-model.md). Service Fabric paketering modellen förlitar sig på två XML-filer: manifesten applikationen eller tjänsten. Schemadefinitionen för ApplicationManifest.xml och ServiceManifest.xml har installerats med Service Fabric-SDK i *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Applikationsmanifestet** programmanifestet används för att beskriva programmet. Den Listar de tjänster som ska utgöra den och andra parametrar som används för att definiera hur en eller flera tjänster ska distribueras, till exempel antal instanser.

  Ett program är en enhet av distribution och uppgradering i Service Fabric. Ett program kan uppgraderas som en enhet där potentiella problem och potentiella återställningar hanteras. Service Fabric garanterar att uppgraderingsprocessen är antingen lyckas, eller om uppgraderingen misslyckas, lämnar då inte programmet i ett okänt eller instabilt tillstånd.
* **Tjänstmanifestet** service manifest går igenom komponenterna av en tjänst. Den innehåller data, till exempel namn och typ av tjänst, och dess kod och konfiguration. Service manifest även vissa ytterligare parametrar som kan användas för att konfigurera tjänsten när den har distribuerats.

## <a name="application-package-file-structure"></a>Filstruktur för programmets paket
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

ApplicationPackageRoot innehåller ApplicationManifest.xml-fil som definierar programmet. En underkatalog för varje tjänst som ingår i programmet används för att innehålla alla artefakter som krävs för tjänsten. Dessa underkataloger är ServiceManifest.xml och vanligtvis följande:

* *Koden*. Den här katalogen innehåller koden.
* *Config*. Den här katalogen innehåller en Settings.xml (och andra filer vid behov) att tjänsten kan komma åt vid körning att hämta specifika konfigurationsinställningar.
* *Data*. Det här är en ytterligare katalog att lagra ytterligare lokala data som kan behövas för tjänsten. Data bör användas för att lagra endast tillfälliga data. Service Fabric Kopiera inte eller replikera ändringar till datakatalogen om tjänsten måste flyttas (till exempel under växling vid fel).

> [!NOTE]
> Du behöver skapa den `config` och `data` kataloger om du inte behöver dem.
>
>

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för relaterad information och uppgifter.
* [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa din första körbara gäst-program som använder Visual Studio](quickstart-guest-app.md)
* [Exempel för förpackning och distribution av en gäst körbar fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till förhandsutgåvan av verktyget paketering
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via Naming service med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)

