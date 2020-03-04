---
title: Självstudie – Utlös avbildnings skapande vid uppdatering av bas avbildning
description: I den här självstudien får du lära dig hur du konfigurerar en Azure Container Registry aktivitet för att automatiskt utlösa behållar avbildnings avbildningar i molnet när en bas avbildning uppdateras i samma register.
ms.topic: tutorial
ms.date: 01/22/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 3870bc70e9d18a3c1c854055cb0c27018554a556
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249988"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Självstudie: automatisera behållar avbildningar skapar när en bas avbildning uppdateras i ett Azure Container Registry 

ACR-aktiviteter har stöd för automatisk behållar avbildning skapar när en behållares [bas avbildning uppdateras](container-registry-tasks-base-images.md), till exempel när du korrigerar operativ systemet eller program ramverket i en av dina bas avbildningar. 

I den här självstudien får du lära dig hur du skapar en ACR-aktivitet som utlöser en version i molnet när en behållares bas avbildning skickas till samma register. Du kan också prova en själv studie kurs om du vill skapa en ACR-aktivitet som utlöser en avbildnings version när en bas avbildning skickas till [ett annat Azure Container Registry](container-registry-tutorial-private-base-image-update.md). 

I den här självstudien:

> [!div class="checklist"]
> * Skapa basavbildningen
> * Skapa en program avbildning i samma register för att spåra bas avbildningen 
> * Uppdatera basavbildningen till att utlösa en programavbildningsuppgift
> * Visa den utlösta uppgiften
> * Kontrollera den uppdaterade programavbildningen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt måste du ha Azure CLI version **2.0.46** eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI][azure-cli].

## <a name="prerequisites"></a>Förutsättningar

### <a name="complete-the-previous-tutorials"></a>Slutför de tidigare självstudierna

I den här självstudien förutsätts det att du redan har slutfört stegen i de två första självstudierna i serien, med följande innehåll:

* Skapa Azure Container Registry
* Förgrena en exempellagringsplats
* Klona en exempellagringsplats
* Skapa en personlig åtkomsttoken för GitHub

Om du inte redan har gjort det, slutför du följande självstudier innan du fortsätter:

[Skapa containeravbildningar i molnet med Azure Container Registry-uppgifter](container-registry-tutorial-quick-task.md)

