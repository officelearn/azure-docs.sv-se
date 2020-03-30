---
title: Hantera Azure Service Fabric-program med sfctl
description: Lär dig hur du distribuerar och tar bort program från ett Azure Service Fabric-kluster med hjälp av Azure Service Fabric CLI
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259076"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Hantera ett Azure Service Fabric-program med hjälp av Azure Service Fabric CLI (sfctl)

Lär dig hur du skapar och tar bort program som körs i ett Azure Service Fabric-kluster.

## <a name="prerequisites"></a>Krav

* Installera Service Fabric CLI. Välj sedan ditt Service Fabric-kluster. Mer information finns i [Komma igång med Service Fabric CLI](service-fabric-cli.md).

* Ha ett Service Fabric-programpaket redo att distribueras. Mer information om hur du skapar och paketerar ett program finns i [programmodellen Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Översikt

Så här distribuerar du ett nytt program:

1. Ladda upp ett programpaket till imagebutiken Service Fabric.
2. Etablera en programtyp.
3. Ta bort innehållet i bildarkivet.
4. Ange och skapa ett program.
5. Ange och skapa tjänster.

Så här tar du bort ett befintligt program:

1. Ta bort programmet.
2. Avetablera den associerade programtypen.

## <a name="deploy-a-new-application"></a>Distribuera ett nytt program

Så här distribuerar du ett nytt program:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Ladda upp ett nytt programpaket till bildarkivet

Innan du skapar ett program laddar du upp programpaketet till avbildningsarkivet Service Fabric.

Om ditt programpaket till exempel `app_package_dir` finns i katalogen använder du följande kommandon för att ladda upp katalogen:

```shell
sfctl application upload --path ~/app_package_dir
```

För stora programpaket kan du `--show-progress` ange alternativet för att visa förloppet för överföringen.

### <a name="provision-the-application-type"></a>Etablera programtypen

När överföringen är klar etablerar du programmet. Använd följande kommando om du vill etablera programmet:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

Värdet för `application-type-build-path` är namnet på katalogen där du laddade upp ditt programpaket.

### <a name="delete-the-application-package"></a>Ta bort programpaketet

Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Om du tar bort programpaket från avbildningsarkivet frigörs systemresurser.  Om du behåller oanvända programpaket förbrukar disklagring och leder till problem med programmets prestanda. 

Om du vill ta bort programpaketet från bildarkivet använder du följande kommando:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`måste vara namnet på den katalog som du laddade upp när du skapade programmet.

### <a name="create-an-application-from-an-application-type"></a>Skapa ett program från en programtyp

När du har etablerat programmet använder du följande kommando för att namnge och skapa ditt program:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`är det namn som du vill använda för programinstansen. Du kan hämta ytterligare parametrar från det tidigare etablerade programmanifestet.

Programnamnet måste börja med `fabric:/`prefixet .

### <a name="create-services-for-the-new-application"></a>Skapa tjänster för det nya programmet

När du har skapat ett program skapar du tjänster från programmet. I följande exempel skapar vi en ny tillståndslös tjänst från vårt program. De tjänster som du kan skapa från ett program definieras i ett tjänstmanifest i det tidigare etablerade programpaketet.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verifiera programdistribution och hälsa

Om du vill kontrollera att allt är felfritt använder du följande hälsokommandon:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Om du vill kontrollera att tjänsten är felfri använder du liknande kommandon för att hämta hälsotillståndet för både tjänsten och programmet:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Hälsosamma tjänster och `HealthState` program `Ok`har ett värde av .

## <a name="remove-an-existing-application"></a>Ta bort ett befintligt program

Så här tar du bort ett program:

### <a name="delete-the-application"></a>Ta bort programmet

Om du vill ta bort programmet använder du följande kommando:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Avetablera programtypen

När du har tagit bort programmet kan du avetablera programtypen om du inte längre behöver det. Om du vill ta av dig programtypen använder du följande kommando:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

Typnamn och typversion måste matcha namn och version i det tidigare etablerade programmanifestet.

## <a name="upgrade-application"></a>Uppgradera program

När du har skapat programmet kan du upprepa samma uppsättning steg för att etablera en andra version av programmet. Sedan, med en Service Fabric-programuppgradering kan du övergå till att köra den andra versionen av programmet. Mer information finns i dokumentationen om uppgraderingar av [Service Fabric-programmet](service-fabric-application-upgrade.md).

Om du vill utföra en uppgradering etablerar du först nästa version av programmet med samma kommandon som tidigare:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Vi rekommenderar då att du utför en övervakad automatisk uppgradering, startar uppgraderingen genom att köra följande kommando:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Uppgraderingar åsidosätter befintliga parametrar med den uppsättning som anges. Programparametrar bör skickas som argument till uppgraderingskommandot om det behövs. Programparametrar ska kodas som ett JSON-objekt.

Om du vill hämta de parametrar `sfctl application info` som tidigare angetts kan du använda kommandot.

När en programuppgradering pågår kan statusen `sfctl application upgrade-status` hämtas med kommandot.

Slutligen, om en uppgradering pågår och måste avbrytas, `sfctl application upgrade-rollback` kan du använda för att återställa uppgraderingen.

## <a name="next-steps"></a>Nästa steg

* [Grunderna i Service Fabric CLI](service-fabric-cli.md)
* [Komma igång med Service Fabric på Linux](service-fabric-get-started-linux.md)
* [Starta en uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)
