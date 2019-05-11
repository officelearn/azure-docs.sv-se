---
title: Självstudie – flera steg behållare uppgifter - Azure Container Registry uppgifter
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry-uppgift för att automatiskt utlösa ett arbetsflöde för flera steg att skapa, köra, och push-överför avbildningar i molnet när du har checkat källkoden till en Git-lagringsplats.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 09b8e5d31bc6a4ec24633889920e2768bb7ce538
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546575"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Självstudier: Köra ett arbetsflöde med flera steg behållare i molnet när du har checkat källkoden

Förutom en [snabb uppgiften](container-registry-tutorial-quick-task.md), ACR-aktiviteter har stöd för flera steg, flera container-baserade arbetsflöden som automatiskt kan utlösa du källkoden till en Git-lagringsplats. 

Lär dig hur du använder exemplet YAML filer för att definiera aktiviteter med flera steg som skapar, köra, och push-överför en eller flera avbildningar till ett register när du har checkat källkoden i de här självstudierna. Om du vill skapa en uppgift som automatiserar bara en enda avbildning version på kodgenomförande [självstudien: Automatisera avbildningar för behållare i molnet när du har checkat källkoden](container-registry-tutorial-build-task.md). En översikt över ACR uppgifter finns i [framework uppdatering med ACR uppgifter och automatisera OS](container-registry-tasks-overview.md),

I den här självstudien:

> [!div class="checklist"]
> * Definiera en uppgift för flera steg med hjälp av en YAML-fil
> * Skapa en uppgift
> * Du kan också lägga till autentiseringsuppgifter i uppgiften för att aktivera åtkomst till ett annat register
> * Testa uppgiften
> * Visa status för aktivitet
> * Utlösa uppgiften med en kodincheckning

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-task.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-task.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt måste du ha Azure CLI version **2.0.62** eller senare installerat och loggat in med [az-inloggning][az-login]. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Skapa en uppgift i flera steg

Nu när du har slutfört stegen som krävs för att aktivera ACR uppgifter att läsa utförandestatus för och skapa webhooks i en databas, kan du skapa en uppgift i flera steg som utlöser att skapa, köra och överföra en behållaravbildning.

### <a name="yaml-file"></a>YAML-fil

Du definierar stegen för att en uppgift för flera steg i en [YAML-fil](container-registry-tasks-reference-yaml.md). Den första aktiviteten som exempel flera steg för den här självstudiekursen definieras i filen `taskmulti.yaml`, vilket är i roten av GitHub-lagringsplats som du klonade:

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

Den här uppgiften med flera steg gör följande:

1. Kör en `build` steg för att skapa en avbildning från Dockerfile som finns i arbetskatalogen. Bild-mål i `Run.Registry`, registret där aktiviteten körs och är märkta med en unik ACR-uppgifter kör ID. 
1. Kör en `cmd` steg för att köra avbildningen i en tillfällig behållare. Det här exemplet startar en tidskrävande-behållaren i bakgrunden och returnerar behållar-ID, innan den stoppar behållaren. I ett verkligt scenario kan du inkludera steg för att testa behållaren som körs för att säkerställa att det körs korrekt.
1. I en `push` steg, skickar avbildningen som skapades i kör registret.

### <a name="task-command"></a>Aktivitetskommandot

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller dessa miljövariabler, måste du manuellt ersätta alla värden där det visas i exempelkommandon.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Nu ska du skapa uppgiften genom att köra följande [az acr uppgift skapa] [ az-acr-task-create] kommando:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Den här aktiviteten anger att alla tidskod strävar efter att den *master* gren i databasen som anges av `--context`, ACR aktiviteter ska köra aktiviteten flera steg från kod på det kontoret. YAML-filen som anges av `--file` från databasen definierar roten stegen. 

Utdata från kommandot [az acr task create][az-acr-task-create] liknar följande:

```console
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

## <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet flera steg

Om du vill testa aktiviteten flera steg, utlöser den manuellt genom att köra den [az acr-uppgiftskörning] [ az-acr-task-run] kommando:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Utdata visar förloppet för att köra varje uppgift steg. Utdata nedan ryms för att visa viktiga steg.

```console
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

```azurecli-interactive
cd acr-build-helloworld-node
```

Sedan kör du följande kommandon för att skapa, checka in och skicka en ny fil till din förgrening av lagringsplatsen på GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Du kan bli ombedd att ange dina GitHub-autentiseringsuppgifter när du kör kommandot `git push`. Ange ditt GitHub-användarnamn och din personliga åtkomsttoken som du skapade tidigare för lösenordet.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

