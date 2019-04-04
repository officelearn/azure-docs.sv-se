---
title: Referens för Azure Container Registry uppgifter - YAML
description: Referens för att definiera aktiviteter i YAML för ACR-aktiviteter, inklusive Aktivitetsegenskaper, stegtyper, stegegenskaper och inbyggda variabler.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b2398e7db7ed91dee8d85c0c50058bb15b9f4c7e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894140"
---
# <a name="acr-tasks-reference-yaml"></a>Referera till ACR-uppgifter: YAML

Flera steg aktivitetsdefinitionen i ACR uppgifter innehåller en behållare till Central beräkning primitiv hämtas som fokuserar på att bygga, testa och korrigeringar behållare. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntaxen för YAML-filer som definierar dina aktiviteter med flera steg.

Den här artikeln innehåller en referens för att skapa flera steg aktivitet YAML-filer för ACR-uppgifter. Om du vill ha en introduktion till ACR-aktiviteter finns i den [ACR uppgifter översikt](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-task.yaml filformat

ACR uppgifter stöder flera steg uppgift deklarationen i standard YAML-syntax. Du definierar en uppgift i en YAML-fil. Du kan sedan köra uppgiften manuellt genom att skicka filen till den [az acr kör] [ az-acr-run] kommando. Eller Använd filen för att skapa en uppgift med [az acr uppgift skapa] [ az-acr-task-create] som utlöses automatiskt på en Git bekräftas eller base uppdateringar. Även om den här artikeln handlar om `acr-task.yaml` som filen som innehåller stegen, ACR uppgifter stöder valfritt giltigt namn med en [stöds tillägget](#supported-task-filename-extensions).

Den översta `acr-task.yaml` primitiver är **uppgift egenskaper**, **steg typer**, och **steg egenskaper**:

* [Uppgift egenskaper](#task-properties) gäller för alla steg i hela för körning av aktiviteten. Det finns flera globala egenskaper, inklusive:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Uppgift stegtyper](#task-step-types) representerar typerna av åtgärder som kan utföras i en aktivitet. Det finns tre steg:
  * `build`
  * `push`
  * `cmd`
* [Uppgift stegegenskaper](#task-step-properties) är parametrar som gäller för ett enskilt steg. Det finns flera stegegenskaper, inklusive:
  * `startDelay`
  * `timeout`
  * `when`
  * .. .och många fler.

Grundläggande formatet för en `acr-task.yaml` filen, inklusive vissa gemensamma stegegenskaper följer. När du inte en fullständig representation av alla tillgängliga stegegenskaper eller typer används i steg ger det en snabb översikt över grundläggande filformatet.

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

### <a name="supported-task-filename-extensions"></a>Stöds uppgift filnamnstillägg

ACR-aktiviteter har reserverat flera filnamnstillägg, inklusive `.yaml`, som behandlas som en uppgiftsfil. Alla tillägg *inte* i listan nedan anses av ACR aktiviteter en Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, PS, .cmd, .bat, TS, .js, .php, .py, .rb, .lua

YAML är det enda filformat som stöds för närvarande av ACR-aktiviteter. Andra filnamnstillägg är reserverade för framtida support som möjligt.

## <a name="run-the-sample-tasks"></a>Köra exemplet-uppgifter

Det finns flera exempel aktivitetsfiler som refereras till i de följande avsnitten i den här artikeln. Exempelaktiviteter finns i en offentlig GitHub-lagringsplats [Azure-Samples/acr-uppgifter][acr-tasks]. Du kan köra dem med Azure CLI-kommando [az acr kör][az-acr-run]. Exempelkommandona liknar:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formateringen av exempelkommandona förutsätter att du har konfigurerat ett standard-register i Azure CLI, så att de utelämna den `--registry` parametern. Om du vill konfigurera en standard-registret, använda den [az konfigurera] [ az-configure] med den `--defaults` parametern, som accepterar en `acr=REGISTRY_NAME` värde.

Till exempel med namnet ”myregistry” att konfigurera Azure CLI med en standard-registret:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Egenskaper för aktivitet

Aktivitetsegenskaper ofta ser överst i en `acr-task.yaml` fil och är globala egenskaper som gäller i hela fullständig körningen av uppgiften stegen. Några av de här globala egenskaperna kan åsidosättas i ett enskilt steg.

| Egenskap  | Type | Valfri | Beskrivning | Åsidosättning som stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Ja | Versionen av den `acr-task.yaml` filen som parsas av tjänsten ACR uppgifter. Medan ACR uppgifter strävar efter att underhålla bakåtkompatibilitet, kan det här värdet ACR uppgifter att bibehålla kompatibilitet inom en definierad version. Om inget anges som standard till den senaste versionen. | Nej | Ingen |
| `stepTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder som ett steg kan köras. Om egenskapen har angetts för en aktivitet anger standard `timeout` egenskapen för alla steg. Om den `timeout` egenskapen har angetts på ett steg, åsidosätter egenskapen från aktiviteten. | Ja | 600 (10 minuter) |
| `workingDirectory` | sträng | Ja | Arbetskatalog för behållaren under körning. Om egenskapen har angetts för en aktivitet anger standard `workingDirectory` egenskapen för alla steg. Om anges på ett steg, åsidosätter egenskapen från aktiviteten. | Ja | `$HOME` |
| `env` | [string, string, ...] | Ja |  Matris med strängar i `key=value` format som definierar miljövariabler för uppgiften. Om egenskapen har angetts för en aktivitet anger standard `env` egenskapen för alla steg. Om anges på ett steg, åsidosätter alla miljövariabler som ärvts från uppgiften. | Ingen |
| `secrets` | [secret, secret, ...] | Ja | Matris med [hemlighet](#secret) objekt. | Ingen |
| `networks` | [network, network, ...] | Ja | Matris med [nätverk](#network) objekt. | Ingen |

### <a name="secret"></a>hemlighet

Det hemliga objektet har följande egenskaper.

| Egenskap  | Type | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sträng | Nej | Identifierare för hemlighet. | Ingen |
| `akv` | sträng | Ja | Den hemliga URL för Azure Key Vault (AKV). | Ingen |
| `clientID` | sträng | Ja | Klient-ID för den Användartilldelad hanterad identitet för Azure-resurser. | Ingen |

### <a name="network"></a>nätverk

Nätverksobjektet har följande egenskaper.

| Egenskap  | Type | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | Nej | Namnet på nätverket. | Ingen |
| `driver` | sträng | Ja | Drivrutinen att hantera nätverket. | Ingen |
| `ipv6` | bool | Ja | Om IPv6-nätverk är aktiverat. | `false` |
| `skipCreation` | bool | Ja | Om du vill hoppa över skapandet av nätverket. | `false` |
| `isDefault` | bool | Ja | Om nätverket är ett standardnätverk som medföljer Azure Container Registry | `false` |

## <a name="task-step-types"></a>Steg aktivitetstyper

ACR uppgifter stöder tre stegtyper. Varje stegtyp av har stöd för flera egenskaper som beskrivs i avsnittet för varje stegtyp av.

| Stegtyp av | Beskrivning |
| --------- | ----------- |
| [`build`](#build) | Skapar en behållaravbildning med hjälp av välbekanta `docker build` syntax. |
| [`push`](#push) | Kör en `docker push` av nyligen skapade eller retagged avbildningar till ett behållarregister. Azure Container Registry, övriga privata register och den offentliga Docker Hub stöds. |
| [`cmd`](#cmd) | Kör en behållare som ett kommando med parametrar skickades till behållarens `[ENTRYPOINT]`. Den `cmd` stegtyp har stöd för parametrar som `env`, `detach`, och andra välbekanta `docker run` kommandoalternativ, för att aktivera enhet och funktionstestning med samtidiga behållare körning. |

## <a name="build"></a>Skapa

Skapa en behållaravbildning. Den `build` stegtyp representerar ett flera innehavare, säkert sätt av att köra `docker build` i molnet som en första klassens primitiv hämtas.

### <a name="syntax-build"></a>Syntax: skapa

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Den `build` stegtyp har stöd för parametrarna i följande tabell. Den `build` stegtyp stöder också alla build-alternativ för den [docker build](https://docs.docker.com/engine/reference/commandline/build/) kommandot, till exempel `--build-arg` att ställa in byggning variabler.

| Parameter | Beskrivning | Valfri |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definierar det fullständiga `image:tag` för den skapade avbildningen.<br /><br />Avbildningar som kan användas för inre uppgift verifieringar, till exempel funktionstester, inte alla avbildningar kräver `push` till ett register. Men om du vill instans en avbildning i en körning av aktiviteten, behöver avbildningen ett namn som refererar till.<br /><br />Till skillnad från `az acr build`, ACR-aktiviteter som körs inte tillhandahålla push standardbeteendet. Med ACR åtgärder, standard-scenariot förutsätter vi att möjligheten att skapa, validera och sedan push-överför avbildningen. Se [push](#push) för att du kan också push inbyggda avbildningar. | Ja |
| `-f` &#124; `--file` | Anger den Dockerfile som skickades till `docker build`. Om inte anges, antas Dockerfile som finns i roten av kontexten för standard. Om du vill ange en Dockerfile Skicka filnamnet i förhållande till roten för kontexten. | Ja |
| `context` | Rotkatalogen skickades till `docker build`. Rotkatalogen för varje aktivitet har angetts till en delad [workingDirectory](#task-step-properties), och innehåller roten av den associera Git-klonade katalogen. | Nej |

### <a name="properties-build"></a>Egenskaper: skapa

Den `build` stegtyp stöder följande egenskaper. Hitta information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfri |
| `disableWorkingDirectoryOverride` | bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [string, string, ...] | Valfri |
| `expose` | [string, string, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | bool | Valfri |
| `network` | objekt | Valfri |
| `ports` | [string, string, ...] | Valfri |
| `pull` | bool | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [string, string, ...] | Valfri |
| `workingDirectory` | sträng | Valfri |

### <a name="examples-build"></a>Exempel: skapa

#### <a name="build-image---context-in-root"></a>Skapa avbildningen - kontexten i rot

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Skapa avbildningen - kontexten i underkatalog

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Skicka en eller flera inbyggda eller retagged avbildningar till ett behållarregister. Har stöd för push-överföra till privata register som Azure Container Registry eller till den offentliga Docker Hub.

### <a name="syntax-push"></a>Syntax: push

Den `push` stegtyp stöder en uppsättning bilder. YAML samling syntax har stöd för intern och kapslade format. Push-överföra en enda avbildning visas vanligtvis med infogad syntax:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Använd kapslad syntax för bättre läsbarhet när push-överföra flera bilder:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

Den `push` stegtyp stöder följande egenskaper. Hitta information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [string, string, ...] | Valfri |

### <a name="examples-push"></a>Exempel: push

#### <a name="push-multiple-images"></a>Skicka flera avbildningar

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Skapa, skicka och köra

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Den `cmd` stegtyp kör en behållare.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

Den `cmd` stegtyp stöder följande egenskaper:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfri |
| `disableWorkingDirectoryOverride` | bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [string, string, ...] | Valfri |
| `expose` | [string, string, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | bool | Valfri |
| `network` | objekt | Valfri |
| `ports` | [string, string, ...] | Valfri |
| `pull` | bool | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [string, string, ...] | Valfri |
| `workingDirectory` | sträng | Valfri |

Du hittar information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör hello world-avbildning

Det här kommandot Kör den `hello-world.yaml` aktivitetssfil som refererar till den [hello-world](https://hub.docker.com/_/hello-world/) avbildning på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash-avbildning och echo ”hello world”

Det här kommandot Kör den `bash-echo.yaml` aktivitetssfil som refererar till den [bash](https://hub.docker.com/_/bash/) avbildning på Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Kör specifika bash bildtagg

Om du vill köra en viss Avbildningsversion, ange taggen i den `cmd`.

Det här kommandot Kör den `bash-echo-3.yaml` aktivitetssfil som refererar till den [bash: 3.0](https://hub.docker.com/_/bash/) avbildning på Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Köra anpassade avbildningar

Den `cmd` stegtyp refererar till bilder med hjälp av standard `docker run` format. Bilder som inte inleds med ett register antas kommer från docker.io. I föregående exempel kan lika framställas som:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Genom att använda `docker run` bild referens konventionen `cmd` köra avbildningar från ett privat register och den offentliga Docker Hub. Om du refererar till bilder i samma registret där ACR uppgiften körs, behöver du inte ange några autentiseringsuppgifter för registret.

* Köra en avbildning från ett Azure container registry

    Ersätt `[myregistry]` med namnet på ditt register:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalisera register med ett kör-variabel

    I stället för att hårdkoda ditt registernamn i en `acr-task.yaml` fil, du kan göra det mer portabel med hjälp av en [kör variabeln](#run-variables). Den `Run.Registry` variabeln expanderas vid körning med namnet på registret där aktiviteten körs.

    För att generalisera den föregående aktiviteten så att den fungerar i alla Azure container registry, referera till den [Run.Registry](#runregistry) variabel i avbildningens namn:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Steg Aktivitetsegenskaper

Varje stegtyp av har stöd för flera egenskaper som är lämpliga för typen. I följande tabell definieras alla tillgängliga stegegenskaper. Inte alla stegtyper av stöd för alla egenskaper. Om du vill se vilka egenskaper som är tillgängliga för varje stegtyp av finns i den [cmd](#cmd), [skapa](#build), och [push](#push) steg typ referensavsnitt.

| Egenskap  | Type | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ja | Oavsett om behållaren ska att koppla från när du kör. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ja | Om du vill inaktivera `workingDirectory` åsidosätta funktioner. Använd det här i kombination med `workingDirectory` ha fullständig kontroll över behållarens arbetskatalog. | `false` |
| `entryPoint` | sträng | Ja | Åsidosätter den `[ENTRYPOINT]` för behållare i ett steg. | Ingen |
| `env` | [string, string, ...] | Ja | Matris med strängar i `key=value` format som definierar miljövariabler för steget. | Ingen |
| `expose` | [string, string, ...] | Ja | Matris med portar som visas från behållaren. |  Ingen |
| [`id`](#example-id) | sträng | Ja | Identifierar steget i uppgiften. Andra steg i aktiviteten kan referera till ett steg `id`, t.ex. för beroende kontrollerar med `when`.<br /><br />Den `id` också är namnet på behållaren som körs. Processer som körs i andra behållare i aktiviteten kan referera till den `id` som dess DNS-värdnamn eller för att komma åt den med dockerloggar [id], till exempel. | `acb_step_%d`, där `%d` är 0-baserade indexet för steget uppifrån och ned i YAML-fil |
| `ignoreErrors` | bool | Ja | Om du vill markera steget som lyckas oavsett om ett fel uppstod under körning av behållare. | `false` |
| `isolation` | sträng | Ja | Isoleringsnivå för behållaren. | `default` |
| `keep` | bool | Ja | Om det steget behållare bör hållas efter körningen. | `false` |
| `network` | objekt | Ja | Identifierar ett nätverk som behållaren körs. | Ingen |
| `ports` | [string, string, ...] | Ja | Matris med portar som publiceras från behållaren till värden. |  Ingen |
| `pull` | bool | Ja | Om du vill tvinga en hämtning av behållaren innan du kör det för att förhindra alla funktionssättet för cachelagring. | `false` |
| `privileged` | bool | Ja | Om du vill köra behållaren i privilegierat läge. | `false` |
| `repeat` | int | Ja | Antal försök Upprepa körningen av en behållare. | 0 |
| `retries` | int | Ja | Antal försök att försöka om en behållare kan inte den kan körningen. Ett nytt försök görs bara om en behållare slutkoden är noll. | 0 |
| `retryDelay` | int (sekunder) | Ja | Fördröjning i sekunder mellan återförsök för körning av en behållare. | 0 |
| `secret` | objekt | Ja | Identifierar en Azure Key Vault-hemlighet eller hanterad identitet för Azure-resurser. | Ingen |
| `startDelay` | int (sekunder) | Ja | Antal sekunder att fördröja körningen av en behållare. | 0 |
| `timeout` | int (sekunder) | Ja | Maximalt antal sekunder som ett steg kan köras innan håller på att avslutas. | 600 |
| [`when`](#example-when) | [string, string, ...] | Ja | Konfigurerar ett steg beroende på en eller flera andra steg i aktiviteten. | Ingen |
| `user` | sträng | Ja | Användarnamnet eller UID för en behållare | Ingen |
| `workingDirectory` | sträng | Ja | Anger arbetskatalogen för ett steg. Som standard skapar ACR uppgifter en rotkatalog som arbetskatalogen. Men om din version har flera steg, kan tidigare dela artefakter med senare steg genom att ange samma arbetskatalogen. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exempel: Steg Aktivitetsegenskaper

#### <a name="example-id"></a>Exempel: id

Skapa två avbildningar, förekomst av en funktionstest-avbildning. Varje steg identifieras med ett unikt `id` som refererar till andra steg i aktiviteten i sina `when` egenskapen.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

Den `when` egenskap anger ett steg beroende på andra steg i aktiviteten. Den stöder två parametervärden:

* `when: ["-"]` -Visar inget beroende på andra åtgärder. Ett steg att ange `when: ["-"]` träder i kraft körning omedelbart och aktiverar samtidig steg körning.
* `when: ["id1", "id2"]` -Visar steget är beroende av steg med `id` ”id1” och `id` ”id2”. Det här steget utföras inte förrän både ”id1” och ”id2” steg slutfört.

Om `when` inte har angetts i ett steg steget är beroende av slutförandet av föregående steg i den `acr-task.yaml` filen.

Sekventiella steg körning utan `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekventiella steg körning med `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Build-parallella avbildningar:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallell image build och beroende testning:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Kör variabler

ACR-aktiviteter innehåller en standarduppsättning variabler som är tillgängliga för uppgift när de kör. Dessa variabler kan nås genom att använda formatet `{{.Run.VariableName}}`, där `VariableName` är något av följande:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Run&#46;ID

Varje körning via `az acr run`, eller starta baserat körningen av aktiviteter som skapas med `az acr task create` har ett unikt ID. ID: T representerar körningen som körs.

Används vanligtvis för ett unikt taggar en bild:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Det fullständigt kvalificerade servernamnet för registret. Används vanligtvis för att referera till registret där aktiviteten körs med det allmänna skyddet.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Den aktuella UTC-tiden började för körningen.

## <a name="next-steps"></a>Nästa steg

En översikt över flera steg uppgifter finns i den [köra flera steg skapa, testa och patch uppgifter i ACR uppgifter](container-registry-tasks-multi-step.md).

Enda steg skapar finns i den [ACR uppgifter översikt](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
