---
title: Paketera en befintlig körbar fil till Azure Service Fabric
description: Lär dig mer om att paketera ett befintligt program som en körbar gäst fil, så att det kan distribueras till ett Service Fabric kluster.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3d7aab28a32effa2caf7b04b830d72e5e3dfda56
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457828"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Distribuera en befintlig körbar fil till Service Fabric
Du kan köra vilken typ av kod som helst, till exempel Node. js, Java C++ eller i Azure Service Fabric som en tjänst. Service Fabric avser de här typerna av tjänster som körbara gäst program.

Körbara gäst program behandlas av Service Fabric som tillstånds lösa tjänster. Det innebär att de placeras på noder i ett kluster, baserat på tillgänglighet och andra mått. Den här artikeln beskriver hur du paketerar och distribuerar en körbar gäst fil till ett Service Fabric kluster med hjälp av Visual Studio eller ett kommando rads verktyg.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Fördelar med att köra en körbar gäst fil i Service Fabric
Det finns flera fördelar med att köra en körbar gäst fil i ett Service Fabric kluster:

* Hög tillgänglighet. Program som körs i Service Fabric görs med hög tillgänglighet. Service Fabric garanterar att instanser av ett program körs.
* Hälso övervakning. Service Fabric hälso övervakning identifierar om ett program körs och innehåller diagnostikinformation om det uppstår ett fel.   
* Hantering av program livs cykel. Förutom att tillhandahålla uppgraderingar utan avbrott ger Service Fabric automatisk återställning till den tidigare versionen om det finns en skadad hälso händelse som rapporteras under en uppgradering.    
* Innebär. Du kan köra flera program i ett kluster, vilket eliminerar behovet av att varje program ska köras på sin egen maskin vara.
* Identifierings möjlighet: med hjälp av REST kan du anropa Service Fabric Naming Service för att hitta andra tjänster i klustret. 

## <a name="samples"></a>Exempel
* [Exempel på paketering och distribution av en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exempel på två körbara gäst programC# (och NodeJS) som kommunicerar via namngivnings tjänsten med rest](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Översikt över filer för program-och tjänst manifest
Som en del av distributionen av en körbar gäst är det bra att förstå Service Fabric förpackning och distributions modell enligt beskrivningen i [program modellen](service-fabric-application-model.md). Service Fabric-förpacknings modellen förlitar sig på två XML-filer: program-och tjänst manifest. Schema definitionen för ApplicationManifest. xml-och ServiceManifest. XML-filerna installeras med Service Fabric SDK i *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Program manifest** Applikations manifestet används för att beskriva programmet. Den listar de tjänster som utgör den och andra parametrar som används för att definiera hur en eller flera tjänster ska distribueras, till exempel antalet instanser.

  I Service Fabric är ett program en enhet för distribution och uppgradering. Ett program kan uppgraderas som en enda enhet där eventuella problem och eventuella återställningar hanteras. Service Fabric garanterar att uppgraderings processen lyckades, eller om uppgraderingen Miss lyckas, lämna inte programmet i ett okänt eller instabilt tillstånd.
* **Tjänst manifest** Tjänst manifestet beskriver komponenterna i en tjänst. Den innehåller data, till exempel namn och typ av tjänst, samt dess kod och konfiguration. Tjänst manifestet innehåller också några ytterligare parametrar som kan användas för att konfigurera tjänsten när den har distribuerats.

## <a name="application-package-file-structure"></a>Fil struktur för programpaket
För att distribuera ett program till Service Fabric ska programmet följa en fördefinierad katalog struktur. Följande är ett exempel på strukturen.

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

ApplicationPackageRoot innehåller den ApplicationManifest. XML-fil som definierar programmet. En under katalog för varje tjänst som ingår i programmet används för att innehålla alla artefakter som krävs av tjänsten. Dessa under kataloger är ServiceManifest. xml och, vanligt vis följande:

* *Kod*. Den här katalogen innehåller Service koden.
* *Config*. Den här katalogen innehåller en Settings. XML-fil (och andra filer om det behövs) som tjänsten kan komma åt vid körning för att hämta vissa konfigurations inställningar.
* *Data*. Detta är ytterligare en katalog där du kan lagra ytterligare lokala data som tjänsten kan behöva. Data ska endast användas för att lagra tillfälliga data. Service Fabric kopierar eller replikerar inte ändringar i data katalogen om tjänsten måste flyttas (till exempel under redundans).

> [!NOTE]
> Du behöver inte skapa `config` och `data` kataloger om du inte behöver dem.
>
>

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för relaterad information och uppgifter.
* [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa ditt första körbara gäst program med Visual Studio](quickstart-guest-app.md)
* [Exempel på paketering och distribution av en körbar gäst fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till för hands versionen av packnings verktyget
* [Exempel på två körbara gäst programC# (och NodeJS) som kommunicerar via namngivnings tjänsten med rest](https://github.com/Azure-Samples/service-fabric-containers)

