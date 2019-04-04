---
title: Automatisera bild skapa, testa och korrigera med Azure Container Registry flerstegstest uppgifter
description: En introduktion till aktiviteter i flera steg, en funktion i ACR uppgifter i Azure Container Registry med uppgiftsbaserade arbetsflöden för att skapa, testa och korrigeringar behållaravbildningar i molnet.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895458"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Köra flera steg skapa, testa och patch uppgifter i ACR-uppgifter

Uppgifter i flera steg utöka enda avbildning build och push kapaciteten för ACR-aktiviteter med flera steg, flera container-baserade arbetsflöden. Använda aktiviteter med flera steg för att bygga och överföra flera bilder i serien eller parallellt. Kör sedan dessa avbildningar som kommandon i en enskild uppgift-körning. Varje steg definierar en behållaravbildning skapa eller skicka åtgärden och kan också definiera körningen av en behållare. Varje steg i en aktivitet med flera steg använder en behållare som dess körningsmiljö.

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Du kan till exempel köra en aktivitet för steg och automatiserar följande logik:

1. Skapa en avbildning för web-program
1. Kör webbprogramsbehållaren
1. Skapa en avbildning för web application test
1. Kör test webbprogramsbehållaren som utför tester mot löpande programbehållare
1. Om testet lyckas, skapa ett Helm-diagram Arkiv-paket
1. Utföra en `helm upgrade` med hjälp av nya Helm-diagram Arkiv-paketet

Alla åtgärder utförs i Azure, avlastning av arbetet som Azure-beräkningsresurser och så att du från infrastrukturhantering. Förutom Azure container registry betalar du bara för de resurser du använder. Mer information om priser finns i den **bygg Container** i avsnittet [priser för Azure Container Registry][pricing].


## <a name="common-task-scenarios"></a>Vanliga scenarier för uppgift

Uppgifter i flera steg gör det möjligt för scenarier som följande logik:

* Skapa tagg, och skicka en eller flera behållaravbildningar i serien eller parallellt.
* Kör och samla in enhet test och kod täckning resultat.
* Kör och samla in funktionella tester. ACR-uppgifter kan köras flera behållare, köra ett antal begäranden mellan dem.
* Utföra uppgiftsbaserade körningen, inklusive före/efter steg i en behållare bild build.
* Distribuera en eller flera behållare med ditt favorit distributionsmotorn till målmiljön.

## <a name="multi-step-task-definition"></a>Flera steg aktivitetsdefinition

En uppgift för flera steg i ACR uppgifter definieras som en serie steg inom en YAML-fil. Varje steg kan ange beroenden när åtgärden har slutförts för en eller flera föregående steg. Följande typer av uppgiften steg är tillgängliga:

* [`build`](container-registry-tasks-reference-yaml.md#build): Skapa en eller flera behållaravbildningar med välbekanta `docker build` syntax i serien eller parallellt.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push-överför avbildningar till ett behållarregister. Privata register som Azure Container Registry stöds som är den offentliga Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Kör en behållare, så att den kan fungera som en funktion inom ramen för aktiviteten som körs. Du kan skicka parametrar till behållarens `[ENTRYPOINT]`, och ange egenskaper som env, koppla bort, och andra välbekanta `docker run` parametrar. Den `cmd` stegtyp möjliggör enhet och funktionstestning med samtidiga behållare körning.

Följande kodfragment visar hur du kombinerar dessa steg aktivitetstyper. Uppgifter i flera steg kan vara lika enkelt som att skapa en enda avbildning från en Dockerfile och push-överföra till ditt register med en YAML-fil som liknar:

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Eller mer komplexa, till exempel det här fiktiva flera steg definition som innehåller stegen för att bygga, testa helm-paketet och helm distribuera (behållarregister och Helm-lagringsplatsen konfiguration inte visas):

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Se [uppgift exempel] [ task-examples] för utföra flera steg YAML aktivitetsfiler och Dockerfiles för flera scenarier.

## <a name="run-a-sample-task"></a>Köra en exempelaktivitet

Uppgifter stöder både manuell körning, kallas ett ”snabbt köra”, och uppdatera för automatisk körning på Git bekräftas eller base avbildning.

Om du vill köra en uppgift du först definiera aktivitetens steg i en YAML-fil och sedan köra Azure CLI-kommandot [az acr kör][az-acr-run].

Här är ett exempel på Azure CLI-kommando som kör en uppgift med hjälp av ett exempel uppgift YAML-fil. Steg skapar och skickar sedan en bild. Uppdatera `\<acrName\>` med namnet på din egen Azure-behållarregister innan du kör kommandot.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

När du kör uppgiften ska utdata Visa förloppet för varje steg som definierats i YAML-fil. I följande utdata visas steg som `acb_step_0` och `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
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

Mer information om automatiska versioner på Git bekräftas eller base uppdateringar finns i den [automatisera avbildningar](container-registry-tutorial-build-task.md) och [baserar uppdateringen avbildningar](container-registry-tutorial-base-image-update.md) självstudiekursens artiklar.

## <a name="next-steps"></a>Nästa steg

Du kan hitta referens för flera steg och exemplen här:

* [Uppgift referens](container-registry-tasks-reference-yaml.md) -stegtyper, deras egenskaper och användning.
* [Uppgift exempel] [ task-examples] -exempel `task.yaml` filer för flera enkla eller komplexa scenarier.
* [Cmd lagringsplatsen](https://github.com/AzureCR/cmd) – en uppsättning behållare som kommandon för ACR-uppgifter.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task