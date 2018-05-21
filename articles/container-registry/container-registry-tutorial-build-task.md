---
title: Självstudie – Automatisera behållaravbildningsversioner med Azure Container Registry Build
description: I självstudien får du lära dig att konfigurera en versionsuppgift till att utlösa behållaravbildningsversioner i molnet automatiskt när du checkar in källkod på en Git-lagringsplats.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Självstudie: Automatisera behållaravbildningsversioner med Azure Container Registry Build

Förutom [Quick Build](container-registry-tutorial-quick-build.md) (Snabbskapa) har ACR Build även stöd för automatiserade Docker-behållaravbildningsversioner med *versionsuppgiften*. I självstudien använder du Azure CLI till att skapa en versionsuppgift som utlöser avbildningsversioner i molnet automatiskt när du checkar in källkod på en Git-lagringsplats.

Den här självstudien är del två i serien:

> [!div class="checklist"]
> * Skapa en versionsuppgift
> * Testa versionsuppgiften
> * Visa versionsstatus
> * Utlösa versionsuppgiften med ett kodgenomförande

Självstudien förutsätter att du redan har slutfört stegen i den [föregående självstudien](container-registry-tutorial-quick-build.md). Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-build.md#prerequisites) i föregående självstudie innan du fortsätter.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt, måste du ha Azure CLI version **2.0.32** eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="get-sample-code"></a>Hämta exempelkod

