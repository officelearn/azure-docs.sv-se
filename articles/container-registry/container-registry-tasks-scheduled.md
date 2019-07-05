---
title: Schemalägga aktiviteter i Azure Container Registry
description: Ange timers för att köra ett Azure Container Registry-aktivitet på ett definierat schema.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509708"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Kör en ACR-uppgift på ett definierat schema

Den här artikeln visar hur du kör en [ACR uppgift](container-registry-tasks-overview.md) enligt ett schema. Schemalägga en aktivitet genom att ställa in en eller flera *timerutlösare*. 

Schemalägga en aktivitet är användbart för scenarier som följande:

* Kör en behållararbetsbelastning för schemalagt underhåll. Till exempel en behållarbaserad app ta bort onödiga filer från ditt register.
* Köra en uppsättning med tester på en avbildning av produktion under arbetsdagen som en del av övervakningen live-webbplatser.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda den lokalt, version 2.0.68 eller senare krävs. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Om att schemalägga en aktivitet

* **Utlösare med cron-uttryck** -timerutlösare för en aktivitet använder en *cron-uttryck*. Uttrycket är en sträng med fem fält att ange minut, timme, dag, månad och dag i veckan för utlösning av aktiviteten. Frekvenser på upp till en gång per minut stöds. 

  Exempel: uttrycket `"0 12 * * Mon-Fri"` utlöser en aktivitet kl. tolv UTC på varje veckodag. Se [information](#cron-expressions) senare i den här artikeln.
* **Flera timerutlösare** – att lägga till flera timers i en uppgift tillåts, så länge scheman skiljer sig åt. 
    * Ange flera timerutlösare när du skapar uppgiften eller lägga till dem senare.
    * Namn alternativt utlösarna för enklare hantering eller ACR uppgifter ger standardnamnen för utlösaren.
    * Om timern scheman överlappar i taget utlöser ACR uppgifter aktiviteten på den schemalagda tiden för varje timer. 
* **Andra aktivitetsutlösare** – i en timerutlöst uppgift, kan du också aktivera utlösare utifrån [källa kodgenomförande](container-registry-tutorial-build-task.md) eller [basera uppdateringar av](container-registry-tutorial-base-image-update.md). Som andra ACR-uppgifter du kan också [manuellt utlösa][az-acr-task-run] en schemalagd aktivitet.

## <a name="create-a-task-with-a-timer-trigger"></a>Skapa en uppgift med en timer som utlösare

När du skapar en uppgift med den [az acr uppgift skapa][az-acr-task-create] kommandot, du kan du lägga till en timer som utlösare. Lägg till den `--schedule` parametern och pass ett cron-uttryck för timern. 

Som ett enkelt exempel kan följande kommando utlösare som körs på `hello-world` avbildning från Docker Hub varje dag vid 21:00 UTC. Aktiviteten körs utan en kontext för käll-kod.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Kör den [az acr uppgift show][az-acr-task-show] kommando för att se att den timer som utlösaren har konfigurerats. Som standard aktiveras även basavbildningen update-utlösare.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Utlösa uppgiften manuellt med [az acr-uppgiftskörning][az-acr-task-run] så att den är korrekt konfigurerad:

```azurecli
az acr task run --name mytask --registry myregistry
```

Om behållaren körs utan problem, ser utdata ut ungefär så här:

```console
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

Efter den schemalagda tiden kör den [az acr list-körs][az-acr-task-list-runs] kommando för att kontrollera att timern utlöst uppgiften som förväntat:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

När timern lyckas ser utdata ut ungefär så här:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Hantera timerutlösare

Använd den [az acr uppgiftstimer][az-acr-task-timer] kommandon för att hantera timerutlösare för en ACR-aktivitet.

### <a name="add-or-update-a-timer-trigger"></a>Lägg till eller uppdatera en timer som utlösare

När en uppgift har skapats kan du lägga till en timer som utlösare med hjälp av den [az acr uppgiftstimer Lägg till][az-acr-task-timer-add] kommando. I följande exempel läggs en timer Utlösarnamn *timer2* till *mytask* skapade tidigare. Den här timern utlöses uppgiften varje dag kl 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Uppdatera schemat för en befintlig utlösare eller ändra dess status genom att använda den [az acr-aktivitetsuppdatering timer][az-acr-task-timer-update] kommando. Till exempel uppdatera utlösare med namnet *timer2* att utlösa aktiviteten på 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista timerutlösare

Den [az acr uppgiftslista timer][az-acr-task-timer-list] -kommando visar timerutlösare som ställts in för en aktivitet:

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

### <a name="remove-a-timer-trigger"></a>Ta bort en timer som utlösare 

Använd den [az acr uppgift timer remove][az-acr-task-timer-remove] kommando för att ta bort en timer som utlösare från en aktivitet. I följande exempel tar bort den *timer2* utlöses från *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron-uttryck

ACR uppgifter använder den [NCronTab](https://github.com/atifaziz/NCrontab) bibliotek som ska tolka cron-uttryck. Stöds uttryck i ACR uppgifter har fem obligatoriska fält avgränsade med blanksteg:

`{minute} {hour} {day} {month} {day-of-week}`

Tidszonen som används med cron-uttryck är Coordinated Universal Time (UTC). Timmar finns i 24-timmarsformat.

> [!NOTE]
> ACR uppgifter stöder inte den `{second}` eller `{year}` i cron-uttryck. Om du kopierar ett cron-uttryck som används i ett annat system, måste du ta bort dessa fält, om de används.

Varje fält kan ha något av följande typer av värden:

|Type  |Exempel  |När det utlöses  |
|---------|---------|---------|
|Ett specifikt värde |<nobr>"5 * * * *"</nobr>|varje timme på 5 minuter efter timmen|
|Alla värden (`*`)|<nobr>"* 5 * * *"</nobr>|Varje minut av timme början 5:00 UTC (60 gånger per dag)|
|Ett intervall (`-` operatorn)|<nobr>"0 1-3 * * *"</nobr>|3 gånger per dag, 1:00, 2:00 och 3:00 UTC|  
|En uppsättning värden (`,` operatorn)|<nobr>"20,30,40 * * * *"</nobr>|3 gånger per timme, 20 minuter, 30 minuter och 40 minuter efter timmen|
|Ett intervallvärde (`/` operatorn)|<nobr>"*/10 * * * *"</nobr>|6 gånger per timme, på 10 minuter, 20 minuter och så vidare, efter timmen

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron-exempel

|Exempel|När det utlöses  |
|---------|---------|
|`"*/5 * * * *"`|en gång var femte minut|
|`"0 * * * *"`|en gång högst upp på varje timme|
|`"0 */2 * * *"`|en gång varannan timme|
|`"0 9-17 * * *"`|en gång i timmen från 9:00 till 17:00 UTC|
|`"30 9 * * *"`|på 9:30 UTC varje dag|
|`"30 9 * * 1-5"`|på 9:30 UTC varje veckodag|
|`"30 9 * Jan Mon"`|på 9:30 UTC varje måndag i januari|


## <a name="next-steps"></a>Nästa steg

Exempel på uppgifter som utlöses av källa kod åtaganden eller basavbildningen uppdateringar, kan du ta en titt på [ACR uppgifter självstudieserien](container-registry-tutorial-quick-task.md).



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