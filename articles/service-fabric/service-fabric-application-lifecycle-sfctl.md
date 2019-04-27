---
title: Hantera Azure Service Fabric-program med Azure Service Fabric CLI (sfctl)
description: Lär dig hur du distribuerar och ta bort program från ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric CLI
services: service-fabric
author: rockboyfor
manager: digimobile
ms.service: service-fabric
ms.topic: conceptual
origin.date: 07/31/2018
ms.date: 09/10/2018
ms.author: v-yeche
ms.openlocfilehash: 9b0f785a6a43f984708645084a8a8036326d3d24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621385"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Hantera ett Azure Service Fabric-program med hjälp av Azure Service Fabric CLI (sfctl)

Lär dig mer om att skapa och ta bort program som körs i ett Azure Service Fabric-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera Service Fabric CLI. Välj Service Fabric-klustret. Mer information finns i [Kom igång med Service Fabric CLI](service-fabric-cli.md).

* Ha ett Service Fabric-programpaket redo att distribueras. Mer information om hur du författare och paketet ett program, Läs om de [Service Fabric-programmodellen](service-fabric-application-model.md).

## <a name="overview"></a>Översikt

Om du vill distribuera ett nytt program, gör du följande:

1. Överför ett programpaket till Service Fabric-avbildningsarkiv.
2. Etablera en typ av program.
3. Ta bort innehållet i store.
4. Ange och skapa ett program.
5. Ange och skapa tjänster.

Ta bort ett befintligt program genom att utföra följande steg:

1. Ta bort programmet.
2. Avetablera associerade programtypen.

## <a name="deploy-a-new-application"></a>Distribuera ett nytt program

För att distribuera ett nytt program måste du utföra följande åtgärder:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Ladda upp ett nytt programpaket till avbildningsarkivet

Innan du skapar ett program måste du ladda upp programpaketet till Service Fabric-avbildningsarkiv.

Exempel: om dina programpaket finns i den `app_package_dir` directory, Använd följande kommandon för att ladda upp katalogen:

```azurecli
sfctl application upload --path ~/app_package_dir
```

För stora programpaket kan du ange den `--show-progress` alternativet för att visa förloppet för överföringen.

### <a name="provision-the-application-type"></a>Etablera programtypen

När överföringen är klar kan du etablera programmet. Om du vill distribuera programmet, använder du följande kommando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Värdet för `application-type-build-path` är namnet på den katalog där du laddade upp programpaketet.

### <a name="delete-the-application-package"></a>Ta bort programpaketet

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med programprestanda. 

Om du vill ta bort programpaketet från avbildningsarkivet, använder du följande kommando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` måste vara namnet på den katalog som du laddade upp när du skapade programmet.

### <a name="create-an-application-from-an-application-type"></a>Skapa ett program från en programtyp

När du etablerar programmet använder du följande kommando för att namnge och skapa ditt program:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` är det namn som du vill använda för instansen för programmet. Du kan få ytterligare parametrar från tidigare etablerade programmanifestet.

Programnamnet måste inledas med prefixet `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Skapa tjänster för det nya programmet

När du har skapat ett program kan du skapa tjänster från programmet. I följande exempel skapar vi en ny tillståndslös tjänst från vårt program. De tjänster som du kan skapa från ett program som har definierats i ett tjänstmanifest i tidigare etablerade programpaketet.

```azurecli
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Kontrollera programdistribution och hälsa

Om du vill kontrollera att allt är felfri, använder du följande kommandon för health:

```azurecli
sfctl application list
sfctl service list --application-id TestApp
```

Kontrollera att tjänsten är felfri genom att använda liknande kommandon för att hämta hälsotillståndet för både service och programmet:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Felfria tjänster och program har en `HealthState` värdet för `Ok`.

## <a name="remove-an-existing-application"></a>Ta bort ett befintligt program

Ta bort ett program genom att utföra följande åtgärder:

### <a name="delete-the-application"></a>Ta bort programmet

Om du vill ta bort programmet, använder du följande kommando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Avetablera programtyp

När du har tagit bort programmet kan avetablera du programtyp om du inte längre behöver den. Om du vill avetablera programtyp, använder du följande kommando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Skriv ett namn och version av programtyp måste matcha namnet och versionen i tidigare etablerade applikationsmanifestet.

## <a name="upgrade-application"></a>Uppgradera program

När du har skapat ditt program, kan du upprepa samma uppsättning steg för att etablera en andra versionen av ditt program. Sedan kan du övergå till körs den andra versionen av programmet med en uppgradering av Service Fabric-programmet. Mer information finns i dokumentationen på [Service Fabric programuppgraderingar](service-fabric-application-upgrade.md).

Om du vill utföra en uppgradering måste du först etablera nästa version av programmet med hjälp av samma kommandon som tidigare:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Vi rekommenderar sedan att utföra en automatisk uppgradering av övervakade, starta uppgraderingen genom att köra följande kommando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uppgraderingar åsidosätta befintliga parametrar med set anges. Programparametrar ska skickas som argument till kommandot uppgradera om det behövs. Programparametrar ska kodas som en JSON-objekt.

Om du vill hämta alla tidigare angivna parametrar du kan använda den `sfctl application info` kommando.

När en uppgradering av programmet pågår, status kan hämtas med hjälp av den `sfctl application upgrade-status` kommando.

Slutligen, om en uppgradering är pågående och måste avbrytas, kan du använda den `sfctl application upgrade-rollback` att återställa uppgraderingen.

## <a name="next-steps"></a>Nästa steg

* [Grunderna i Service Fabric CLI](service-fabric-cli.md)
* [Komma igång med Service Fabric i Linux](service-fabric-get-started-linux.md)
* [Starta en Service Fabric-Programuppgradering](service-fabric-application-upgrade.md)

<!--Update_Description: update meta properties -->