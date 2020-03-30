---
title: Rensa taggar och manifest
description: Använd ett rensningskommando för att ta bort flera taggar och manifest från ett Azure-behållarregister baserat på ålder och ett taggfilter och kan eventuellt schemalägga rensningsåtgärder.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087331"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Rensa avbildningar automatiskt från ett Azure-behållarregister

När du använder ett Azure-behållarregister som en del av ett utvecklingsarbetsflöde kan registret snabbt fyllas med avbildningar eller andra artefakter som inte behövs efter en kort period. Du kanske vill ta bort alla taggar som är äldre än en viss varaktighet eller matcha ett angivet namnfilter. Om du snabbt vill ta bort flera `acr purge` artefakter introducerar den här artikeln kommandot som du kan köra som en ACR-aktivitet på begäran eller [schemalagd.](container-registry-tasks-scheduled.md) 

Kommandot `acr purge` distribueras för närvarande i`mcr.microsoft.com/acr/acr-cli:0.1`en offentlig behållaravbildning ( ), byggd från källkoden i [acr-cli-repo](https://github.com/Azure/acr-cli) i GitHub.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra ACR-uppgiftsexejerna i den här artikeln. Om du vill använda den lokalt krävs version 2.0.69 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install]. 

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Använd `acr purge` kommandot med försiktighet - borttagna bilddata är oåterkallelig. Om du har system som hämtar bilder genom manifestsammandrag (i motsats till bildnamn) bör du inte rensa otaggade bilder. Om du tar bort otaggade avbildningar hindras dessa system från att dra bilderna från registret. Istället för att dra genom manifest, överväga att anta ett *unikt taggningsschema,* en [rekommenderad bästa praxis](container-registry-image-tag-version.md).

Om du vill ta bort enstaka avbildningstaggar eller manifest med Azure CLI-kommandon läser du [Ta bort behållaravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Använda kommandot rensa

Behållarkommandot `acr purge` tar bort bilder efter tagg i en databas som matchar ett namnfilter och som är äldre än en angiven varaktighet. Som standard tas endast taggreferenser bort, inte underliggande [manifest](container-registry-concepts.md#manifest) och lagerdata. Kommandot har ett alternativ för att också ta bort manifest. 

> [!NOTE]
> `acr purge`tar inte bort en bildtagg `write-enabled` eller databas `false`där attributet är inställt på . Mer information finns [i Låsa en behållaravbildning i ett Azure-behållarregister](container-registry-image-lock.md).

`acr purge`är utformat för att köras som ett behållarkommando i en [ACR-uppgift](container-registry-tasks-overview.md), så att det autentiseras automatiskt med registret där aktiviteten körs och utför åtgärder där. I uppgiftsexempelna `acr purge` i den här artikeln används [kommandoaliaset](container-registry-tasks-reference-yaml.md#aliases) i stället för ett fullständigt kvalificerat containeravbildningskommando.

Ange minst följande när du `acr purge`kör:

* `--filter`- En databas och ett *reguljärt uttryck* för att filtrera taggar i databasen. Exempel: `--filter "hello-world:.*"` matchar alla `hello-world` taggar i `--filter "hello-world:^1.*"` databasen och `1`matchar taggar som börjar med . Skicka `--filter` flera parametrar för att rensa flera databaser.
* `--ago`- En [längdsträng](https://golang.org/pkg/time/) i Go-stil för att ange en varaktighet utöver vilken bilder tas bort. Varaktigheten består av en sekvens med ett eller flera decimaltal, var och en med ett enhetssuffix. Giltiga tidsenheter inkluderar "d" i dagar, "h" i timmar och "m" i minuter. Du `--ago 2d3h6m` väljer till exempel alla filtrerade bilder senast ändrade mer än 2 dagar, `--ago 1.5h` 3 timmar och 6 minuter sedan och väljer bilder som senast ändrades för mer än 1,5 timmar sedan.

`acr purge`stöder flera valfria parametrar. Följande två används i exempel i den här artikeln:

* `--untagged`- Anger att manifest som inte har associerade taggar *(otaggade manifest)* tas bort.
* `--dry-run`- Anger att inga data tas bort, men utdata är samma som om kommandot körs utan den här flaggan. Den här parametern är användbar för att testa ett rensningskommando för att se till att det inte oavsiktligt tar bort data som du tänker bevara.

Om du vill `acr purge --help`ha ytterligare parametrar kör du . 

`acr purge`stöder andra funktioner i ACR-uppgifter kommandon, inklusive [kör variabler](container-registry-tasks-reference-yaml.md#run-variables) och [aktivitetskörningsloggar](container-registry-tasks-logs.md) som strömmas och även sparas för senare hämtning.

### <a name="run-in-an-on-demand-task"></a>Köras i en aktivitet på begäran

I följande exempel används kommandot [az acr run][az-acr-run] för att köra `acr purge` kommandot på begäran. I det här exemplet tas alla `hello-world` bildtaggar och manifest bort i databasen i *registret* som ändrades för mer än 1 dag sedan. Behållarkommandot skickas med hjälp av en miljövariabel. Aktiviteten körs utan källkontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Köra i en schemalagd aktivitet

I följande exempel används kommandot [az acr-aktivitet][az-acr-task-create] för att skapa en daglig [schemalagd ACR-aktivitet](container-registry-tasks-scheduled.md). Uppgiften rensar taggar som ändrats för `hello-world` mer än 7 dagar sedan i databasen. Behållarkommandot skickas med hjälp av en miljövariabel. Aktiviteten körs utan källkontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Kör kommandot [az acr task show][az-acr-task-show] för att se att timerutlösaren är konfigurerad.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Rensa ett stort antal taggar och manifest

Det kan ta flera minuter eller längre att rensa ett stort antal taggar och manifest. Om du vill rensa tusentals taggar och manifest kan kommandot behöva köras längre än standardtidsgränsen på 600 sekunder för en aktivitet på begäran eller 3600 sekunder för en schemalagd aktivitet. Om timeout-tiden överskrids tas endast en delmängd av taggar och manifest bort. Om du vill vara säkra på att `--timeout` en storskalig rensning är klar skickar du parametern för att öka värdet. 

Följande aktivitet på begäran anger till exempel en timeout-tid på 3600 sekunder (1 timme):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exempel: Schemalagd rensning av flera databaser i ett register

I det här `acr purge` exemplet går du igenom med hjälp av att regelbundet rensa flera databaser i ett register. Du kan till exempel ha en utvecklingspipeline som skickar bilder till `samples/devimage1` och `samples/devimage2` databaser. Du importerar regelbundet utvecklingsavbildningar till en produktionsdatabas för dina distributioner, så du behöver inte längre utvecklingsavbildningarna. Varje vecka rensar du `samples/devimage1` `samples/devimage2` och förråden, som förberedelse för den kommande veckans arbete.

### <a name="preview-the-purge"></a>Förhandsgranska rensningen

Innan du tar bort data rekommenderar vi att `--dry-run` du kör en rensningsaktivitet på begäran med parametern. Med det här alternativet kan du se de taggar och manifest som kommandot rensar, utan att ta bort några data. 

I följande exempel markerar filtret i varje databas alla taggar. Parametern `--ago 0d` matchar bilder i alla åldrar i de databaser som matchar filtren. Ändra urvalskriterierna efter behov för ditt scenario. Parametern `--untagged` anger att manifest ska tas bort förutom taggar. Behållarkommandot skickas till kommandot [az acr run][az-acr-run] med hjälp av en miljövariabel.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Granska kommandoutdata för att se de taggar och manifest som matchar urvalsparametrarna. Eftersom kommandot körs `--dry-run`med tas inga data bort.

Exempel på utdata:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Schemalägg rensningen

När du har verifierat den torra körningen skapar du en schemalagd aktivitet för att automatisera rensningen. I följande exempel schemaläggs en veckoaktivitet på söndag klockan 1:00 UTC för att köra föregående rensningskommando:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Kör kommandot [az acr task show][az-acr-task-show] för att se att timerutlösaren är konfigurerad.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra alternativ för att [ta bort avbildningsdata](container-registry-delete.md) i Azure Container Registry.

Mer information om avbildningslagring finns [i Lagringsavbildningslagring i Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

