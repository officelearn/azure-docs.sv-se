---
title: Självstudiekurs - ACR-uppgift i flera steg
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry Task för att automatiskt utlösa ett arbetsflöde i flera steg för att skapa, köra och skicka behållaravbildningar i molnet när du genomför källkod till en Git-databas.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ff32b3095638af6b2b246b99a5dc9219e0020782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78402302"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Självstudiekurs: Kör ett arbetsflöde för behållare i flera steg i molnet när du genomför källkod

Förutom en [snabb uppgift](container-registry-tutorial-quick-task.md)stöder ACR-uppgifter flera steg, flerbehållaresbaserade arbetsflöden som automatiskt kan utlösa när du genomför källkoden till en Git-databas. 

I den här självstudien får du lära dig hur du använder exempel YAML-filer för att definiera flerstegsuppgifter som skapar, kör och skickar en eller flera behållaravbildningar till ett register när du genomför källkod. Om du vill skapa en uppgift som bara automatiserar en enda avbildningsversion om kodkonsage läser du [Självstudiekurs: Automatisera behållaravbildningsversioner i molnet när du genomför källkoden](container-registry-tutorial-build-task.md). En översikt över ACR-uppgifter finns i [Automatisera os och ramkorrigering med ACR-uppgifter](container-registry-tasks-overview.md),

I de här självstudierna har du

> [!div class="checklist"]
> * Definiera en aktivitet i flera steg med hjälp av en YAML-fil
> * Skapa en uppgift
> * Du kan också lägga till autentiseringsuppgifter i uppgiften för att aktivera åtkomst till ett annat register
> * Testa uppgiften
> * Visa status för aktivitet
> * Utlösa uppgiften med en kodincheckning

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-task.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt måste du ha Azure CLI version **2.0.62** eller senare installerat och inloggad med [az-inloggning][az-login]. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Skapa en aktivitet i flera steg

Nu när du har slutfört stegen som krävs för att aktivera ACR-uppgifter för att läsa commit-status och skapa webhooks i en databas, skapa en flerstegsuppgift som utlöser att bygga, köra och trycka på en behållaravbildning.

### <a name="yaml-file"></a>YAML-fil

Du definierar stegen för en aktivitet i flera steg i en [YAML-fil](container-registry-tasks-reference-yaml.md). Det första exemplet med flera steg för den `taskmulti.yaml`här självstudien definieras i filen , som ligger i roten till den GitHub-repo som du klonade:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Den här aktiviteten i flera steg gör följande:

1. Kör `build` ett steg för att skapa en avbildning från Dockerfile i arbetskatalogen. Avbildningen `Run.Registry`är inriktad på registret där aktiviteten körs och taggas med ett unikt ACR-aktivitetskörnings-ID. 
1. Kör `cmd` ett steg för att köra avbildningen i en tillfällig behållare. Det här exemplet startar en tidskrävande behållare i bakgrunden och returnerar behållar-ID:t och stoppar sedan behållaren. I ett verkligt scenario kan du inkludera steg för att testa behållaren som körs för att säkerställa att den körs korrekt.
1. I `push` ett steg, skjuter avbildningen som byggdes till kör registret.

