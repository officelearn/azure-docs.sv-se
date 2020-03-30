---
title: YAML-referens - ACR-uppgifter
description: Referens för att definiera uppgifter i YAML för ACR-uppgifter, inklusive aktivitetsegenskaper, stegtyper, stegegenskaper och inbyggda variabler.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246986"
---
# <a name="acr-tasks-reference-yaml"></a>REFERENS FÖR ACR-uppgifter: YAML

Aktivitetsdefinition i flera steg i ACR-uppgifter ger en behållare-centrerad beräkning primitiva fokuserat på att bygga, testa och lappa behållare. Den här artikeln beskriver kommandon, parametrar, egenskaper och syntax för YAML-filer som definierar dina flerstegsuppgifter.

Den här artikeln innehåller referens för att skapa YAML-filer i flera steg för ACR-uppgifter. Om du vill ha en introduktion till ACR-uppgifter läser du [översikten ÖVER ACR-uppgifter](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml-filformat

ACR-uppgifter stöder uppgiftsdeklaration i flera steg i standard-YAML-syntax. Du definierar stegen för en aktivitet i en YAML-fil. Du kan sedan köra aktiviteten manuellt genom att skicka filen till kommandot [az acr run.][az-acr-run] Du kan också använda filen för att skapa en uppgift med [az acr-aktivitetsskapande][az-acr-task-create] som utlöses automatiskt på en Git-commit- eller basavbildningsuppdatering. Även om den `acr-task.yaml` här artikeln refererar till som den fil som innehåller stegen, stöder ACR-uppgifter alla giltiga filnamn med ett [tillägg som stöds](#supported-task-filename-extensions).

Primitiverna på `acr-task.yaml` den översta nivån är **aktivitetsegenskaper,** **stegtyper**och **stegegenskaper:**

* [Aktivitetsegenskaper](#task-properties) gäller för alla steg under hela aktivitetskörningen. Det finns flera globala aktivitetsegenskaper, inklusive:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Uppgiftsstegstyper](#task-step-types) representerar de typer av åtgärder som kan utföras i en aktivitet. Det finns tre stegtyper:
  * `build`
  * `push`
  * `cmd`
* [Egenskaper för aktivitetssteg](#task-step-properties) är parametrar som gäller för ett enskilt steg. Det finns flera stegegenskaper, inklusive:
  * `startDelay`
  * `timeout`
  * `when`
  * ... och många fler.

Basformatet för `acr-task.yaml` en fil, inklusive några vanliga stegegenskaper, följer. Även om det inte finns en uttömmande representation av alla tillgängliga stegegenskaper eller stegtypsanvändning, ger den en snabb översikt över det grundläggande filformatet.

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

### <a name="supported-task-filename-extensions"></a>Tillägg för aktivitetsfilnamn som stöds

ACR-uppgifter har reserverat flera `.yaml`filnamnstillägg, inklusive , som det ska bearbetas som en uppgiftsfil. Varje tillägg *som inte* finns i följande lista anses av ACR-uppgifter vara en Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML är det enda filformat som för närvarande stöds av ACR-uppgifter. De andra filnamnstilläggen är reserverade för eventuellt framtida stöd.

## <a name="run-the-sample-tasks"></a>Köra exempelaktiviteterna

Det finns flera exempel på uppgiftsfiler som refereras i följande avsnitt i den här artikeln. Exempeluppgifterna finns i en offentlig [GitHub-databas, Azure-Samples/acr-uppgifter][acr-tasks]. Du kan köra dem med Azure CLI-kommandot [az acr run][az-acr-run]. Exempelkommandona liknar:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formateringen av exempelkommandona förutsätter att du har konfigurerat ett standardregister i Azure `--registry` CLI, så de utelämnar parametern. Om du vill konfigurera ett standardregister använder `--defaults` du kommandot az `acr=REGISTRY_NAME` [configure][az-configure] med parametern, som accepterar ett värde.

Om du till exempel vill konfigurera Azure CLI med standardregistret "mitt register":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Egenskaper för aktivitet

Aktivitetsegenskaper visas vanligtvis överst i en `acr-task.yaml` fil och är globala egenskaper som gäller under hela körningen av aktivitetsstegen. Vissa av dessa globala egenskaper kan åsidosättas inom ett enskilt steg.

| Egenskap | Typ | Valfri | Beskrivning | Åsidosätt stöds | Standardvärde |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sträng | Ja | Den version `acr-task.yaml` av filen som tolkas av tjänsten ACR-uppgifter. Acr-uppgifter strävar efter att upprätthålla bakåtkompatibilitet, men det här värdet gör det möjligt för ACR-uppgifter att upprätthålla kompatibiliteten i en definierad version. Om det inte anges, som standard till den senaste versionen. | Inga | Inget |
| `stepTimeout` | int (sekunder) | Ja | Det maximala antalet sekunder ett steg kan köras. Om egenskapen anges för en aktivitet anges `timeout` standardegenskapen för alla steg. Om `timeout` egenskapen anges i ett steg åsidosätts egenskapen som tillhandahålls av aktiviteten. | Ja | 600 (10 minuter) |
| `workingDirectory` | sträng | Ja | Behållarens arbetskatalog under körning. Om egenskapen anges för en aktivitet anges `workingDirectory` standardegenskapen för alla steg. Om det anges i ett steg åsidosätts egenskapen som tillhandahålls av aktiviteten. | Ja | `/workspace` |
| `env` | [sträng, sträng, ...] | Ja |  Matris med strängar `key=value` i format som definierar miljövariablerna för aktiviteten. Om egenskapen anges för en aktivitet anges `env` standardegenskapen för alla steg. Om det anges i ett steg åsidosätts alla miljövariabler som ärvs från aktiviteten. | Inget |
| `secrets` | [hemlig, hemlighet, ...] | Ja | Matris med [hemliga](#secret) objekt. | Inget |
| `networks` | [nätverk, nätverk, ...] | Ja | Matris med [nätverksobjekt.](#network) | Inget |

### <a name="secret"></a>hemlighet

Det hemliga objektet har följande egenskaper.

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sträng | Inga | Identifieraren för hemligheten. | Inget |
| `keyvault` | sträng | Ja | Hemlig URL för Azure Key Vault. | Inget |
| `clientID` | sträng | Ja | Klient-ID för den [användartilldelade hanterade identiteten](container-registry-tasks-authentication-managed-identity.md) för Azure-resurser. | Inget |

### <a name="network"></a>network

Nätverksobjektet har följande egenskaper.

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sträng | Inga | Namnet på nätverket. | Inget |
| `driver` | sträng | Ja | Drivrutinen för att hantera nätverket. | Inget |
| `ipv6` | bool | Ja | Om IPv6-nätverk är aktiverat. | `false` |
| `skipCreation` | bool | Ja | Om du vill hoppa över nätverksskapande. | `false` |
| `isDefault` | bool | Ja | Om nätverket är ett standardnätverk som medföljer Azure Container Registry | `false` |

## <a name="task-step-types"></a>Stegtyper för aktivitet

ACR-uppgifter stöder tre stegtyper. Varje stegtyp stöder flera egenskaper som beskrivs i avsnittet för varje stegtyp.

| Stegtyp | Beskrivning |
| --------- | ----------- |
| [`build`](#build) | Skapar en behållaravbildning med välbekant `docker build` syntax. |
| [`push`](#push) | Kör en `docker push` av nybyggda eller retaggade avbildningar till ett behållarregister. Azure Container Registry, andra privata register och den offentliga Docker Hub stöds. |
| [`cmd`](#cmd) | Kör en behållare som ett kommando, med `[ENTRYPOINT]`parametrar som skickas till behållarens . Stegtypen `cmd` stöder `env`parametrar `detach`som , `docker run` och andra välbekanta kommandoalternativ, vilket möjliggör enhets- och funktionstestning med samtidig körning av behållare. |

## <a name="build"></a>skapa

Skapa en behållaravbildning. Stegtypen `build` representerar ett säkert sätt `docker build` att köra i molnet som en förstklassig primitiv.

### <a name="syntax-build"></a>Syntax: bygg

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Stegtypen `build` stöder parametrarna i följande tabell. Stegtypen `build` stöder också alla byggalternativ för [docker](https://docs.docker.com/engine/reference/commandline/build/) build-kommandot, till exempel `--build-arg` för att ange byggtidsvariabler.

| Parameter | Beskrivning | Valfri |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Definierar den `image:tag` fullt kvalificerade av den inbyggda avbildningen.<br /><br />Eftersom avbildningar kan användas för validering av inre uppgift, `push` till exempel funktionstester, behöver inte alla avbildningar ett register. Om du vill ange en bild i en aktivitetskörning behöver bilden dock ett namn som referens.<br /><br />Till `az acr build`skillnad från ger du inte standardtryckbeteende som körs med ACR-uppgifter. Med ACR-uppgifter förutsätter standardscenariot möjligheten att skapa, validera och sedan skicka en avbildning. Se [push](#push) för hur du eventuellt kan driva byggda bilder. | Ja |
| `-f`&#124;`--file` | Anger den Dockerfile `docker build`som skickades till . Om inget anges antas standardläget Dockerfile i kontextens rot. Om du vill ange en Dockerfile skickar du filnamnet i förhållande till kontextens rot. | Ja |
| `context` | Rotkatalogen gick `docker build`till . Rotkatalogen för varje aktivitet är inställd på en delad [workingDirectory](#task-step-properties)och innehåller roten till den associerade Git-klonade katalogen. | Inga |

### <a name="properties-build"></a>Egenskaper: bygg

Stegtypen `build` stöder följande egenskaper. Hitta information om dessa egenskaper i avsnittet [Egenskaper för uppgiftssteg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfri |
| `disableWorkingDirectoryOverride` | bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng, ...] | Valfri |
| `expose` | [sträng, sträng, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | bool | Valfri |
| `network` | objekt | Valfri |
| `ports` | [sträng, sträng, ...] | Valfri |
| `pull` | bool | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng, ...] | Valfri |
| `workingDirectory` | sträng | Valfri |

### <a name="examples-build"></a>Exempel: bygg

#### <a name="build-image---context-in-root"></a>Skapa bild - kontext i rot

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Skapa bild - kontext i underkatalog

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Tryck

Skicka en eller flera inbyggda eller taggade avbildningar till ett behållarregister. Stöder att skicka till privata register som Azure Container Registry eller till den offentliga Docker Hub.

### <a name="syntax-push"></a>Syntax: push

Stegtypen `push` stöder en samling bilder. SYNTAX FÖR YAML-samling stöder infogade och kapslade format. Att trycka på en enskild bild representeras vanligtvis med hjälp av infogad syntax:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

För ökad läsbarhet använder du kapslad syntax när du trycker på flera bilder:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Egenskaper: push

Stegtypen `push` stöder följande egenskaper. Hitta information om dessa egenskaper i avsnittet [Egenskaper för uppgiftssteg](#task-step-properties) i den här artikeln.

| | | |
| -------- | ---- | -------- |
| `env` | [sträng, sträng, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng, ...] | Valfri |

### <a name="examples-push"></a>Exempel: push

#### <a name="push-multiple-images"></a>Push flera bilder

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Skapa, pusha och kör

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Stegtypen `cmd` kör en behållare.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Egenskaper: cmd

Stegtypen `cmd` stöder följande egenskaper:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Valfri |
| `disableWorkingDirectoryOverride` | bool | Valfri |
| `entryPoint` | sträng | Valfri |
| `env` | [sträng, sträng, ...] | Valfri |
| `expose` | [sträng, sträng, ...] | Valfri |
| `id` | sträng | Valfri |
| `ignoreErrors` | bool | Valfri |
| `isolation` | sträng | Valfri |
| `keep` | bool | Valfri |
| `network` | objekt | Valfri |
| `ports` | [sträng, sträng, ...] | Valfri |
| `pull` | bool | Valfri |
| `repeat` | int | Valfri |
| `retries` | int | Valfri |
| `retryDelay` | int (sekunder) | Valfri |
| `secret` | objekt | Valfri |
| `startDelay` | int (sekunder) | Valfri |
| `timeout` | int (sekunder) | Valfri |
| `when` | [sträng, sträng, ...] | Valfri |
| `workingDirectory` | sträng | Valfri |

Du hittar information om dessa egenskaper i avsnittet Egenskaper för [aktivitetssteg](#task-step-properties) i den här artikeln.

### <a name="examples-cmd"></a>Exempel: cmd

#### <a name="run-hello-world-image"></a>Kör hello-world bild

Det här kommandot `hello-world.yaml` kör uppgiftsfilen, som refererar till [hello-world-avbildningen](https://hub.docker.com/_/hello-world/) på Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Kör bash bild och eko "hello world"

Det här kommandot `bash-echo.yaml` kör aktivitetsfilen, som refererar till bash-avbildningen på Docker Hub. [bash](https://hub.docker.com/_/bash/)

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Kör specifik bash-bildtagg

Om du vill köra en viss `cmd`bildversion anger du taggen i .

Det här kommandot `bash-echo-3.yaml` kör aktivitetsfilen, som refererar till [bash:3.0-avbildningen](https://hub.docker.com/_/bash/) på Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Kör anpassade bilder

Stegtypen `cmd` refererar till `docker run` bilder med standardformatet. Avbildningar som inte föregås av ett register antas komma från docker.io. Det föregående exemplet kan också representeras som:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Genom att `docker run` använda standardkonventionen `cmd` för bildreferens kan du köra avbildningar från alla privata register eller den offentliga Docker Hub. Om du refererar till avbildningar i samma register där ACR-aktiviteten körs behöver du inte ange några registerautentiseringsuppgifter.

* Kör en avbildning som kommer från ett Azure-behållarregister. I följande exempel förutsätts att `myregistry`du har ett `myimage:mytag`register med namnet och en anpassad avbildning .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalisera registerreferensen med en run-variabel eller ett alias

    I stället för att hårdkoda `acr-task.yaml` registernamnet i en fil kan du göra det mer portabelt med hjälp av en [run variabel](#run-variables) eller [alias](#aliases). Variabeln `Run.Registry` `$Registry` eller aliaset expanderas vid körning till namnet på det register där aktiviteten körs.

    Om du till exempel vill generalisera föregående uppgift så att den fungerar i alla Azure-behållarregister refererar du till $Registry variabeln i avbildningsnamnet:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Egenskaper för aktivitetssteg

Varje stegtyp stöder flera egenskaper som är lämpliga för dess typ. I följande tabell definieras alla tillgängliga stegegenskaper. Alla stegtyper stöder inte alla egenskaper. Information om vilka av dessa egenskaper som är tillgängliga för varje stegtyp finns i referensavsnitten [cmd,](#cmd) [build](#build)och [push](#push) step type.

| Egenskap | Typ | Valfri | Beskrivning | Standardvärde |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ja | Om behållaren ska tas bort när den körs. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ja | Om åsidosättningsfunktioner ska inaktiveras. `workingDirectory` Använd detta i `workingDirectory` kombination med att ha fullständig kontroll över behållarens arbetskatalog. | `false` |
| `entryPoint` | sträng | Ja | Åsidosätter `[ENTRYPOINT]` behållaren för ett steg. | Inget |
| `env` | [sträng, sträng, ...] | Ja | Matris med strängar `key=value` i format som definierar miljövariablerna för steget. | Inget |
| `expose` | [sträng, sträng, ...] | Ja | Matris med portar som exponeras från behållaren. |  Inget |
| [`id`](#example-id) | sträng | Ja | Identifierar steget i uppgiften unikt. Andra steg i aktiviteten kan referera `id`till ett steg, `when`till exempel för sambandskontroll med .<br /><br />Den `id` är också den löpande behållarens namn. Processer som körs i andra behållare `id` i aktiviteten kan referera till som dess DNS-värdnamn, eller för att komma åt det med docker loggar [id], till exempel. | `acb_step_%d`, `%d` var är det 0-baserade indexet för steget uppifrån och ned i YAML-filen |
| `ignoreErrors` | bool | Ja | Om steget ska markeras som framgångsrikt oavsett om ett fel uppstod under körningen av behållaren. | `false` |
| `isolation` | sträng | Ja | Isoleringsnivån för behållaren. | `default` |
| `keep` | bool | Ja | Om trappstegets behållare ska behållas efter körningen. | `false` |
| `network` | objekt | Ja | Identifierar ett nätverk där behållaren körs. | Inget |
| `ports` | [sträng, sträng, ...] | Ja | Matris med portar som publiceras från behållaren till värden. |  Inget |
| `pull` | bool | Ja | Om du vill tvinga fram ett drag av behållaren innan den körs för att förhindra cachelagringsbeteende. | `false` |
| `privileged` | bool | Ja | Om behållaren ska köras i privilegierat läge. | `false` |
| `repeat` | int | Ja | Antalet försök för att upprepa körningen av en behållare. | 0 |
| `retries` | int | Ja | Antalet försök att försöka om en behållare misslyckas med körningen. Ett nytt försök görs bara om en behållares avslutningskod inte är noll. | 0 |
| `retryDelay` | int (sekunder) | Ja | Fördröjningen i sekunder mellan återförsöken av en behållares körning. | 0 |
| `secret` | objekt | Ja | Identifierar en hemlig azure key vault-hemlighet eller [hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). | Inget |
| `startDelay` | int (sekunder) | Ja | Antal sekunder för att fördröja körningen av en behållare. | 0 |
| `timeout` | int (sekunder) | Ja | Maximalt antal sekunder ett steg kan köras innan det avslutas. | 600 |
| [`when`](#example-when) | [sträng, sträng, ...] | Ja | Konfigurerar ett stegs samband med ett eller flera andra steg i aktiviteten. | Inget |
| `user` | sträng | Ja | Användarnamnet eller UID för en behållare | Inget |
| `workingDirectory` | sträng | Ja | Anger arbetskatalogen för ett steg. Som standard skapar ACR-uppgifter en rotkatalog som arbetskatalog. Men om din version har flera steg kan tidigare steg dela artefakter med senare steg genom att ange samma arbetskatalog. | `/workspace` |

### <a name="examples-task-step-properties"></a>Exempel: Egenskaper för aktivitetssteg

#### <a name="example-id"></a>Exempel: id

Skapa två bilder, instancing en funktionell testbild. Varje steg identifieras med `id` en unik som andra `when` steg i aktivitetsreferensen i egenskapen.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exempel: när

Egenskapen `when` anger ett stegs samband med andra steg i aktiviteten. Den stöder två parametervärden:

* `when: ["-"]`- Anger inget beroende av andra steg. Ett steg `when: ["-"]` som anger börjar köras omedelbart och möjliggör samtidig körning av steg.
* `when: ["id1", "id2"]`- Anger att steget är `id` beroende av steg `id` med "id1" och "id2". Det här steget körs inte förrän både "id1" och "id2" steg slutförs.

Om `when` inte anges i ett steg är det steget beroende av `acr-task.yaml` att föregående steg i filen har slutförts.

Sekventiell stegkörning `when`utan:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sekventiell stegkörning `when`med :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallella bilder bygga:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallell bilduppbyggnad och beroende testning:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Kör variabler

ACR-uppgifter innehåller en standarduppsättning variabler som är tillgängliga för aktivitetssteg när de körs. Dessa variabler kan nås med `{{.Run.VariableName}}`hjälp `VariableName` av formatet , där är något av följande:

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

Variabeln namnger är allmänt själv-förklara. Detaljer följer för vanliga variabler. Från och med `v1.1.0`YAML-versionen kan du använda ett förkortat, fördefinierat [aktivitetsalias](#aliases) i stället för de flesta körningsvariabler. I stället `{{.Run.Registry}}`för använder du `$Registry` till exempel aliaset.

### <a name="runid"></a>Run.ID

Varje körning, `az acr run`genom eller utlösa baserad `az acr task create`körning av uppgifter som skapats genom , har ett unikt ID. ID:et representerar körningen som körs för närvarande.

Används vanligtvis för att unikt tagga en bild:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

Registrets fullständigt kvalificerade servernamn. Används vanligtvis för att allmänt referera till registret där aktiviteten körs.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Kör.RegisterNamn

Namnet på behållarregistret. Används vanligtvis i aktivitetssteg som inte kräver ett fullständigt `cmd` kvalificerat servernamn, till exempel steg som kör Azure CLI-kommandon i register.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Kör.Datum

Den aktuella UTC-tiden körningen började.

### <a name="runcommit"></a>Kör.Commit

För en uppgift som utlöses av en commit till en GitHub-databas, commit-identifieraren.

### <a name="runbranch"></a>Kör.Gren

För en aktivitet som utlöses av en commit till en GitHub-databas, grennamnet.

## <a name="aliases"></a>Alias

Från `v1.1.0`och med stöder ACR-uppgifter alias som är tillgängliga för aktivitetssteg när de körs. Alias liknar i konceptet alias (kommandogenvägar) som stöds i bash och några andra kommandoskal. 

Med ett alias kan du starta valfritt kommando eller en grupp med kommandon (inklusive alternativ och filnamn) genom att skriva ett enda ord.

ACR-uppgifter stöder flera fördefinierade alias och även anpassade alias som du skapar.

### <a name="predefined-aliases"></a>Fördefinierade alias

Följande aktivitetsalias kan användas i stället för [körningsvariabler:](#run-variables)

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

I uppgiftssteg, föregå ett `$` alias med direktivet, som i det här exemplet:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Bildalias

Var och en av följande alias pekar på en stabil avbildning i MCR (Microsoft Container Registry). Du kan referera till var `cmd` och en av dem i avsnittet i en uppgiftsfil utan att använda ett direktiv.

| Alias | Bild |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

I följande exempelaktivitet används flera alias för att [rensa](container-registry-auto-purge.md) bildtaggar som är äldre än 7 dagar i korrigeringsfilen `samples/hello-world` i körningsregistret:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Anpassat alias

Definiera ett anpassat alias i YAML-filen och använd det som visas i följande exempel. Ett alias kan bara innehålla alfanumeriska tecken. Standarddirektivet för att expandera `$` ett alias är tecknet.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Du kan länka till en fjärr- eller lokal YAML-fil för anpassade aliasdefinitioner. I följande exempel länkar du till en YAML-fil i Azure blob storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Nästa steg

En översikt över flera steg-aktiviteter finns i [utföra flerstegs-, test- och korrigeringsuppgifter i ACR-uppgifter](container-registry-tasks-multi-step.md).

För enstegsversioner finns i [översikten ACR-uppgifter](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