[Automatisera containeravbildningar med Azure Container Registry-uppgifter](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Konfigurera miljön

Fyll i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i de här miljövariablerna måste du ersätta varje värde manuellt var det visas i exempel kommandona.

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Uppdateringsscenario för basavbildningar

Den här självstudien vägleder dig genom ett bas avbildnings uppdaterings scenario där en bas avbildning och en program avbildning upprätthålls i ett enda register. 

[Kod exemplet][code-sample] innehåller två Dockerfiles: en program avbildning och en bild som anges som bas. I följande avsnitt skapar du en ACR-uppgift som automatiskt utlöser en version av program avbildningen när en ny version av bas avbildningen skickas till samma behållar register.

* [Dockerfile – app][dockerfile-app]: ett litet Node. js-webbprogram som återger en statisk webb sida som visar den Node. js-version som den baseras på. Versionssträngen är simulerad: den visar innehållet i miljövariabeln `NODE_VERSION`, som definieras i basavbildningen.

* [Dockerfile – bas][dockerfile-base]: den bild som `Dockerfile-app` anger som bas. Den är i sig själv baserad på en [Node][base-node] -avbildning och innehåller `NODE_VERSION`-miljövariabeln.

I följande avsnitt skapar du en uppgift, uppdaterar värdet `NODE_VERSION` i basavbildningen Dockerfile och använder sedan ACR Tasks för att skapa basavbildningen. När ACR-uppgiften skickar den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Du kan också köra programmets containeravbildning lokalt om du vill se andra versionssträngar i versionsavbildningarna.

I den här självstudien skapar din ACR-uppgift och pushar en program behållar avbildning som anges i en Dockerfile. ACR-aktiviteter kan också köra [aktiviteter med flera steg](container-registry-tasks-multi-step.md), med hjälp av en yaml-fil för att definiera steg för att skapa, skicka och välja att testa flera behållare.

## <a name="build-the-base-image"></a>Skapa basavbildningen

Börja med att skapa en bas avbildning med en *snabb uppgift*för ACR uppgifter med hjälp av [AZ ACR build][az-acr-build]. Enligt beskrivningen i den [första självstudien](container-registry-tutorial-quick-task.md) i serien skapar denna process inte bara avbildningen, utan skickar den även till containerregistret om den lyckas.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Skapa en aktivitet

Skapa sedan en uppgift med [AZ ACR Task Create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Den här uppgiften liknar den uppgift som skapades i [föregående självstudie](container-registry-tutorial-build-task.md). Den instruerar ACR Tasks att utlösa en avbildningsversion när incheckningar skickas till den lagringsplats som anges i `--context`. Dockerfile som används för att bygga avbildningen i föregående självstudie anger en offentlig bas avbildning (`FROM node:9-alpine`), Dockerfile i den här uppgiften, [Dockerfile-app][dockerfile-app], anger en bas avbildning i samma register:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Den här konfigurationen gör det enkelt att simulera en Ramverks korrigering i bas avbildningen senare i den här självstudien.

## <a name="build-the-application-container"></a>Skapa programcontainern

Använd [AZ ACR Task Run][az-acr-task-run] för att utlösa aktiviteten manuellt och skapa program avbildningen. Det här steget krävs så att aktiviteten spårar program bildens beroende av bas avbildningen.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

När uppgiften är klar antecknar du **Run ID** (till exempel ”da6”) om du vill slutföra följande valfria steg.

### <a name="optional-run-application-container-locally"></a>Valfritt: Köra programcontainern lokalt

Om du arbetar lokalt (inte i Cloud Shell) och har installerat Docker, kör du containern för att se det program som återges i webbläsaren innan du återskapar dess basavbildning. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `az acr login` eller `docker run`).

Börja med att autentisera till behållar registret med [AZ ACR-inloggning][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Kör nu containern lokalt med `docker run`. Ersätt **\<run-id\>** med det Run ID som finns i utdata från föregående steg (till exempel ”da6”). I det här exemplet namnges behållar `myapp` och innehåller parametern `--rm` för att ta bort behållaren när den stoppas.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till `http://localhost:8080` i webbläsaren. Du bör nu se versionsnumret för Node.js som återgavs på webbsidan, liknande nedan. I ett senare steg ökar du versionen genom att lägga till ett ”a” i versionssträngen.

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-01]

Kör följande kommando för att stoppa och ta bort behållaren:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Lista versionerna

Sedan anger du den aktivitet som ska köras som ACR uppgifter har slutförts för registret med hjälp av [AZ ACR-aktivitets listan-kör][az-acr-task-list-runs] kommando:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Om du slutförde föregående självstudie (och inte tog bort registret) bör du nu se utdata som liknar följande. Anteckna antalet uppgiftskörningar och senaste RUN ID så att du kan jämföra utdata när du har uppdaterat basavbildningen i nästa avsnitt.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Uppdatera basavbildningen

Här simulerar du en ramverkskorrigering i basavbildningen. Redigera **Dockerfile-base** och lägg till ett ”a” efter versionsnumret som definieras i `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Kör en snabbuppgift för att skapa den ändrade basavbildningen. Anteckna **Run ID** i utdata.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

När versionen är klar och ACR-uppgiften har skickat den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Det kan ta en stund innan den uppgift som du skapade tidigare utlöser programmets avbildningsversion, eftersom den måste identifiera den nyligen slutförda och skickade basavbildningen.

## <a name="list-updated-build"></a>Lista uppdaterad version

Nu när du har uppdaterat basavbildningen listar du återigen dina uppgiftskörningar för att kunna jämföra med den tidigare listan. Om utdatan inte skiljer sig åt kan du köra kommandot flera gånger för att se när den nya uppgiftskörningen visas i listan.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

De utdata som genereras liknar följande. TRIGGER (utlösaren) för den senast körda versionen ska vara ”Image Update” (Avbildningsuppdatering), vilket anger att uppgiften startades av snabbuppgiften för basavbildningen.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Om du vill utföra följande valfria steg och köra den nya containern för att visa det uppdaterade versionsnumret skriver du ned **RUN ID** för versionen som skapades av avbildningsuppdateringen (”da8” i föregående utdata).

### <a name="optional-run-newly-built-image"></a>Valfritt: Köra nyskapad avbildning

Om du arbetar lokalt (inte i Cloud Shell) och du har installerat Docker, kör du den nya programavbildningen när dess version är klar. Ersätt `<run-id>` med det RUN ID som du hämtade i föregående steg. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till http://localhost:8081 i webbläsaren. Du bör nu se det uppdaterade versionsnumret för Node.js (med ett ”a”) på webbsidan:

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-02]

Observera att du har uppdaterat din **basavbildning** med ett nytt versionsnummer, men den senaste skapade **programavbildningen** visar den nya versionen. ACR Tasks hämtade din ändring av basavbildningen och återskapade din programavbildning automatiskt.

Kör följande kommando för att stoppa och ta bort behållaren:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda en uppgift till att utlösa containeravbildningsversioner automatiskt när en basavbildning har uppdaterats. Gå vidare till nästa självstudie och lär dig hur du utlöser aktiviteter enligt ett definierat schema.

> [!div class="nextstepaction"]
> [Köra en aktivitet enligt ett schema](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
