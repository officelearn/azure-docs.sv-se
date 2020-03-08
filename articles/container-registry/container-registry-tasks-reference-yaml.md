---
title: YAML-referens – ACR-uppgifter
description: Referens för att definiera aktiviteter i YAML för ACR-aktiviteter, inklusive aktivitets egenskaper, steg typer, steg egenskaper och inbyggda variabler.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669387"
---
# <a name="acr-tasks-reference-yaml"></a>Referens för ACR-uppgifter: YAML

Aktivitets definitionen i flera steg i ACR-aktiviteter ger en molnbaserad Compute-beräkning som fokuserar på att skapa, testa och korrigera behållare. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntax för de YAML-filer som definierar dina aktiviteter i flera steg.

Den här artikeln innehåller information om hur du skapar YAML-filer med flera steg för ACR-aktiviteter. Om du vill ha en introduktion till ACR-uppgifter går du till [Översikt över ACR-aktiviteter](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. yaml-filformat

ACR-aktiviteter har stöd för en aktivitets deklaration med flera steg i standardsyntaxen för YAML. Du definierar en aktivitets steg i en YAML-fil. Du kan sedan köra aktiviteten manuellt genom att skicka filen till kommandot [AZ ACR Run][az-acr-run] . Du kan också använda filen för att skapa en uppgift med [AZ ACR Task Create][az-acr-task-create] som utlöses automatiskt vid en git-incheckning eller en bas avbildnings uppdatering. Även om den här artikeln refererar till `acr-task.yaml` som filen som innehåller stegen, stöder ACR-aktiviteter alla giltiga fil namns namn med ett [tillägg som stöds](#supported-task-filename-extensions).

`acr-task.yaml` primitiver på översta nivån är **aktivitets egenskaper**, **steg typer**och **steg egenskaper**:

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

Det grundläggande formatet för en `acr-task.yaml`-fil, inklusive några vanliga steg-egenskaper, följer. Även om det inte finns en fullständig representation av alla tillgängliga steg-egenskaper eller användnings typer, ger det en snabb översikt över det grundläggande fil formatet.

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

ACR-aktiviteter har reserverade flera fil namns tillägg, inklusive `.yaml`, som ska bearbetas som en aktivitets fil. Alla tillägg som *inte* finns i följande lista betraktas som ACR-aktiviteter som Dockerfile:. yaml,. yml,. toml,. JSON,. sh,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. py,. lua,.

YAML är det enda fil format som för närvarande stöds av ACR-uppgifter. De andra fil namns tilläggen är reserverade för eventuell framtida support.

## <a name="run-the-sample-tasks"></a>Kör exempel aktiviteter

Det finns flera exempel på aktivitets filer som refereras i följande avsnitt i den här artikeln. Exempel uppgifterna finns i en offentlig GitHub-lagringsplats, [Azure-samples/ACR-tasks][acr-tasks]. Du kan köra dem med Azure CLI-kommandot [AZ ACR kör][az-acr-run]. Exempel kommandona liknar:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formateringen av exempel kommandona förutsätter att du har konfigurerat ett standard register i Azure CLI, så att du utelämnar parametern `--registry`. Om du vill konfigurera ett standard register använder du kommandot [AZ Configure][az-configure] med parametern `--defaults` som accepterar ett `acr=REGISTRY_NAME`-värde.

Om du till exempel vill konfigurera Azure CLI med ett standard register med namnet "Registry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Aktivitets egenskaper

Aktivitets egenskaper visas vanligt vis överst i en `acr-task.yaml`-fil och är globala egenskaper som gäller för hela körningen av uppgifts stegen. Några av dessa globala egenskaper kan åsidosättas i ett enskilt steg.

| Egenskap | Typ | Valfri | Beskrivning | Åsidosättning stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Ja | Versionen av `acr-task.yaml`-filen som parsas av tjänsten ACR Tasks. Medan ACR-aktiviteter strävar efter bakåtkompatibilitet, tillåter det här värdet ACR-aktiviteter för att upprätthålla kompatibilitet inom en definierad version. Om inget anges används den senaste versionen som standard. | Nej | Ingen |
| `stepTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder som ett steg kan köras. Om egenskapen anges för en uppgift, anges standard `timeout` egenskapen för alla steg. Om egenskapen `timeout` anges i ett steg, åsidosätts den egenskap som anges av uppgiften. | Ja | 600 (10 minuter) |
| `workingDirectory` | sträng | Ja | Arbets katalogen i behållaren under körning. Om egenskapen anges för en uppgift, anges standard `workingDirectory` egenskapen för alla steg. Om den anges i ett steg åsidosätts den egenskap som anges av uppgiften. | Ja | `/workspace` |
| `env` | [sträng, sträng,...] | Ja |  Sträng mat ris i `key=value` format som definierar miljövariabler för aktiviteten. Om egenskapen anges för en uppgift, anges standard `env` egenskapen för alla steg. Om det anges i ett steg åsidosätts alla miljövariabler som ärvts från uppgiften. | Ingen |
| `secrets` | [hemligt, hemligt,...] | Ja | Matris med [hemliga](#secret) objekt. | Ingen |
| `networks` | [nätverk, nätverk,...] | Ja | Matris med [nätverks](#network) objekt. | Ingen |

### <a name="secret"></a>hemlighet

Objektet Secret har följande egenskaper.

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sträng | Nej | Identifieraren för hemligheten. | Ingen |
| `keyvault` | sträng | Ja | Den Azure Key Vault hemliga URL: en. | Ingen |
| `clientID` | sträng | Ja | Klient-ID: t för den [användarspecifika hanterade identiteten](container-registry-tasks-authentication-managed-identity.md) för Azure-resurser. | Ingen |

### <a name="network"></a>nätverks

Objektet Network har följande egenskaper.

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | Nej | Nätverkets namn. | Ingen |
| `driver` | sträng | Ja | Driv rutinen för att hantera nätverket. | Ingen |
| `ipv6` | booleska | Ja | Om IPv6-nätverk är aktiverat. | `false` |
| `skipCreation` | booleska | Ja | Om du vill hoppa över att skapa nätverk. | `false` |
| `isDefault` | booleska | Ja | Om nätverket är ett standard nätverk som medföljer Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typer av uppgifts steg

ACR-aktiviteter stöder tre steg typer. Varje stegtyp stöder flera egenskaper som beskrivs i avsnittet för varje typ av steg.

| Steg typ | Beskrivning |
| --------- | ----------- |
| [`build`](#build) | Skapar en behållar avbildning med hjälp av välbekant `docker build` syntax. |
| [`push`](#push) | Kör en `docker push` av nyskapade eller omtaggade avbildningar till ett behållar register. Azure Container Registry, andra privata register och den offentliga Docker-hubben stöds. |
| [`cmd`](#cmd) | Kör en behållare som ett kommando, med parametrar som skickas till behållarens `[ENTRYPOINT]`. `cmd` stegtyp stöder parametrar som `env`, `detach`och andra välbekanta `docker run` kommando alternativ, vilket möjliggör enhets-och funktions testning med körning av samtidiga behållare. |

## <a name="build"></a>konstruktion

Bygg en behållar avbildning. `build` stegs typ representerar en multi-klient, säker metod för att köra `docker build` i molnet som en överordnad första klass.

### <a name="syntax-build"></a>Syntax: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Den `build` steg typen stöder parametrarna i följande tabell. `build` stegtyp stöder också alla build-alternativ för kommandot [Docker build](https://docs.docker.com/engine/reference/commandline/build/) , till exempel `--build-arg` för att ställa in Bygg tids variabler.

| Parameter | Beskrivning | Valfri |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definierar den fullständigt kvalificerade `image:tag` av den färdiga avbildningen.<br /><br />Eftersom bilder kan användas för inre aktivitets valideringar, till exempel funktionella tester, kräver inte alla avbildningar `push` till ett register. Men för att kunna instans av en bild i en uppgifts körning behöver avbildningen ett namn att referera till.<br /><br />Till skillnad från `az acr build`ger ACR uppgifter som standard inte push-beteende. Med ACR-uppgifter förutsätter standard scenariot att du kan bygga, validera och sedan skicka en avbildning. Se [push](#push) för hur du kan skicka inbyggda avbildningar. | Ja |
| `-f` &#124; `--file` | Anger det Dockerfile som skickats till `docker build`. Om inget värde anges antas standard-Dockerfile i kontextens rot. Om du vill ange en Dockerfile skickar du fil namnet i förhållande till kontextens rot. | Ja |
| `context` | Rot katalogen som skickades till `docker build`. Rot katalogen för varje aktivitet anges till en delad [WorkingDirectory](#task-step-properties)och inkluderar roten för den tillhör ande git-klonade katalogen. | Nej |

### <a name="properties-build"></a>Egenskaper: version

`build` stegtyp stöder följande egenskaper. Mer information om dessa egenskaper finns i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `detach` | booleska | Valfri |
| `disableWorkingDirectoryOverride` | booleska | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng,...] | Valfri |
| `expose` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | booleska | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | booleska | Valfri |
| `network` | objekt | Valfri |
| `ports` | [sträng, sträng,...] | Valfri |
| `pull` | booleska | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |
| `workingDirectory` | sträng | Valfri |

### <a name="examples-build"></a>Exempel: build

#### <a name="build-image---context-in-root"></a>Bygg avbildnings kontext i roten

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Bygg avbildnings kontext i under Katalog

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>trycka

Push en eller flera inbyggda eller omtaggade avbildningar till ett behållar register. Stöder push-överföring till privata register som Azure Container Registry eller till den offentliga Docker-hubben.

### <a name="syntax-push"></a>Syntax: push

`push` stegtyp stöder en samling avbildningar. YAML Collection-syntax stöder infogade och kapslade format. Att överföra en enda avbildning visas vanligt vis med en infogad syntax:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Använd kapslad syntax vid push-överföring av flera avbildningar för ökad läsbarhet:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

`push` stegtyp stöder följande egenskaper. Mer information om dessa egenskaper finns i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `env` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | booleska | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |

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

I den `cmd` steg typen körs en behållare.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

`cmd`s steg typ har stöd för följande egenskaper:

| | | |
| -------- | ---- | -------- |
| `detach` | booleska | Valfri |
| `disableWorkingDirectoryOverride` | booleska | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng,...] | Valfri |
| `expose` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | booleska | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | booleska | Valfri |
| `network` | objekt | Valfri |
| `ports` | [sträng, sträng,...] | Valfri |
| `pull` | booleska | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |
| `workingDirectory` | sträng | Valfri |

Du hittar information om de här egenskaperna i avsnittet [Egenskaper för aktivitets steg](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör Hello-World-avbildning

Det här kommandot kör den `hello-world.yaml` aktivitets filen som refererar till [Hello-World-](https://hub.docker.com/_/hello-world/) avbildningen på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash-avbildning och Echo "Hello World"

Det här kommandot kör den `bash-echo.yaml` aktivitets filen som refererar till [bash](https://hub.docker.com/_/bash/) -avbildningen på Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Kör speciell bash-avbildnings tag

Om du vill köra en angiven avbildnings version anger du taggen i `cmd`.

Det här kommandot kör den `bash-echo-3.yaml` aktivitets filen som refererar till [bash: 3.0](https://hub.docker.com/_/bash/) -avbildningen på Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Köra anpassade avbildningar

`cmd`-stegtyp refererar till bilder med standard `docker run` formatet. Avbildningar som inte föregås av ett register antas ha sitt ursprung i docker.io. Det tidigare exemplet kan vara likvärdigt med:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Genom att använda standard referens konventionen `docker run` avbildningen kan `cmd` köra bilder från ett privat register eller den offentliga Docker-hubben. Om du refererar till avbildningar i samma register där ACR-aktiviteten körs, behöver du inte ange några autentiseringsuppgifter för registret.

* Kör en avbildning från ett Azure Container Registry. I följande exempel förutsätter vi att du har ett register med namnet `myregistry`och en anpassad avbildning `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalisera register referensen med en Run-variabel eller ett alias

    I stället för att hårdkoda ditt register namn i en `acr-task.yaml`-fil kan du göra det mer portabelt med hjälp av en [Run-variabel](#run-variables) eller ett [alias](#aliases). `Run.Registry` variabeln eller `$Registry` Ali Aset expanderas vid körning till namnet på det register som aktiviteten körs i.

    Om du till exempel vill generalisera föregående aktivitet så att den fungerar i alla Azure Container Registry, referera till $Registry variabeln i avbildnings namnet:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Egenskaper för aktivitets steg

Varje typ av steg stöder flera egenskaper som passar för typen. I följande tabell definieras alla tillgängliga steg egenskaper. Alla typer av steg har inte stöd för alla egenskaper. Information om vilka av dessa egenskaper som är tillgängliga för varje steg typ finns i avsnittet om typ referens för [cmd](#cmd), [build](#build)och [push](#push) .

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | booleska | Ja | Anger om behållaren ska kopplas från när den körs. | `false` |
| `disableWorkingDirectoryOverride` | booleska | Ja | Om du vill inaktivera `workingDirectory` åsidosätta funktioner. Använd detta i kombination med `workingDirectory` för att få fullständig kontroll över behållarens arbets katalog. | `false` |
| `entryPoint` | sträng | Ja | Åsidosätter `[ENTRYPOINT]` i ett stegs behållare. | Ingen |
| `env` | [sträng, sträng,...] | Ja | Sträng mat ris i `key=value` format som definierar miljövariablerna för steget. | Ingen |
| `expose` | [sträng, sträng,...] | Ja | Matris med portar som exponeras från behållaren. |  Ingen |
| [`id`](#example-id) | sträng | Ja | Identifierar ett unikt steg i uppgiften. Andra steg i uppgiften kan referera till ett stegs `id`, till exempel för beroende kontroll med `when`.<br /><br />`id` är också namnet på den behållare som körs. Processer som körs i andra behållare i aktiviteten kan referera till `id` som DNS-värdnamn, eller för att komma åt den med Docker-loggar [ID], till exempel. | `acb_step_%d`, där `%d` är det 0-baserade indexet för steget överst i YAML-filen |
| `ignoreErrors` | booleska | Ja | Om du vill markera steget som slutfört oavsett om ett fel uppstod när containern kördes. | `false` |
| `isolation` | sträng | Ja | Behållarens isolerings nivå. | `default` |
| `keep` | booleska | Ja | Anger om stegets behållare ska behållas efter körning. | `false` |
| `network` | objekt | Ja | Identifierar ett nätverk där behållaren körs. | Ingen |
| `ports` | [sträng, sträng,...] | Ja | Matris med portar som publiceras från behållaren till värden. |  Ingen |
| `pull` | booleska | Ja | Om du vill tvinga fram en hämtning av behållaren innan du kör den för att förhindra alla funktioner för cachelagring. | `false` |
| `privileged` | booleska | Ja | Om behållaren ska köras i privilegierat läge. | `false` |
| `repeat` | int | Ja | Antalet försök att upprepa körningen av en behållare. | 0 |
| `retries` | int | Ja | Antalet återförsök som ska göras om en behållare inte kan köras. Ett nytt försök görs bara om slut koden för en behållare är skilt från noll. | 0 |
| `retryDelay` | int (sekunder) | Ja | Fördröjningen i sekunder mellan återförsök för en behållares körning. | 0 |
| `secret` | objekt | Ja | Identifierar en Azure Key Vault hemlig eller [hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). | Ingen |
| `startDelay` | int (sekunder) | Ja | Antal sekunder som en behållares körning ska fördröjas. | 0 |
| `timeout` | int (sekunder) | Ja | Maximalt antal sekunder som ett steg kan utföras innan det avslutas. | 600 |
| [`when`](#example-when) | [sträng, sträng,...] | Ja | Konfigurerar ett stegs beroende av ett eller flera andra steg i aktiviteten. | Ingen |
| `user` | sträng | Ja | Användar namnet eller UID för en behållare | Ingen |
| `workingDirectory` | sträng | Ja | Anger arbets katalogen för ett steg. Som standard skapar ACR-aktiviteter en rot Katalog som arbets katalog. Men om din version har flera steg, kan tidigare steg dela artefakter med senare steg genom att ange samma arbets katalog. | `/workspace` |

### <a name="examples-task-step-properties"></a>Exempel: egenskaper för aktivitets steg

#### <a name="example-id"></a>Exempel: ID

Bygg två avbildningar och indelning av en funktionell test avbildning. Varje steg identifieras av en unik `id` som innehåller andra steg i aktivitets referensen i deras `when` egenskap.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

Egenskapen `when` anger ett stegs beroende av andra steg i uppgiften. Det stöder två parameter värden:

* `when: ["-"]` – indikerar inget beroende av andra steg. Ett steg som anger `when: ["-"]` börjar köras omedelbart och möjliggör samkörning av samtidiga steg.
* `when: ["id1", "id2"]` – anger att steget är beroende av steg med `id` "id1" och `id` "ID2". Det här steget körs inte förrän båda stegen "id1" och "ID2" har slutförts.

Om `when` inte anges i ett steg är det steget beroende av att föregående steg har slutförts i `acr-task.yaml`-filen.

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
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Variabel namnen är vanligt vis själv för klar Ande. Information följer de variabler som används ofta. Från och med YAML version `v1.1.0`kan du använda ett förkortat, fördefinierat [aktivitets-alias](#aliases) i stället för de flesta körnings variabler. I stället för `{{.Run.Registry}}`använder du till exempel `$Registry` alias.

### <a name="runid"></a>Run.ID

Varje körning, via `az acr run`eller utlösare baserad körning av uppgifter som skapats via `az acr task create`, har ett unikt ID. ID: t representerar körningen som körs för närvarande.

Används vanligt vis för att unikt tagga en bild:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

Det fullständigt kvalificerade Server namnet för registret. Används vanligt vis för att allmänt referera till registret där aktiviteten körs.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Kör. RegistryName

Namnet på behållar registret. Används vanligt vis i uppgifts steg som inte kräver ett fullständigt kvalificerat Server namn, till exempel `cmd` steg som kör Azure CLI-kommandon på register.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Kör. datum

Den aktuella UTC-tid då körningen påbörjades.

### <a name="runcommit"></a>Kör. commit

För en uppgift som utlöses av ett genomförande till en GitHub-lagringsplats, COMMIT-ID: t.

### <a name="runbranch"></a>Run.Branch

För en uppgift som utlöses av ett genomförande till en GitHub-lagringsplats, namnet på grenen.

## <a name="aliases"></a>Alias

Från och med `v1.1.0`stöder ACR-aktiviteter alias som är tillgängliga för uppgifts steg när de körs. Alias är liknande för alias (kommando gen vägar) som stöds i bash och vissa andra kommando gränssnitt. 

Med ett alias kan du starta ett kommando eller en grupp med kommandon (inklusive alternativ och fil namn) genom att ange ett enda ord.

ACR-aktiviteter stöder flera fördefinierade alias och även anpassade alias som du skapar.

### <a name="predefined-aliases"></a>Fördefinierade alias

Följande uppgifter alias är tillgängliga för användning i stället för Run- [variabler](#run-variables):

| Alias | Kör variabel |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

I uppgifts steg, skriver du in ett alias med `$`-direktivet, som i det här exemplet:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Bildalias

Vart och ett av följande alias pekar på en stabil avbildning i Microsoft Container Registry (MCR). Du kan referera till var och en av dem i avsnittet `cmd` i en aktivitets fil utan att använda ett direktiv.

| Alias | Bild |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

I följande exempel aktivitet används flera alias för att [Rensa](container-registry-auto-purge.md) bildtaggar som är äldre än 7 dagar i lagrings platsen-`samples/hello-world` i körnings registret:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Anpassat alias

Definiera ett anpassat alias i din YAML-fil och Använd det på det sätt som visas i följande exempel. Ett alias får bara innehålla alfanumeriska tecken. Standard direktivet för att expandera ett alias är `$`s symbolen.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Du kan länka till en fjärran sluten eller lokal YAML-fil för anpassade definitioner av alias. Följande exempel länkar till en YAML-fil i Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

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