Självstudien förutsätter att du redan har slutfört stegen i [föregående självstudie](container-registry-tutorial-quick-build.md), samt att du har förgrenat och klonat exempellagringsplatsen. Om du inte har gjort det, slutför du stegen i avsnittet [Krav](container-registry-tutorial-quick-build.md#prerequisites) i föregående självstudie innan du fortsätter.

### <a name="container-registry"></a>Behållarregister

Du måste ha ett Azure-behållarregister i din Azure-prenumeration för att kunna slutföra den här självstudien. Om du behöver ett register kan du gå till [föregående självstudie](container-registry-tutorial-quick-build.md) eller [Snabbstart: Skapa ett behållarregister med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Versionsuppgift

En versionsuppgift definierar egenskaperna för en automatiserad version, inklusive platsen för behållaravbildningens källkod och den händelse som utlöser versionen. När en händelse som definierats i denna versionsuppgift inträffar, till exempel en incheckning på en Git-lagringsplats, initierar ACR Build en behållaravbildningsversion i molnet. Som standard skickar den sedan en skapad avbildning till det Azure-behållarregister som anges i uppgiften.

ACR Build stöder för närvarande följande utlösare av versionsuppgifter:

* Checka in på en Git-lagringsplats
* Basavbildningsuppdatering

## <a name="create-a-build-task"></a>Skapa en versionsuppgift

I det här avsnittet ska du först skapa en personlig åtkomsttoken för GitHub som ska användas med ACR Build. Därefter skapar du en versionsuppgift som utlöser en version när koden checkar in på din förgrening av lagringsplatsen.

### <a name="create-a-github-personal-access-token"></a>Skapa en personlig åtkomsttoken för GitHub

För att kunna utlösa en version för en incheckning på en Git-lagringsplats, behöver ACR Build ha en personlig åtkomsttoken för att komma åt lagringsplatsen. Följ dessa steg för att generera en personlig åtkomsttoken i GitHub:

1. Gå till sidan där personliga åtkomsttokens skapas i GitHub på https://github.com/settings/tokens/new
1. Ange en kort **beskrivning** för din token, till exempel ”Uppgiftsdemo för ACR Build”
1. Under **repo** aktiverar du **repo:status** och **public_repo**

   ![Skärmbild av sidan Personlig åtkomsttoken i GitHub][build-task-01-new-token]

1. Välj knappen **Generera token** (du kan behöva bekräfta lösenordet)
1. Kopiera och spara din genererade token på en **säker plats** (du använder denna token när du definierar en versionsuppgift i följande avsnitt)

   ![Skärmbild av genererad personlig åtkomsttoken i GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Skapa versionsuppgiften

Nu när du har slutfört de steg som krävs för att aktivera att ACR Build läser incheckningsstatus och skapar webhooks på en lagringsplats, kan du skapa en versionsuppgift som utlöser en behållaravbildningsversion på incheckningar till lagringsplatsen.

Fyll först i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Detta är inte obligatoriskt, men gör körningen av flerradiga Azure CLI-kommandon i självstudien lite enklare. Om du inte fyller i dessa måste du manuellt ersätta varje värde, oavsett var de visas i exempelkommandona.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Nu ska du skapa versionsuppgiften genom att köra kommandot [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Versionsuppgiften anger att varje gång en tidskod checkas in på *huvudförgreningen* i lagringsplatsen som anges av `--context`, skapar ACR Build behållaravbildningen från koden i förgreningen. Argumentet `--image` anger ett parametriserat värde på `{{.Build.ID}}` för versionsdelen av avbildningstaggen, vilket säkerställer att versionsavbildningen motsvarar en viss version och är unikt taggad.

Utdata från kommandot [az acr build-task create][az-acr-build-task-create] liknar följande:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Testa versionsuppgiften

Nu har du en versionsuppgift som definierar din version. Om du vill testa versionsdefinitionen utlöser du en version manuellt genom att köra kommandot [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Som standard strömmar kommandot `az acr build-task run` loggens utdata till konsolen när du kör kommandot. Dessa utdata visar att versionen **aa2** har placerats i kö och skapats.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Visa versionsstatus

Det kan ibland vara bra att se statusen för en pågående version som inte har utlösts manuellt. Exempelvis när du felsöker versioner som utlösts av ett källkodsgenomförande. I det här avsnittet utlöser du en manuell version, men utelämnar standardbeteendet för strömningen av versionsloggen till konsolen. Sedan använder du kommandot `az acr build-task logs` till att övervaka den pågående versionen.

Utlös först en version manuellt som du gjorde tidigare, men ange argumentet `--no-logs` för att utelämna loggning till konsolen:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Använd sedan kommandot `az build-task logs` för att se loggen för den version som körs:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Loggen för den version som körs strömmas till konsolen och bör likna följande utdata (visas här trunkerat):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Utlösa en version med en incheckning

Nu när du har testat denna versionsuppgift genom att köra den manuellt, utlöser du den automatiskt med en ändring i källkoden.

Kontrollera först att du är i den katalog som innehåller din lokala klon av [lagringsplatsen][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Sedan kör du följande kommandon för att skapa, checka in och skicka en ny fil till din förgrening av lagringsplatsen på GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Du kan bli ombedd att ange dina GitHub-autentiseringsuppgifter när du kör kommandot `git push`. Ange ditt GitHub-användarnamn och din personliga åtkomsttoken som du skapade tidigare för lösenordet.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

När du har skickat en incheckning till lagringsplatsen startar den webhook som skapades av ACR Build en version i Azure Container Registry. Visa versionsloggarna för den version som körs för att kontrollera och övervaka versionsförloppet:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Utdatan liknar följande och visar den version som körs för närvarande (eller som kördes senast):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Versionslista

Om du vill se en lista med de versioner som ACR Build har slutfört för registret, kör du kommandot [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Utdatan från kommandot ska se ut ungefär som följande. De versioner som ACR Build har kört visas och ”Git Commit” (Git-incheckning) visas i kolumnen UTLÖSARE för den senaste versionen:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Nästa steg

I självstudien har du lärt dig att använda en versionsuppgift för att utlösa behållaravbildningsversioner i Azure automatiskt när du checkar in källkod på en Git-lagringsplats. Gå vidare till nästa självstudie för att lära dig att skapa versionsuppgifter som utlöser versioner när en behållaravbildnings basavbildning uppdateras.

> [!div class="nextstepaction"]
> [Automatisera versioner i basavbildningsuppdateringar](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
