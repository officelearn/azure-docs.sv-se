---
title: Flera steg uppgift att bygga, testa & patch bild
description: Introduktion till uppgifter i flera steg, en funktion i ACR-uppgifter i Azure Container Registry som tillhandahåller uppgiftsbaserade arbetsflöden för att skapa, testa och korrigera behållaravbildningar i molnet.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399697"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Köra flerstegsuppbyggnads-, test- och korrigeringsuppgifter i ACR-uppgifter

Multistegsuppgifter utökar acr-uppgifternas enhetsuppbyggnad och push-funktioner med flera steg, flera behållarbaserade arbetsflöden. Använd multistegsuppgifter för att skapa och skicka flera bilder, i serie eller parallellt. Kör sedan dessa bilder som kommandon i en enda aktivitetskörning. Varje steg definierar en behållaravbildnings- eller push-åtgärd och kan också definiera körningen av en behållare. Varje steg i en aktivitet i flera steg använder en behållare som körningsmiljö.

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Du kan till exempel köra en aktivitet med steg som automatiserar följande logik:

1. Skapa en avbildning av webbprogram
1. Kör behållaren för webbprogram
1. Skapa en testavbildning för webbprogram
1. Kör testbehållaren för webbprogram som utför tester mot programbehållaren som körs
1. Om testerna går igenom, bygga ett Helm-diagramarkivpaket
1. Utföra `helm upgrade` ett med det nya Helm-diagramarkivpaketet

Alla steg utförs i Azure, avlastning arbetet till Azures beräkningsresurser och befria dig från infrastrukturhantering. Förutom ditt Azure-behållarregister betalar du bara för de resurser du använder. Information om priser finns i avsnittet **Container Build** i [prissättningen för Azure Container-registret][pricing].


## <a name="common-task-scenarios"></a>Vanliga aktivitetsscenarier

Aktiviteter i flera steg möjliggör scenarier som följande logik:

* Skapa, tagga och skicka en eller flera behållaravbildningar, i serie eller parallellt.
* Kör och samla in enhetstest- och kodtäckningsresultat.
* Kör och fånga funktionstester. ACR-uppgifter stöder körning av mer än en behållare och kör en serie begäranden mellan dem.
* Utför uppgiftsbaserad körning, inklusive steg före/efter för en behållaravbildning.
* Distribuera en eller flera behållare med din favoritdistributionsmotor till din målmiljö.

## <a name="multi-step-task-definition"></a>Aktivitetsdefinition i flera steg

En aktivitet i flera steg i ACR-uppgifter definieras som en serie steg i en YAML-fil. Varje steg kan ange beroenden för att slutföra ett eller flera tidigare steg. Följande uppgiftsstegstyper är tillgängliga:

* [`build`](container-registry-tasks-reference-yaml.md#build): Skapa en eller flera `docker build` behållaravbildningar med välbekant syntax, serie eller parallellt.
* [`push`](container-registry-tasks-reference-yaml.md#push): Skicka in byggda avbildningar till ett behållarregister. Privata register som Azure Container Registry stöds, liksom den offentliga Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Kör en behållare, så att den kan fungera som en funktion inom ramen för den pågående aktiviteten. Du kan skicka parametrar till `[ENTRYPOINT]`behållarens och ange egenskaper som env, koppla från och andra välbekanta `docker run` parametrar. Stegtypen `cmd` möjliggör enhets- och funktionstestning, med samtidig körning av behållare.

Följande kodavsnitt visar hur du kombinerar dessa stegtyper för aktiviteter. Flerstegsuppgifter kan vara så enkla som att skapa en enda avbildning från en Dockerfile och skicka till registret, med en YAML-fil som liknar:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Eller mer komplex, till exempel den här fiktiva flerstegsdefinitionen som innehåller steg för bygg-, test-, helm-paket och helm deploy (containerregistry och Helm-databaskonfiguration visas inte):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Se [uppgiftsexempel](container-registry-tasks-samples.md) för YAML-filer och Dockerfiler i flera steg i flera scenarier.

## <a name="run-a-sample-task"></a>Köra en exempelaktivitet

Uppgifter stöder både manuell körning, så kallad "snabbkörning" och automatisk körning på Git commit eller basavbildningsuppdatering.

Om du vill köra en uppgift definierar du först aktivitetens steg i en YAML-fil och kör sedan Azure CLI-kommandot [az acr run][az-acr-run].

Här är ett exempel på Azure CLI-kommando som kör en uppgift med hjälp av en EXEMPELuppgift YAML-fil. Dess steg bygga och sedan driva en bild. Uppdatera `\<acrName\>` med namnet på ditt eget Azure-behållarregister innan du kör kommandot.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

När du kör aktiviteten ska utdata visa förloppet för varje steg som definieras i YAML-filen. I följande utdata visas `acb_step_0` stegen `acb_step_1`som och .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Mer information om automatiska versioner av Git commit eller basavbildningsuppdatering finns i [självstudieartiklarna Automatisera avbildning och](container-registry-tutorial-build-task.md) [Grundläggande avbildningsuppdatering](container-registry-tutorial-base-image-update.md) bygger självstudieartiklar.

## <a name="next-steps"></a>Nästa steg

Du hittar aktivitetsreferens och exempel i flera steg här:

* [Aktivitetsreferens](container-registry-tasks-reference-yaml.md) - Stegtyper för uppgift, deras egenskaper och användning.
* [Uppgiftsexempel](container-registry-tasks-samples.md) - Exempel- `task.yaml` och Docker-filer för flera scenarier, enkla till komplexa.
* [Cmd repo](https://github.com/AzureCR/cmd) - En samling behållare som kommandon för ACR-uppgifter.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task