---
title: Självstudiekurs - Schemalägga en ACR-uppgift
description: I den här självstudien kan du läsa om hur du kör en Azure Container Registry Task enligt ett definierat schema genom att ange en eller flera timerutlösare
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402878"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Köra en ACR-aktivitet enligt ett definierat schema

Den här självstudien visar hur du kör en [ACR-aktivitet](container-registry-tasks-overview.md) enligt ett schema. Schemalägg en aktivitet genom att ställa in en eller flera *timerutlösare*. Timerutlösare kan användas ensamt eller i kombination med andra aktivitetsutlösare.

I den här självstudien kan du läsa mer om schemaläggning av uppgifter och:

> [!div class="checklist"]
> * Skapa en uppgift med en timerutlösare
> * Hantera timerutlösare

Schemaläggning av en aktivitet är användbart för scenarier som följande:

* Kör en behållararbetsbelastning för schemalagda underhållsåtgärder. Kör till exempel en behållare app för att ta bort onödiga bilder från registret.
* Kör en uppsättning tester på en produktionsavbildning under arbetsdagen som en del av din övervakning på live-plats.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda den lokalt krävs version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Om att schemalägga en aktivitet

* **Utlösare med cron-uttryck** - Timer-utlösaren för en aktivitet använder ett *cron-uttryck*. Uttrycket är en sträng med fem fält som anger minut, timme, dag, månad och veckodag för att utlösa aktiviteten. Frekvenser på upp till en gång per minut stöds.

  Uttrycket `"0 12 * * Mon-Fri"` utlöser till exempel en aktivitet vid lunchtid UTC på varje veckodag. Se [mer information](#cron-expressions) senare i den här artikeln.
* **Flera timerutlösare** - Det är tillåtet att lägga till flera timers till en aktivitet, så länge schemana skiljer sig åt.
    * Ange flera timerutlösare när du skapar aktiviteten eller lägg till dem senare.
    * Du kan också namnge utlösare för enklare hantering, eller acr-uppgifter ger standardutlösningsnamn.
    * Om timerscheman överlappar varandra i taget utlöser ACR-aktiviteter aktiviteten vid den schemalagda tiden för varje timer.
* **Andra aktivitetsutlösare** - I en timerutlöst uppgift kan du också aktivera utlösare baserat på [källkodskonset eller](container-registry-tutorial-build-task.md) [basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md). Precis som andra ACR-aktiviteter kan du också utlösa en schemalagd aktivitet [manuellt.][az-acr-task-run]

## <a name="create-a-task-with-a-timer-trigger"></a>Skapa en uppgift med en timerutlösare

När du skapar en uppgift med kommandot [az acr-aktivitetsskapande][az-acr-task-create] kan du också lägga till en timerutlösare. Lägg `--schedule` till parametern och skicka ett cron-uttryck för timern.

Som ett enkelt exempel utlöser följande `hello-world` kommando att köra avbildningen från Docker Hub varje dag klockan 21:00 UTC. Aktiviteten körs utan en källkodskontext.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Kör kommandot [az acr task show][az-acr-task-show] för att se att timerutlösaren är konfigurerad. Som standard är utlösaren för basavbildningsuppdatering också aktiverad.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Utlösa aktiviteten manuellt med [az acr-aktivitetskörning][az-acr-task-run] för att säkerställa att den är korrekt konfigurerad:

```azurecli
az acr task run --name mytask --registry myregistry
```

Om behållaren körs korrekt liknar utdata följande:

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Efter den schemalagda tiden kör du kommandot [az acr task list-runs][az-acr-task-list-runs] för att kontrollera att timern utlöste aktiviteten som förväntat:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

När timern lyckas liknar utdata följande:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Hantera timerutlösare

Använd kommandona [för a-a-aktivitets timer][az-acr-task-timer] för att hantera timerutlösare för en ACR-uppgift.

### <a name="add-or-update-a-timer-trigger"></a>Lägga till eller uppdatera en timerutlösare

När en uppgift har skapats kan du lägga till en timerutlösare med kommandot [az acr task timer add.][az-acr-task-timer-add] I följande exempel läggs en timer utlösande faktor *till timer2* *i mytask* som skapats tidigare. Den här timern utlöser aktiviteten varje dag klockan 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Uppdatera schemat för en befintlig utlösare, eller ändra dess status, med hjälp av kommandot [az acr task timer update.][az-acr-task-timer-update] Uppdatera till exempel utlösaren med namnet *timer2* för att utlösa aktiviteten vid 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Utlösare av listtidsutlösare

Kommandot [az acr-aktivitetstidslista][az-acr-task-timer-list] visar de timerutlösare som ställts in för en aktivitet:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Exempel på utdata:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Ta bort en timerutlösare

Använd kommandot [az acr task timer remove][az-acr-task-timer-remove] för att ta bort en timerutlösare från en aktivitet. I följande exempel tas *timer2-utlösaren* bort från *mytask:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-uttryck

ACR-uppgifter använder [NCronTab-biblioteket](https://github.com/atifaziz/NCrontab) för att tolka cron-uttryck. Uttryck som stöds i ACR-uppgifter har fem obligatoriska fält avgränsade med blanksteg:

`{minute} {hour} {day} {month} {day-of-week}`

Tidszonen som används med cron-uttrycken är Coordinated Universal Time (UTC). Timmar är i 24-timmarsformat.

> [!NOTE]
> ACR-uppgifter stöder `{second}` inte `{year}` fältet eller i cron-uttryck. Om du kopierar ett cron-uttryck som används i ett annat system måste du ta bort dessa fält om de används.

Varje fält kan ha någon av följande typer av värden:

|Typ  |Exempel  |När utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>`"5 * * * *"`</nobr>|varje timme vid 5 minuter efter timmen|
|Alla värden`*`( )|<nobr>`"* 5 * * *"`</nobr>|varje minut av timmen börjar 5:00 UTC (60 gånger om dagen)|
|Ett intervall`-` (operatör)|<nobr>`"0 1-3 * * *"`</nobr>|3 gånger per dag, vid 1:00, 2:00 och 3:00 UTC|
|En uppsättning värden`,` (operator)|<nobr>`"20,30,40 * * * *"`</nobr>|3 gånger per timme, 20 minuter, 30 minuter och 40 minuter efter timmen|
|Ett intervallvärde`/` (operator)|<nobr>`"*/10 * * * *"`</nobr>|6 gånger per timme, på 10 minuter, 20 minuter, och så vidare, förbi timmen

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron exempel

|Exempel|När utlöses  |
|---------|---------|
|`"*/5 * * * *"`|en gång var femte minut|
|`"0 * * * *"`|en gång högst upp i varje timme|
|`"0 */2 * * *"`|en gång varannan timme|
|`"0 9-17 * * *"`|en gång i timmen från 9:00 till 17:00 UTC|
|`"30 9 * * *"`|vid 9:30 UTC varje dag|
|`"30 9 * * 1-5"`|på 9:30 UTC varje vardag|
|`"30 9 * Jan Mon"`|på 9:30 UTC varje måndag i januari|

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort alla resurser som du har skapat i den här självstudieserien, inklusive behållarregistret eller behållarregistret, behållarinstansen, nyckelvalvet och tjänstens huvudnamn:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar Azure Container Registry-uppgifter som automatiskt utlöses av en timer. 

Ett exempel på hur du använder en schemalagd aktivitet för att rensa databaser i ett register finns i [Rensa avbildningar automatiskt från ett Azure-behållarregister](container-registry-auto-purge.md).

Exempel på uppgifter som utlöses av källkodsanageringar eller basavbildningsuppdateringar finns i andra artiklar i [självstudieserien ACR-uppgifter](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