När du har push-överfört en incheckning till lagringsplatsen, utlöses webhooken som skapats av ACR uppgifter och inleder aktiviteten i Azure Container Registry. Visa loggarna för den uppgift som körs för att kontrollera och övervaka versionsförloppet:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Utdata liknar följande och visar den uppgift som körs för närvarande (eller som kördes senast):

```console
$ az acr task logs --registry $ACR_NAME
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

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Skapa en uppgift för flera registret flera steg

ACR-uppgifter som standard har behörighet att skicka eller hämta avbildningar från registret där aktiviteten körs. Du kanske vill köra en aktivitet i flera steg som riktar sig mot en eller flera register förutom kör registret. Du kan behöva skapa bilder i ett register och lagra bilder med olika taggar i ett andra register som används av ett produktionssystem. Det här exemplet visar hur du skapar en sådan uppgift och ange autentiseringsuppgifter för ett annat register.

Om du inte redan har ett andra register, skapa en för det här exemplet. Om du behöver ett register kan du titta på en [tidigare självstudie](container-registry-tutorial-quick-task.md) eller på [Snabbstart: Skapa ett containerregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

Om du vill skapa uppgiften måste namnet på behållarregistrets inloggningsserver som är i formatet *mycontainerregistrydate.azurecr.io* (endast gemener). I det här exemplet använder du andra registret för att lagra bilder som taggats med build datum.

### <a name="yaml-file"></a>YAML-fil

Andra exempel flerstegstest aktiviteten för den här självstudiekursen definieras i filen `taskmulti-multiregistry.yaml`, vilket är i roten av GitHub-lagringsplats som du klonade:

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

Den här uppgiften med flera steg gör följande:

1. Kör två `build` stegen för att skapa avbildningar från Dockerfile som finns i arbetskatalogen:
    * Den första är riktad mot den `Run.Registry`, registret där aktiviteten körs och är märkta med ACR-uppgifter kör ID. 
    * Andra mål i registret som identifieras av värdet för `regDate`, som du angav när du skapar aktiviteten (eller ange via en extern `values.yaml` filen skickas till `az acr task create`). Den här avbildningen är taggad med kör datum.
1. Kör en `cmd` steg för att köra något av de inbyggda behållarna. Det här exemplet startar en tidskrävande-behållaren i bakgrunden och returnerar behållar-ID, innan den stoppar behållaren. I ett verkligt scenario kan du testa en behållare som körs för att säkerställa att det körs korrekt.
1. I en `push` steg, skickar bilder som har skapats, först med att köra registret, andra i registret som identifieras av `regDate`.

### <a name="task-command"></a>Aktivitetskommandot

Med shell miljövariabler som definierats tidigare, skapa uppgiften genom att köra följande [az acr uppgift skapa] [ az-acr-task-create] kommando. Ersätt namnet på registret för *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Lägg till autentiseringsuppgift för uppgift

För push-avbildningar i registret som identifieras av värdet för `regDate`, använda den [az acr uppgift autentiseringsuppgifter lägger du till] [ az-acr-task-credential-add] kommando för att lägga till inloggningsuppgifterna för det registret i uppgiften.

I det här exemplet rekommenderar vi att du skapar en [tjänstens huvudnamn](container-registry-auth-service-principal.md) med åtkomst till registret tillhöra den *AcrPush* roll. Om du vill skapa tjänstens huvudnamn finns i den här [Azure CLI-skript](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Skicka service principal program-ID och lösenord i följande `az acr task credential add` kommando:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI Returnerar namnet på behållarregistrets inloggningsserver som du har lagt till.

### <a name="test-the-multi-step-workflow"></a>Testa arbetsflödet flera steg

Som i föregående exempel, om du vill testa aktiviteten flera steg, utlöser den manuellt genom att köra den [az acr-uppgiftskörning] [ az-acr-task-run] kommando. Om du vill starta uppgiften med ett åtagande på Git-lagringsplatsen, finns i avsnittet [utlösa en build med ett åtagande](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Som standard strömmar kommandot `az acr task run` loggens utdata till konsolen när du kör kommandot. Som visar tidigare, utdata förloppet för att köra varje uppgift steg. Utdata är ryms för att visa viktiga steg

Utdata:

```console
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

I den här självstudien lärde du dig att skapa flera steg, flera container-baserad uppgifter som sätts automatiskt när du har checkat källkoden till en Git-lagringsplats. Mer avancerade funktionerna i flera steg aktiviteter, inklusive körning av parallella program och beroende steg, finns i den [ACR uppgifter YAML referens](container-registry-tasks-reference-yaml.md). Gå vidare till nästa självstudie för att lära dig att skapa uppgifter som utlöser versioner när en containeravbildnings basavbildning uppdateras.

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
