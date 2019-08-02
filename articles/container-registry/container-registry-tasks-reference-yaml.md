---
title: Referens för Azure Container Registry uppgifter – YAML
description: Referens för att definiera aktiviteter i YAML för ACR-aktiviteter, inklusive aktivitets egenskaper, steg typer, steg egenskaper och inbyggda variabler.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 27c38f51104dfb170c59860c96a8e3a86973bb1e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638914"
---
# <a name="acr-tasks-reference-yaml"></a>Referens för ACR-uppgifter: YAML

Aktivitets definitionen i flera steg i ACR-aktiviteter ger en molnbaserad Compute-beräkning som fokuserar på att skapa, testa och korrigera behållare. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntax för de YAML-filer som definierar dina aktiviteter i flera steg.

Den här artikeln innehåller information om hur du skapar YAML-filer med flera steg för ACR-aktiviteter. Om du vill ha en introduktion till ACR-uppgifter går du till [Översikt över ACR-aktiviteter](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. yaml-filformat

ACR-aktiviteter har stöd för en aktivitets deklaration med flera steg i standardsyntaxen för YAML. Du definierar en aktivitets steg i en YAML-fil. Du kan sedan köra aktiviteten manuellt genom att skicka filen till kommandot [AZ ACR Run][az-acr-run] . Du kan också använda filen för att skapa en uppgift med [AZ ACR Task Create][az-acr-task-create] som utlöses automatiskt vid en git-incheckning eller en bas avbildnings uppdatering. Även om den här artikeln `acr-task.yaml` refererar till som den fil som innehåller stegen, stöder ACR-aktiviteter alla giltiga fil namns namn med ett [tillägg som stöds](#supported-task-filename-extensions).

De `acr-task.yaml` översta topparna är **aktivitets egenskaper**, **steg typer**och **steg egenskaper**:

* [Aktivitets egenskaperna](#task-properties) gäller för alla steg i uppgifts körningen. Det finns flera globala aktivitets egenskaper, inklusive:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* De olika typerna av åtgärder som kan utföras i en [aktivitet representerar de](#task-step-types) typer av åtgärder som kan utföras. Det finns tre typer av steg:
  * `build`
  * `push`
  * `cmd`
* [Egenskaper för aktivitets steg](#task-step-properties) är parametrar som gäller för ett enskilt steg. Det finns flera steg-egenskaper, inklusive:
  * `startDelay`
  * `timeout`
  * `when`
  * ... och många fler.

Bas formatet för en `acr-task.yaml` fil, inklusive några vanliga steg egenskaper, följer. Även om det inte finns en fullständig representation av alla tillgängliga steg-egenskaper eller användnings typer, ger det en snabb översikt över det grundläggande fil formatet.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Aktivitets fil namns tillägg som stöds

ACR-aktiviteter har reserverat flera fil `.yaml`namns tillägg, inklusive, som ska bearbetas som en aktivitets fil. Alla tillägg som *inte* finns i följande lista betraktas som ACR-aktiviteter som Dockerfile:. yaml,. yml,. toml,. JSON,. sh,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. py,. lua,.

YAML är det enda fil format som för närvarande stöds av ACR-uppgifter. De andra fil namns tilläggen är reserverade för eventuell framtida support.

## <a name="run-the-sample-tasks"></a>Kör exempel aktiviteter

Det finns flera exempel på aktivitets filer som refereras i följande avsnitt i den här artikeln. Exempel uppgifterna finns i en offentlig GitHub-lagringsplats, [Azure-samples/ACR-tasks][acr-tasks]. Du kan köra dem med Azure CLI-kommandot [AZ ACR kör][az-acr-run]. Exempel kommandona liknar:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formateringen av exempel kommandona förutsätter att du har konfigurerat ett standard register i Azure CLI, så att `--registry` parametererna utelämnas. Om du vill konfigurera ett standard register använder du kommandot [AZ Configure][az-configure] med `--defaults` parametern som accepterar ett `acr=REGISTRY_NAME` värde.

Om du till exempel vill konfigurera Azure CLI med ett standard register med namnet "Registry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Aktivitets egenskaper

Aktivitets egenskaper visas vanligt vis överst i en `acr-task.yaml` fil och globala egenskaper som gäller för hela körningen av uppgifts stegen. Några av dessa globala egenskaper kan åsidosättas i ett enskilt steg.

| Egenskap | type | Valfritt | Beskrivning | Åsidosättning stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Ja | Versionen av `acr-task.yaml` filen som parsas av ACR tasks-tjänsten. Medan ACR-aktiviteter strävar efter bakåtkompatibilitet, tillåter det här värdet ACR-aktiviteter för att upprätthålla kompatibilitet inom en definierad version. Om inget anges används den senaste versionen som standard. | Nej | Inga |
| `stepTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder som ett steg kan köras. Om egenskapen anges för en aktivitet anges standard `timeout` egenskapen för alla steg. `timeout` Om egenskapen anges i ett steg åsidosätts den egenskap som anges av uppgiften. | Ja | 600 (10 minuter) |
| `workingDirectory` | sträng | Ja | Arbets katalogen i behållaren under körning. Om egenskapen anges för en aktivitet anges standard `workingDirectory` egenskapen för alla steg. Om den anges i ett steg åsidosätts den egenskap som anges av uppgiften. | Ja | `$HOME` |
| `env` | [sträng, sträng,...] | Ja |  Sträng mat ris i `key=value` format som definierar miljövariabler för aktiviteten. Om egenskapen anges för en aktivitet anges standard `env` egenskapen för alla steg. Om det anges i ett steg åsidosätts alla miljövariabler som ärvts från uppgiften. | Inga |
| `secrets` | [hemligt, hemligt,...] | Ja | Matris med [hemliga](#secret) objekt. | Inga |
| `networks` | [nätverk, nätverk,...] | Ja | Matris med [nätverks](#network) objekt. | Ingen |

### <a name="secret"></a>secret

Objektet Secret har följande egenskaper.

| Egenskap | type | Valfritt | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sträng | Nej | Identifieraren för hemligheten. | Inga |
| `keyvault` | sträng | Ja | Den Azure Key Vault hemliga URL: en. | Inga |
| `clientID` | sträng | Ja | Klient-ID: t för den [användarspecifika hanterade identiteten](container-registry-tasks-authentication-managed-identity.md) för Azure-resurser. | Inga |

### <a name="network"></a>nätverk

Objektet Network har följande egenskaper.

| Egenskap | type | Valfritt | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | Nej | Nätverkets namn. | Inga |
| `driver` | sträng | Ja | Driv rutinen för att hantera nätverket. | Ingen |
| `ipv6` | bool | Ja | Om IPv6-nätverk är aktiverat. | `false` |
| `skipCreation` | bool | Ja | Om du vill hoppa över att skapa nätverk. | `false` |
| `isDefault` | bool | Ja | Om nätverket är ett standard nätverk som medföljer Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typer av uppgifts steg

ACR-aktiviteter stöder tre steg typer. Varje stegtyp stöder flera egenskaper som beskrivs i avsnittet för varje typ av steg.

| Steg typ | Beskrivning |
| --------- | ----------- |
| [`build`](#build) | Skapar en behållar avbildning `docker build` med hjälp av välbekant syntax. |
| [`push`](#push) | Kör en `docker push` av nyligen inbyggda eller omtaggade avbildningar till ett behållar register. Azure Container Registry, andra privata register och den offentliga Docker-hubben stöds. |
| [`cmd`](#cmd) | Kör en behållare som ett kommando, med parametrar som skickas till behållarens `[ENTRYPOINT]`. Stegtyp stöder parametrar som `env`, och andra `detach`välkända `docker run` kommando alternativ, aktiverar enhets-och funktions testning med körning av samtidiga behållare. `cmd` |

## <a name="build"></a>konstruktion

Bygg en behållar avbildning. Steg typen representerar en flera klient, säkra metoder för att köra `docker build` i molnet som en överordnad första klass. `build`

### <a name="syntax-build"></a>Syntax: build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` Steg typen stöder parametrarna i följande tabell. Steg typen har även stöd för alla build-alternativ [](https://docs.docker.com/engine/reference/commandline/build/) för `--build-arg` kommandot Docker build, till exempel för att ställa in Bygg tids variabler. `build`

| Parameter | Beskrivning | Valfritt |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Definierar den färdiga avbildningens fullständigt kvalificerade `image:tag` avbildning.<br /><br />Eftersom bilder kan användas för inre aktivitets valideringar, till exempel funktionella tester, kräver `push` inte alla avbildningar i registret. Men för att kunna instans av en bild i en uppgifts körning behöver avbildningen ett namn att referera till.<br /><br />Till skillnad `az acr build`från fungerar inte ACR-aktiviteter som standard-push-beteende. Med ACR-uppgifter förutsätter standard scenariot att du kan bygga, validera och sedan skicka en avbildning. Se [push](#push) för hur du kan skicka inbyggda avbildningar. | Ja |
| `-f`&#124;`--file` | Anger den Dockerfile som skickades till `docker build`. Om inget värde anges antas standard-Dockerfile i kontextens rot. Om du vill ange en Dockerfile skickar du fil namnet i förhållande till kontextens rot. | Ja |
| `context` | Rot katalogen som skickades `docker build`till. Rot katalogen för varje aktivitet anges till en delad [WorkingDirectory](#task-step-properties)och inkluderar roten för den tillhör ande git-klonade katalogen. | Nej |

### <a name="properties-build"></a>Egenskaper: version

`build` Steg typen stöder följande egenskaper. Mer information om dessa egenskaper finns i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfritt |
| `disableWorkingDirectoryOverride` | bool | Valfritt |
| `entryPoint` | sträng | Valfritt |
| `env` | [sträng, sträng,...] | Valfritt |
| `expose` | [sträng, sträng,...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | bool | Valfritt |
| `isolation` | sträng | Valfritt |
| `keep` | bool | Valfritt |
| `network` | object | Valfritt |
| `ports` | [sträng, sträng,...] | Valfritt |
| `pull` | bool | Valfritt |
| `repeat` | int | Valfritt |
| `retries` | int | Valfritt |
| `retryDelay` | int (sekunder) | Valfritt |
| `secret` | object | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `when` | [sträng, sträng,...] | Valfritt |
| `workingDirectory` | sträng | Valfritt |

### <a name="examples-build"></a>Exempel: build

#### <a name="build-image---context-in-root"></a>Bygg avbildnings kontext i roten

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Bygg avbildnings kontext i under Katalog

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>trycka

Push en eller flera inbyggda eller omtaggade avbildningar till ett behållar register. Stöder push-överföring till privata register som Azure Container Registry eller till den offentliga Docker-hubben.

### <a name="syntax-push"></a>Syntax: push

`push` Steg typen stöder en samling avbildningar. YAML Collection-syntax stöder infogade och kapslade format. Att överföra en enda avbildning visas vanligt vis med en infogad syntax:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Använd kapslad syntax vid push-överföring av flera avbildningar för ökad läsbarhet:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

`push` Steg typen stöder följande egenskaper. Mer information om dessa egenskaper finns i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `env` | [sträng, sträng,...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | bool | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `when` | [sträng, sträng,...] | Valfritt |

### <a name="examples-push"></a>Exempel: push

#### <a name="push-multiple-images"></a>Skicka flera avbildningar

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Skapa, push och kör

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` Steg typen kör en behållare.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

`cmd` Steg typen stöder följande egenskaper:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfritt |
| `disableWorkingDirectoryOverride` | bool | Valfritt |
| `entryPoint` | sträng | Valfritt |
| `env` | [sträng, sträng,...] | Valfritt |
| `expose` | [sträng, sträng,...] | Valfritt |
| `id` | sträng | Valfritt |
| `ignoreErrors` | bool | Valfritt |
| `isolation` | sträng | Valfritt |
| `keep` | bool | Valfritt |
| `network` | object | Valfritt |
| `ports` | [sträng, sträng,...] | Valfritt |
| `pull` | bool | Valfritt |
| `repeat` | int | Valfritt |
| `retries` | int | Valfritt |
| `retryDelay` | int (sekunder) | Valfritt |
| `secret` | object | Valfritt |
| `startDelay` | int (sekunder) | Valfritt |
| `timeout` | int (sekunder) | Valfritt |
| `when` | [sträng, sträng,...] | Valfritt |
| `workingDirectory` | sträng | Valfritt |

Du hittar information om de här egenskaperna i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör Hello-World-avbildning

Det här kommandot kör `hello-world.yaml` aktivitets filen som refererar till [Hello-World-](https://hub.docker.com/_/hello-world/) avbildningen på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash-avbildning och Echo "Hello World"

Det här kommandot kör `bash-echo.yaml` aktivitets filen som refererar till [bash](https://hub.docker.com/_/bash/) -avbildningen på Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Kör speciell bash-avbildnings tag

Om du vill köra en angiven avbildnings version anger du taggen `cmd`i.

Det här kommandot kör `bash-echo-3.yaml` aktivitets filen som refererar till [bash: 3.0](https://hub.docker.com/_/bash/) -avbildningen på Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Köra anpassade avbildningar

Steg `cmd` typen refererar till bilder med hjälp av `docker run` standardformatet. Avbildningar som inte föregås av ett register antas ha sitt ursprung i docker.io. Det tidigare exemplet kan vara likvärdigt med:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Genom att använda standard `docker run` referens konventionen för avbildningar kan du köra bilder från privata register eller den offentliga Docker- `cmd` hubben. Om du refererar till avbildningar i samma register där ACR-aktiviteten körs, behöver du inte ange några autentiseringsuppgifter för registret.

* Kör en avbildning från ett Azure Container Registry

    Ersätt `[myregistry]` med namnet på ditt register:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalisera register referensen med en Run-variabel

    I stället för att hårdkoda ditt register namn i en `acr-task.yaml` fil kan du göra det mer portabelt med hjälp av en [Run-variabel](#run-variables). `Run.Registry` Variabeln expanderas vid körning till namnet på det register som aktiviteten körs i.

    Om du vill generalisera föregående aktivitet så att den fungerar i alla Azure Container Registry refererar du till [Kör. Registry](#runregistry) -variabeln i avbildnings namnet:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Egenskaper för aktivitets steg

Varje typ av steg stöder flera egenskaper som passar för typen. I följande tabell definieras alla tillgängliga steg egenskaper. Alla typer av steg har inte stöd för alla egenskaper. Information om vilka av dessa egenskaper som är tillgängliga för varje steg typ finns i avsnittet om typ referens för [cmd](#cmd), [build](#build)och [push](#push) .

| Egenskap | type | Valfritt | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ja | Anger om behållaren ska kopplas från när den körs. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ja | Om åsidosättning- `workingDirectory` funktionen ska inaktive ras. Använd detta i kombination med `workingDirectory` för att få fullständig kontroll över behållarens arbets katalog. | `false` |
| `entryPoint` | sträng | Ja | Åsidosätter `[ENTRYPOINT]` i ett stegs behållare. | Inga |
| `env` | [sträng, sträng,...] | Ja | Sträng mat ris i `key=value` formatet som definierar miljövariablerna för steget. | Ingen |
| `expose` | [sträng, sträng,...] | Ja | Matris med portar som exponeras från behållaren. |  Inga |
| [`id`](#example-id) | sträng | Ja | Identifierar ett unikt steg i uppgiften. Andra steg i uppgiften kan referera till ett steg `id`, till exempel för beroende kontroll med. `when`<br /><br />`id` Är också namnet på den behållare som körs. Processer `id` som körs i andra behållare i aktiviteten kan referera till som sitt DNS-värdnamn, eller för att komma åt den med Docker-loggar [ID], till exempel. | `acb_step_%d`, där `%d` är det 0-baserade indexet för steget överst i yaml-filen |
| `ignoreErrors` | bool | Ja | Om du vill markera steget som slutfört oavsett om ett fel uppstod när containern kördes. | `false` |
| `isolation` | sträng | Ja | Behållarens isolerings nivå. | `default` |
| `keep` | bool | Ja | Anger om stegets behållare ska behållas efter körning. | `false` |
| `network` | object | Ja | Identifierar ett nätverk där behållaren körs. | Inga |
| `ports` | [sträng, sträng,...] | Ja | Matris med portar som publiceras från behållaren till värden. |  Inga |
| `pull` | bool | Ja | Om du vill tvinga fram en hämtning av behållaren innan du kör den för att förhindra alla funktioner för cachelagring. | `false` |
| `privileged` | bool | Ja | Om behållaren ska köras i privilegierat läge. | `false` |
| `repeat` | int | Ja | Antalet försök att upprepa körningen av en behållare. | 0 |
| `retries` | int | Ja | Antalet återförsök som ska göras om en behållare inte kan köras. Ett nytt försök görs bara om slut koden för en behållare är skilt från noll. | 0 |
| `retryDelay` | int (sekunder) | Ja | Fördröjningen i sekunder mellan återförsök för en behållares körning. | 0 |
| `secret` | object | Ja | Identifierar en Azure Key Vault hemlig eller [hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). | Inga |
| `startDelay` | int (sekunder) | Ja | Antal sekunder som en behållares körning ska fördröjas. | 0 |
| `timeout` | int (sekunder) | Ja | Maximalt antal sekunder som ett steg kan utföras innan det avslutas. | 600 |
| [`when`](#example-when) | [sträng, sträng,...] | Ja | Konfigurerar ett stegs beroende av ett eller flera andra steg i aktiviteten. | Inga |
| `user` | sträng | Ja | Användar namnet eller UID för en behållare | Ingen |
| `workingDirectory` | sträng | Ja | Anger arbets katalogen för ett steg. Som standard skapar ACR-aktiviteter en rot Katalog som arbets katalog. Men om din version har flera steg, kan tidigare steg dela artefakter med senare steg genom att ange samma arbets katalog. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exempel: Egenskaper för aktivitets steg

#### <a name="example-id"></a>Exempel: ID

Bygg två avbildningar och indelning av en funktionell test avbildning. Varje steg identifieras av ett unikt `id` steg i aktivitets referensen i sina `when` egenskaper.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

`when` Egenskapen anger ett stegs beroende av andra steg i uppgiften. Det stöder två parameter värden:

* `when: ["-"]`-Indikerar inget beroende på andra steg. Ett steg som `when: ["-"]` anger börjar köras omedelbart och möjliggör körning av samtidiga steg.
* `when: ["id1", "id2"]`-Anger att steget är beroende av stegen med `id` "id1" och `id` "ID2". Det här steget körs inte förrän båda stegen "id1" och "ID2" har slutförts.

Om `when` inte anges i ett steg är det steget beroende av att föregående steg har slutförts `acr-task.yaml` i filen.

Sekventiell steg körning utan `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekventiell steg körning med `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Utveckla parallella bilder:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallell avbildnings version och beroende testning:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Kör variabler

ACR-uppgifter innehåller en standard uppsättning med variabler som är tillgängliga för uppgifts steg när de körs. Dessa variabler kan nås med hjälp av formatet `{{.Run.VariableName}}`, där `VariableName` är något av följande:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Varje körning, via `az acr run`eller utlösare baserad körning av uppgifter som `az acr task create` skapats med har ett unikt ID. ID: t representerar körningen som körs för närvarande.

Används vanligt vis för att unikt tagga en bild:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Det fullständigt kvalificerade Server namnet för registret. Används vanligt vis för att allmänt referera till registret där aktiviteten körs.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Kör. datum

Den aktuella UTC-tid då körningen påbörjades.

### <a name="runcommit"></a>Kör. commit

För en uppgift som utlöses av ett genomförande till en GitHub-lagringsplats, COMMIT-ID: t.

### <a name="runbranch"></a>Run.Branch

För en uppgift som utlöses av ett genomförande till en GitHub-lagringsplats, namnet på grenen.

## <a name="next-steps"></a>Nästa steg

En översikt över aktiviteter i flera steg finns i [utföra åtgärder för att skapa flera steg, testa och uppdatera i ACR uppgifter](container-registry-tasks-multi-step.md).

För enskilda-steg-versioner, se [Översikt över ACR-aktiviteter](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
