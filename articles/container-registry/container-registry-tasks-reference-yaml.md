---
title: Referens för Azure Container Registry uppgifter - YAML
description: Referens för att definiera aktiviteter i YAML för ACR-aktiviteter, inklusive Aktivitetsegenskaper, stegtyper, stegegenskaper och inbyggda variabler.
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: marsma
ms.openlocfilehash: a40c05b2775e32bd0932122245914951d42cf712
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048085"
---
# <a name="acr-tasks-reference-yaml"></a>ACR uppgifter referens: YAML

Flera steg aktivitetsdefinitionen i ACR uppgifter innehåller en behållare till Central beräkning primitiv hämtas som fokuserar på att bygga, testa och korrigeringar behållare. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntaxen för YAML-filer som definierar dina aktiviteter med flera steg.

Den här artikeln innehåller en referens för att skapa flera steg aktivitet YAML-filer för ACR-uppgifter. Om du vill ha en introduktion till ACR-aktiviteter finns i den [ACR uppgifter översikt](container-registry-tasks-overview.md).

> [!IMPORTANT]
> Funktionen flerstegstest uppgifter ACR uppgifter är för närvarande i förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="acr-taskyaml-file-format"></a>ACR-task.yaml filformat

ACR uppgifter stöder flera steg uppgift deklarationen i standard YAML-syntax. Du definierar en uppgift i en YAML-fil som du kan sedan köra manuellt eller har utlösts automatiskt på Git bekräftas eller base uppdateringar. Även om den här artikeln handlar om `acr-task.yaml` som filen som innehåller stegen, ACR uppgifter stöder valfritt giltigt namn med en [stöds tillägget](#supported-task-filename-extensions).

Den översta `acr-task.yaml` primitiver är **uppgift egenskaper**, **steg typer**, och **steg egenskaper**:

* [Uppgift egenskaper](#task-properties) gäller för alla steg i hela för körning av aktiviteten. Det finns tre globala egenskaperna:
  * version
  * stepTimeout
  * totalTimeout
* [Uppgift stegtyper](#task-step-types) representerar typerna av åtgärder som kan utföras i en aktivitet. Det finns tre steg:
  * Skapa
  * Push
  * cmd
* [Uppgift stegegenskaper](#task-step-properties) är parametrar som gäller för ett enskilt steg. Det finns flera stegegenskaper, inklusive:
  * startDelay
  * timeout
  * när
  * .. .och många fler.

Grundläggande formatet för en `acr-task.yaml` filen, inklusive vissa gemensamma stegegenskaper följer. När du inte en fullständig representation av alla tillgängliga stegegenskaper eller typer används i steg ger det en snabb översikt över grundläggande filformatet.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
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

Aktivitetsegenskaper ofta ser överst i en `acr-task.yaml` fil och är globala egenskaper som gäller i hela fullständig körning av aktiviteten. Några av de här globala egenskaperna kan åsidosättas i ett enskilt steg.

| Egenskap  | Typ | Valfri | Beskrivning | Åsidosättning som stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Nej | Versionen av den `acr-task.yaml` filen som parsas av tjänsten ACR uppgifter. Medan ACR uppgifter strävar efter att underhålla bakåtkompatibilitet, kan det här värdet ACR uppgifter att bibehålla kompatibilitet inom en definierad version. | Nej | Ingen |
| `stepTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder som ett steg kan köras. Den här egenskapen kan åsidosättas i ett steg genom att ange steget [timeout](#timeout) egenskapen. | Ja | 600 (10 minuter) |
| `totalTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder som en aktivitet kan köras. Ett ”kör” innehåller körning och slutförande av alla steg i den här aktiviteten om lyckades eller misslyckades. Också inkluderat är utskriftsaktivitet utdata som identifierade bild beroenden och aktivitetsstatus för körning. | Nej | 3600 (1 timme) |

## <a name="task-step-types"></a>Steg aktivitetstyper

ACR uppgifter stöder tre stegtyper. Varje stegtyp av har stöd för flera egenskaper som beskrivs i avsnittet för varje stegtyp av.

| Stegtyp av | Beskrivning |
| --------- | ----------- |
| [`build`](#build) | Skapar en behållaravbildning med hjälp av välbekanta `docker build` syntax. |
| [`push`](#push) | Kör en `docker push` av nyligen skapade eller retagged avbildningar till ett behållarregister. Azure Container Registry, övriga privata register och den offentliga Docker Hub stöds.
| [`cmd`](#cmd) | Kör en behållare som ett kommando med parametrar skickades till behållarens `[ENTRYPOINT]`. Den `cmd` stegtyp har stöd för parametrar som env, koppla bort, och andra välbekanta `docker run` kommandoalternativ, för att aktivera enhet och funktionstestning med samtidiga behållare körning. |

## <a name="build"></a>Skapa

Skapa en behållaravbildning. Den `build` stegtyp representerar ett flera innehavare, säkert sätt av att köra `docker build` i molnet som en första klassens primitiv hämtas.

### <a name="syntax-build"></a>Syntax: skapa

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

Den `build` stegtyp stöder följande parametrar:

| Parameter | Beskrivning | Valfri |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definierar det fullständiga `image:tag` för den skapade avbildningen.<br /><br />Avbildningar som kan användas för inre uppgift verifieringar, till exempel funktionstester, inte alla avbildningar kräver `push` till ett register. Men om du vill instans en avbildning i en körning av aktiviteten, behöver avbildningen ett namn som refererar till.<br /><br />Till skillnad från `az acr build`, ACR-aktiviteter som körs tillhandahåller inte push standardbeteendet. Med ACR åtgärder, standard-scenariot förutsätter vi att möjligheten att skapa, validera och sedan push-överför avbildningen. Se [push](#push) för att du kan också push inbyggda avbildningar. | Ja |
| `-f` &#124; `--file` | Anger den Dockerfile som skickades till `docker build`. Om inte anges, antas Dockerfile som finns i roten av kontexten för standard. Om du vill ange ett alternativ Dockerfile Skicka filnamnet i förhållande till roten för kontexten. | Ja |
| `context` | Rotkatalogen skickades till `docker build`. Rotkatalogen för varje aktivitet har angetts till en delad [workingDirectory](#task-step-properties), och innehåller roten av den associera Git-klonade katalogen. | Nej |

### <a name="properties-build"></a>Egenskaper: skapa

Den `build` stegtyp stöd för följande egenskaper som du kan hitta information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `detach` | Bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | Bool | Valfri |
| `keep` | Bool | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |
| `workingDirectory` | sträng | Valfri |

### <a name="examples-build"></a>Exempel: skapa

#### <a name="build-image---context-in-root"></a>Skapa avbildningen - kontexten i rot

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Skapa avbildningen - kontexten i underkatalog

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Push

Skicka en eller flera inbyggda eller retagged avbildningar till ett behållarregister. Har stöd för push-överföra till privata register som Azure Container Registry eller till den offentliga Docker Hub.

### <a name="syntax-push"></a>Syntax: push

Den `push` stegtyp stöder en uppsättning bilder. YAML samling syntax har stöd för intern och kapslade format. Push-överföra en enda avbildning visas vanligtvis med infogad syntax:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Använd kapslad syntax för bättre läsbarhet när push-överföra flera bilder:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

Den `push` stegtyp stöder följande egenskaper. Du hittar information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `env` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | Bool | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |

### <a name="examples-push"></a>Exempel: push

#### <a name="push-multiple-images"></a>Skicka flera avbildningar

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Skapa, skicka och köra

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Den `cmd` stegtyp kör en behållare.

### <a name="syntax-cmd"></a>Syntax: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

Den `cmd` stegtyp stöder följande egenskaper:

| | | |
| -------- | ---- | -------- |
| `detach` | Bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng,...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | Bool | Valfri |
| `keep` | Bool | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng,...] | Valfri |
| `workingDirectory` | sträng | Valfri |

Du hittar information om de här egenskaperna i den [steg Aktivitetsegenskaper](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör hello world-avbildning

Det här kommandot Kör den `hello-world.yaml` aktivitetssfil som refererar till den [hello-world](https://hub.docker.com/_/hello-world/) avbildning på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash-avbildning och echo ”hello world”

Det här kommandot Kör den `bash-echo.yaml` aktivitetssfil som refererar till den [bash](https://hub.docker.com/_/bash/) avbildning på Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Kör specifika bash bildtagg

Om du vill köra en viss Avbildningsversion, ange taggen i den `cmd`.

Det här kommandot Kör den `bash-echo-3.yaml` aktivitetssfil som refererar till den [bash: 3.0](https://hub.docker.com/_/bash/) avbildning på Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Köra anpassade avbildningar

Den `cmd` stegtyp refererar till bilder med hjälp av standard `docker run` format. Bilder som inte inleds med ett register antas kommer från docker.io. I föregående exempel kan lika framställas som:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

Genom att använda `docker run` bild referens konventionen `cmd` kan köra avbildningar som finns i ett privat register eller den offentliga Docker Hub. Om du refererar till bilder i samma registret där ACR uppgiften körs, behöver du inte ange några autentiseringsuppgifter för registret.

* Köra en avbildning som finns i ett Azure container registry

    Ersätt `[myregistry]` med namnet på ditt register:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Generalisera register med ett kör-variabel

    I stället för att hårdkoda ditt registernamn i en `acr-task.yaml` fil, du kan göra det mer portabel med hjälp av en [kör variabeln](#run-variables). Den `Run.Registry` variabeln expanderas vid körning med namnet på registret där aktiviteten körs.

    För att generalisera den föregående aktiviteten så att den fungerar i alla Azure container registry, referera till den [Run.Registry](#runregistry) variabel i avbildningens namn:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Steg Aktivitetsegenskaper

Varje stegtyp av har stöd för flera egenskaper som är lämpliga för typen. I följande tabell definieras alla tillgängliga stegegenskaper. Inte alla stegtyper av stöd för alla egenskaper. Om du vill se vilka egenskaper som är tillgängliga för varje stegtyp av finns i den [cmd](#cmd), [skapa](#build), och [push](#push) steg typ referensavsnitt.

| Egenskap  | Typ | Valfri | Beskrivning |
| -------- | ---- | -------- | ----------- |
| `detach` | Bool | Ja | Oavsett om behållaren ska att koppla från när du kör. |
| `entryPoint` | sträng | Ja | Åsidosätter den `[ENTRYPOINT]` för behållare i ett steg. |
| `env` | [sträng, sträng,...] | Ja | Matris med strängar i `key=value` format som definierar miljövariabler för steget. |
| [`id`](#example-id) | sträng | Ja | Identifierar steget i uppgiften. Andra steg i aktiviteten kan referera till ett steg `id`, t.ex. för beroende kontrollerar med `when`.<br /><br />Den `id` också är namnet på behållaren som körs. Processer som körs i andra behållare i aktiviteten kan referera till den `id` som dess DNS-värdnamn eller för att komma åt den med dockerloggar [id], till exempel. |
| `ignoreErrors` | Bool | Ja | När värdet `true`, steget markeras som slutförda oavsett om ett fel uppstod under körningen. Standard: `false`. |
| `keep` | Bool | Ja | Om det steget behållare bör hållas efter körningen. |
| `startDelay` | int (sekunder) | Ja | Antal sekunder att fördröja körningen för ett steg. |
| `timeout` | int (sekunder) | Ja | Maximalt antal sekunder som ett steg kan köras innan håller på att avslutas. |
| [`when`](#example-when) | [sträng, sträng,...] | Ja | Konfigurerar ett steg beroende på en eller flera andra steg i aktiviteten. |
| `workingDirectory` | sträng | Ja | Anger arbetskatalogen för ett steg. Som standard skapar ACR uppgifter en rotkatalog som arbetskatalogen. Men om din version har flera steg, kan tidigare dela artefakter med senare steg genom att ange samma arbetskatalogen. |

### <a name="examples-task-step-properties"></a>Exempel: Steg Aktivitetsegenskaper

#### <a name="example-id"></a>Exempel: id

Skapa två avbildningar, förekomst av en funktionstest-avbildning. Varje steg identifieras med ett unikt `id` som refererar till andra steg i aktiviteten i sina `when` egenskapen.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

Den `when` egenskap anger ett steg beroende på andra steg i aktiviteten. Den stöder två parametervärden:

* `when: ["-"]` -Visar inget beroende på andra åtgärder. Ett steg att ange `when: ["-"]` träder i kraft körning omedelbart och aktiverar samtidig steg körning.
* `when: ["id1", "id2"]` -Visar steget är beroende av steg med `id` ”id1” och `id` ”id2”. Det här steget utföras inte förrän både ”id1” och ”id2” steg slutfört.

Om `when` inte har angetts i ett steg steget är beroende av slutförandet av föregående steg i den `acr-task.yaml` filen.

Sekventiella steg körning utan `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekventiella steg körning med `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Build-parallella bild:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallell image build och beroende testning:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Kör variabler

ACR-aktiviteter innehåller en standarduppsättning variabler som är tillgängliga för uppgift när de kör. Dessa variabler kan nås genom att använda formatet `{{.Run.VariableName}}`, där `VariableName` är något av följande:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Kör&#46;ID

Varje körning via `az acr run`, eller starta baserat körningen av aktiviteter som skapas med `az acr task create` har ett unikt ID. ID: T representerar körningen som körs.

Används vanligtvis för ett unikt taggar en bild:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Det fullständigt kvalificerade servernamnet för registret. Används vanligtvis för att referera till registret där aktiviteten körs med det allmänna skyddet.

```yaml
version: 1.0-preview-1
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
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure