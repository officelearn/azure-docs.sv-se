---
title: "Hantera Azure Service Fabric-program med hjälp av Azure Service Fabric CLI"
description: "Lär dig hur du distribuerar och ta bort program från ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric CLI"
services: service-fabric
author: Christina-Kang
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 08/22/2017
ms.author: bikang
ms.openlocfilehash: 2d330921b66a1c261298601dad46f00f66659037
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli"></a>Hantera ett Azure Service Fabric-program med hjälp av Azure Service Fabric CLI

Lär dig mer om att skapa och ta bort program som körs i ett Azure Service Fabric-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Installera Service Fabric CLI. Välj sedan Service Fabric-klustret. Mer information finns i [komma igång med Service Fabric CLI](service-fabric-cli.md).

* Har ett Service Fabric-programpaket klar att distribueras. Mer information om hur du författare och paketet ett program Läs mer om den [Service Fabric programmodell](service-fabric-application-model.md).

## <a name="overview"></a>Översikt

Följ stegen för att distribuera ett nytt program:

1. Överför ett programpaket till Service Fabric image store.
2. Etablera en typ av program.
3. Ta bort image store-innehåll.
4. Ange och skapa ett program.
5. Ange och skapa tjänster.

Ta bort ett befintligt program genom att utföra följande steg:

1. Ta bort programmet.
2. Avetablera typen associerade program.

## <a name="deploy-a-new-application"></a>Distribuera ett nytt program

För att distribuera ett nytt program måste du utföra följande uppgifter:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Ladda upp ett nytt programpaket till image store

Ladda upp programpaketet till Service Fabric image store innan du skapar ett program.

Om ditt programpaket är i till exempel den `app_package_dir` directory, använda följande kommandon för att ladda upp katalogen:

```azurecli
sfctl application upload --path ~/app_package_dir
```

För stora programpaket kan du ange den `--show-progress` alternativet för att visa förloppet för överföringen.

### <a name="provision-the-application-type"></a>Etablera programtypen

När överföringen är klar kan du etablera programmet. Om du vill distribuera programmet, använder du följande kommando:

```azurecli
sfctl application provision --application-type-build-path app_package_dir
```

Värdet för `application-type-build-path` är namnet på den katalog där du laddade upp ditt programpaket.

### <a name="delete-the-application-package"></a>Ta bort programpaketet

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med prestanda. 

Om du vill ta bort programmet paketet från avbildningsarkivet, använder du följande kommando:

```azurecli
sfctl store delete --content-path app_package_dir
```

`content-path` måste vara namnet på den katalog som du överfört när du skapade programmet.

### <a name="create-an-application-from-an-application-type"></a>Skapa ett program från en typ av program

När du distribuera programmet, använder du följande kommando att namnge och skapa programmet:

```azurecli
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` är det namn som du vill använda för programinstansen. Du kan få ytterligare parametrar från tidigare etablerade programmanifestet.

Programnamnet måste börja med prefixet `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Skapa tjänster för det nya programmet

När du har skapat ett program kan du skapa tjänster från programmet. I följande exempel skapar vi tillståndslösa tjänsten från våra program. De tjänster som du kan skapa från ett program som har definierats i en tjänstmanifestet i det tidigare etablerade programpaketet.

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

Verifiera att tjänsten är felfritt, att använda liknande kommandon för att hämta hälsotillståndet för både tjänsten och programmet:

```azurecli
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Felfri tjänster och program har en `HealthState` värdet för `Ok`.

## <a name="remove-an-existing-application"></a>Ta bort ett befintligt program

Om du vill ta bort ett program måste du utföra följande uppgifter:

### <a name="delete-the-application"></a>Ta bort programmet

Om du vill ta bort programmet, använder du följande kommando:

```azurecli
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Avetablera programtypen

När du har tagit bort programmet avetablera du programtypen om du inte längre behöver. Om du vill avetablera programtypen, använder du följande kommando:

```azurecli
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Typnamn och Typversion måste matcha namnet och versionen i tidigare etablerade programmanifestet.

## <a name="upgrade-application"></a>Uppgradera program

Du kan upprepa samma uppsättning steg för att etablera en andra versioner av programmet när du har skapat ditt program. Sedan kan du övergå till den andra versionen av programmet som körs med en uppgradering av Service Fabric-programmet. Mer information finns i dokumentationen på [Service Fabric programuppgraderingar](service-fabric-application-upgrade.md).

Om du vill utföra en uppgradering att etablera nästa version av programmet som använder samma kommandon som tidigare:

```azurecli
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Det rekommenderas sedan att utföra en övervakade automatisk uppgradering, starta uppgraderingen genom att köra följande kommando:

```azurecli
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uppgraderingar åsidosätta befintliga parametrar med oavsett set anges. Programmet parametrar ska skickas som argument för kommandot uppgradera om det behövs. Parametrar för programmet ska vara kodad som ett JSON-objekt.

Om du vill hämta alla tidigare angivna parametrar du kan använda den `sfctl application info` kommando.

När en uppgradering av programmet pågår status kan hämtas med hjälp av den `sfctl application upgrade-status` kommando.

Slutligen, om en uppgradering är pågående och behöver avbrytas, kan du använda den `sfctl application upgrade-rollback` att återställa uppgraderingen.

## <a name="next-steps"></a>Nästa steg

* [Grunderna i Service Fabric CLI](service-fabric-cli.md)
* [Komma igång med Service Fabric på Linux](service-fabric-get-started-linux.md)
* [Starta en uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)
