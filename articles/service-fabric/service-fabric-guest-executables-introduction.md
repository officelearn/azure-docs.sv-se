---
title: Paketera en befintlig körbar till Azure Service Fabric
description: Lär dig mer om hur du paketerar ett befintligt program som en körbar gäst, så att det kan distribueras till ett Service Fabric-kluster.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457828"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Distribuera en befintlig körbar till Service Fabric
Du kan köra alla typer av kod, till exempel Node.js, Java eller C++ i Azure Service Fabric som en tjänst. Service Fabric avser dessa typer av tjänster som gäst körbara filer.

Gästblanter behandlas av Service Fabric som tillståndslösa tjänster. Därför placeras de på noder i ett kluster, baserat på tillgänglighet och andra mått. I den här artikeln beskrivs hur du paketerar och distribuerar en gäst körbar till ett Service Fabric-kluster med hjälp av Visual Studio eller ett kommandoradsverktyg.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Fördelar med att köra en gäst körbar i Service Fabric
Det finns flera fördelar med att köra en gäst körbar i ett Service Fabric-kluster:

* Hög tillgänglighet. Program som körs i Service Fabric görs mycket tillgängliga. Service Fabric säkerställer att instanser av ett program körs.
* Hälsoövervakning. Hälsoövervakning av tjänst fabric identifierar om ett program körs och tillhandahåller diagnostikinformation om det finns ett fel.   
* Hantering av programmets livscykel. Förutom att tillhandahålla uppgraderingar utan driftstopp, ger Service Fabric automatisk återställning till den tidigare versionen om det finns en dålig hälsohändelse som rapporterats under en uppgradering.    
* Densitet. Du kan köra flera program i ett kluster, vilket eliminerar behovet för varje program att köras på sin egen maskinvara.
* Upptäckbarhet: Med REST kan du ringa tjänsten Service Fabric Naming för att hitta andra tjänster i klustret. 

## <a name="samples"></a>Exempel
* [Prov för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gästar (C# och nodejs) som kommunicerar via namngivningstjänsten med REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Översikt över program- och tjänstmanifestfiler
Som en del av distributionen av en körbar gäst är det användbart att förstå paketerings- och distributionsmodellen för Service Fabric enligt beskrivningen i [programmodellen](service-fabric-application-model.md). Paketeringsmodellen Service Fabric är beroende av två XML-filer: program- och tjänstmanifesten. Schemadefinitionen för filerna ApplicationManifest.xml och ServiceManifest.xml installeras med Service Fabric SDK i *C:\Program Files\Microsoft SDK\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Ansökan manifest** Programmanifestet används för att beskriva programmet. Den listar de tjänster som utgör den och andra parametrar som används för att definiera hur en eller flera tjänster ska distribueras, till exempel antalet instanser.

  I Service Fabric är ett program en enhet för distribution och uppgradering. Ett program kan uppgraderas som en enda enhet där potentiella fel och potentiella återställningar hanteras. Service Fabric garanterar att uppgraderingsprocessen antingen lyckas eller, om uppgraderingen misslyckas, inte lämnar programmet i ett okänt eller instabilt tillstånd.
* **Servicemanifest** Tjänstmanifestet beskriver komponenterna i en tjänst. Den innehåller data, till exempel namn och typ av tjänst, och dess kod och konfiguration. Tjänstmanifestet innehåller också några ytterligare parametrar som kan användas för att konfigurera tjänsten när den har distribuerats.

## <a name="application-package-file-structure"></a>Filstruktur för programpaket
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

ApplicationPackageRoot innehåller filen ApplicationManifest.xml som definierar programmet. En underkatalog för varje tjänst som ingår i programmet används för att innehålla alla artefakter som tjänsten kräver. Dessa underkataloger är ServiceManifest.xml och, vanligtvis, följande:

* *Kod*. Den här katalogen innehåller servicekoden.
* *Config*. Den här katalogen innehåller en Settings.xml-fil (och andra filer om det behövs) som tjänsten kan komma åt vid körning för att hämta specifika konfigurationsinställningar.
* *Data*. Detta är ytterligare en katalog för att lagra ytterligare lokala data som tjänsten kan behöva. Data bör endast användas för att lagra efemära data. Service Fabric kopierar eller replikerar inte ändringar i datakatalogen om tjänsten måste flyttas (till exempel under redundans).

> [!NOTE]
> Du behöver inte skapa `config` och `data` kataloger om du inte behöver dem.
>
>

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för relaterad information och uppgifter.
* [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa ditt första körbara gästprogram med Visual Studio](quickstart-guest-app.md)
* [Prov för att paketera och distribuera en körbar gäst,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)inklusive en länk till föruteasen av förpackningsverktyget
* [Exempel på två körbara gästar (C# och nodejs) som kommunicerar via namngivningstjänsten med REST](https://github.com/Azure-Samples/service-fabric-containers)