### <a name="task-command"></a>Kommandot Uppgift

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i dessa miljövariabler måste du manuellt ersätta varje värde var det än visas i exempelkommandona.

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Skapa nu uppgiften genom att köra följande kommando för [att skapa a-aktiviteter:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Den här aktiviteten anger att varje tidskod har bekräftats `--context`till *huvudgrenen* i databasen som anges av , kör ACR-uppgifter aktiviteten i flera steg från koden i den grenen. YAML-filen som `--file` anges av från databasroten definierar stegen. 

Utdata från kommandot [az acr task create][az-acr-task-create] liknar följande:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet i flera steg

Om du vill testa aktiviteten i flera steg utlöser du den manuellt genom att köra kommandot [az acr-aktivitetskörning:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Utdata visar förloppet för att köra vart och ett av aktivitetsstegen. Utdata nedan kondenseras för att visa viktiga steg.

```output
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Utlösa en version med en incheckning

Nu när du har testat denna uppgift genom att köra den manuellt utlöser du den automatiskt med en ändring i källkoden.

Kontrollera först att du är i den katalog som innehåller din lokala klon av [lagringsplatsen][sample-repo]:

```console
cd acr-build-helloworld-node
```

Sedan kör du följande kommandon för att skapa, checka in och skicka en ny fil till din förgrening av lagringsplatsen på GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Du kan bli ombedd att ange dina GitHub-autentiseringsuppgifter när du kör kommandot `git push`. Ange ditt GitHub-användarnamn och din personliga åtkomsttoken som du skapade tidigare för lösenordet.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

När du har pushat ett åtagande till din databas, webhook skapas av ACR Uppgifter bränder och startar uppgiften i Azure Container Registry. Visa loggarna för den uppgift som körs för att kontrollera och övervaka versionsförloppet:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Utdata liknar följande och visar den uppgift som körs för närvarande (eller som kördes senast):

```output
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Versionslista

Om du vill se en lista över de uppgiftskörningar som ACR Tasks har slutfört för ditt register kör du kommandot [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Utdatan från kommandot ska se ut ungefär som följande. De körningar som ACR Tasks har kört visas och ”Git Commit” (Git-incheckning) visas i kolumnen TRIGGER (Utlösare) för den senaste uppgiften:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Skapa en flerstegsaktivitet för flera steg i flera steg

ACR-uppgifter har som standard behörighet att skicka eller hämta avbildningar från registret där aktiviteten körs. Du kanske vill köra en aktivitet i flera steg som riktar sig till en eller flera register utöver körningsregistret. Du kan till exempel behöva skapa avbildningar i ett register och lagra avbildningar med olika taggar i ett andra register som används av ett produktionssystem. I det här exemplet visas hur du skapar en sådan uppgift och innehåller autentiseringsuppgifter för ett annat register.

Om du inte redan har ett andra register skapar du ett för det här exemplet. Om du behöver ett register kan du gå till [föregående självstudie](container-registry-tutorial-quick-task.md) eller [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

För att skapa uppgiften behöver du namnet på registerinloggningsservern, som är av formuläret *mycontainerregistrydate.azurecr.io* (alla gemener). I det här exemplet använder du det andra registret för att lagra bilder som taggats av byggdatum.

### <a name="yaml-file"></a>YAML-fil

Det andra exemplet med flera steg för den `taskmulti-multiregistry.yaml`här självstudien definieras i filen , som ligger i roten till den GitHub-repo som du klonade:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Den här aktiviteten i flera steg gör följande:

1. Kör `build` två steg för att skapa avbildningar från Dockerfile i arbetskatalogen:
    * Den första `Run.Registry`riktar sig till registret där aktiviteten körs och taggas med ID:et för ACR-uppgifter. 
    * Den andra riktar sig till registret `regDate`som identifieras med värdet i , som `values.yaml` du anger `az acr task create`när du skapar uppgiften (eller tillhandahåller via en extern fil som skickas till ). Den här bilden är taggad med körningsdatumet.
1. Kör `cmd` ett steg för att köra en av de inbyggda behållarna. Det här exemplet startar en tidskrävande behållare i bakgrunden och returnerar behållar-ID:t och stoppar sedan behållaren. I ett verkligt scenario kan du testa en behållare som körs för att säkerställa att den körs korrekt.
1. I `push` ett steg, skjuter de avbildningar som har byggts, den första till `regDate`kör registret, den andra till registret identifieras av .

### <a name="task-command"></a>Kommandot Uppgift

Med hjälp av de skalmiljövariabler som definierats tidigare skapar du aktiviteten genom att köra följande kommando [för att skapa a-aktiviteter.][az-acr-task-create] Ersätt namnet på registret för *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Lägga till aktivitetsautentiseringsuppgifter

Om du vill skicka avbildningar `regDate`till registret som identifieras med värdet i använder du kommandot [az acr task-autentiseringsuppgifter][az-acr-task-credential-add] för att lägga till inloggningsuppgifter för registret i uppgiften.

I det här exemplet rekommenderar vi att du skapar ett [huvudnamn](container-registry-auth-service-principal.md) för tjänsten med åtkomst till registret som omfattas av *rollen AcrPush.* Information om hur du skapar tjänstens huvudnamn finns i det här [Azure CLI-skriptet](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Skicka tjänstens huvudprogram-ID och `az acr task credential add` lösenord i följande kommando:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI returnerar namnet på registerinloggningsservern som du har lagt till.

### <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet i flera steg

Precis som i föregående exempel, för att testa flerstegsaktiviteten, utlösa den manuellt genom att köra kommandot [az acr-aktivitetskörning.][az-acr-task-run] Information om hur du utlöser uppgiften med ett åtagande till Git-databasen finns i avsnittet [Utlösa en version med ett genomförande](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Precis som tidigare visar utdata förloppet för att köra vart och ett av aktivitetsstegen. Utdata kondenseras för att visa viktiga steg.

Resultat:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar flerstegsaktiviteter med flera behållare som automatiskt utlöses när du genomför källkoden till en Git-databas. Avancerade funktioner för flerstegsaktiviteter, inklusive parallell och beroende stegkörning, finns i [YAML-referensen för ACR-uppgifter](container-registry-tasks-reference-yaml.md). Gå vidare till nästa självstudie för att lära dig att skapa uppgifter som utlöser versioner när en containeravbildnings basavbildning uppdateras.

> [!div class="nextstepaction"]
> [Automatisera versioner i basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
