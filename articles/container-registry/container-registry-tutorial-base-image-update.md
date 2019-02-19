---
title: Självstudie – Automatisera containeravbildningsversioner vid uppdatering av basavbildning – Azure Container Registry-uppgifter
description: I den här självstudien lär du dig hur du konfigurerar en Azure Container Registry-uppgift till att automatiskt utlösa containeravbildningsversioner i molnet när en basavbildning uppdateras.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e2dd02ada2c22fa8d6c2d79387ea01f3ec97dd7e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108134"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Självstudie: Automatisera containeravbildningsversioner när en basavbildning uppdateras i ett Azure-containerregister 

ACR Tasks stöder automatisk versionskörning när en containers basavbildning uppdateras, till exempel när du korrigerar operativsystemet eller ett programramverk i någon av basavbildningarna. I självstudien får du lära dig att skapa en uppgift i ACR Tasks som utlöser en version i molnet när en containers basavbildning har skickats till registret.

I den här självstudien, som är den avslutande delen i serien:

> [!div class="checklist"]
> * Skapa basavbildningen
> * Skapa en versionsuppgift för programavbildningen
> * Uppdatera basavbildningen till att utlösa en programavbildningsuppgift
> * Visa den utlösta uppgiften
> * Kontrollera den uppdaterade programavbildningen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt måste du ha Azure CLI version **2.0.46** eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="complete-the-previous-tutorials"></a>Slutför de tidigare självstudierna

I den här självstudien förutsätts det att du redan har slutfört stegen i de två första självstudierna i serien, med följande innehåll:

* Skapa Azure Container Registry
* Förgrena en exempellagringsplats
* Klona en exempellagringsplats
* Skapa en personlig åtkomsttoken för GitHub

Om du inte redan gjort det, slutför du de två första självstudierna innan du fortsätter:

[Skapa containeravbildningar i molnet med Azure Container Registry-uppgifter](container-registry-tutorial-quick-task.md)

[Automatisera containeravbildningar med Azure Container Registry-uppgifter](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Konfigurera miljön

Fyll i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller dessa miljövariabler måste du manuellt ersätta alla värden oavsett var de visas i exempelkommandona.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Basavbildningar

Docker-filer som definierar de flesta containeravbildningarna anger en överordnad avbildning som de är baserade på. Detta kallas ofta för en *basavbildning*. Basavbildningar innehåller vanligtvis operativsystemet, till exempel [Alpine Linux][base-alpine] eller [Windows Nano Server][base-windows], där resten av containerns lager tillämpas. De kan även innehålla programramverk som [Node.js][base-node] eller [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Uppdateringar av basavbildningar

En basavbildning uppdateras ofta av avbildningens underhållare med nya funktioner och förbättringar av operativsystem eller ramverk i avbildningen. Säkerhetskorrigeringar är en annan vanlig orsak till att en basavbildning uppdateras.

När en basavbildning har uppdaterats kan du behöva återskapa containeravbildningar i registret med de nya funktionerna och korrigeringarna. I ACR Tasks finns möjligheten att automatiskt skapa avbildningar när en containers basavbildning uppdateras.

### <a name="base-image-update-scenario"></a>Uppdateringsscenario för basavbildningar

Den här självstudien vägleder dig genom ett uppdateringsscenario för basavbildningen. [Kodexemplet][code-sample] innehåller två Docker-filer: en programavbildning och en avbildning som anges som bas. I avsnitten nedan skapar du en ACR-uppgift som automatiskt utlöser en version av programavbildningen när en ny version av basavbildningen skickas till containerregistret.

[Dockerfile-app][dockerfile-app]: En liten Node.js-webbapp som återger en statisk webbplats som visar vilken Node.js-version den är baserad på. Versionssträngen är simulerad: den visar innehållet i miljövariabeln `NODE_VERSION`, som definieras i basavbildningen.

[Dockerfile-base][dockerfile-base]: Den avbildning som `Dockerfile-app` anger som sin bas. Den är baserad på en [nod][base-node]avbildning och inkluderar miljövariabeln `NODE_VERSION`.

I följande avsnitt skapar du en uppgift, uppdaterar värdet `NODE_VERSION` i basavbildningen Dockerfile och använder sedan ACR Tasks för att skapa basavbildningen. När ACR-uppgiften skickar den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Du kan också köra programmets containeravbildning lokalt om du vill se andra versionssträngar i versionsavbildningarna.

I den här självstudien skapar och skickar din ACR-uppgift en enskild containeravbildning som anges i en Dockerfile. ACR-uppgifter kan även köra [uppgifter i flera steg](container-registry-tasks-multi-step.md) (för närvarande i förhandsversion) med hjälp av en YAML-fil för att definiera steg för att skapa, skicka och om så önskas testa flera containrar.

## <a name="build-the-base-image"></a>Skapa basavbildningen

Börja genom att skapa basavbildningen med *quick task* (snabbuppgift) i ACR Tasks. Enligt beskrivningen i den [första självstudien](container-registry-tutorial-quick-task.md) i serien skapar denna process inte bara avbildningen, utan skickar den även till containerregistret om den lyckas.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Skapa en uppgift

Skapa sedan en uppgift med [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Uppgiften liknar den snabbuppgift som skapades i [föregående självstudie](container-registry-tutorial-build-task.md). Den instruerar ACR Tasks att utlösa en avbildningsversion när incheckningar skickas till den lagringsplats som anges i `--context`.

Om den skiljer sig i sitt beteende utlöser den också en version för avbildningen när dess *basavbildning* uppdateras. Den Dockerfile som anges av `--file`-argumentet, [Dockerfile-app][dockerfile-app], har stöd för att ange en avbildning i samma register som basen:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

När du kör en uppgift identifierar ACR Tasks beroenden för en avbildning. Om den basavbildning som har angetts i `FROM`-instruktionen finns i samma register eller på en offentlig Docker Hub-lagringsplats lägger den till en hook för att säkerställa att avbildningen återskapas varje gång basen uppdateras.

## <a name="build-the-application-container"></a>Skapa programcontainern

För göra så att ACR Tasks kan fastställa och spåra en containeravbildnings beroenden – som inkluderar dess basavbildning – **måste** du först utlösa dess aktivitet **minst en gång**.

Använd [az acr task run][az-acr-task-run] för att manuellt utlösa uppgiften och skapa programavbildningen:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

När uppgiften är klar antecknar du **Run ID** (till exempel ”da6”) om du vill slutföra följande valfria steg.

### <a name="optional-run-application-container-locally"></a>Valfritt: Köra programcontainern lokalt

Om du arbetar lokalt (inte i Cloud Shell) och har installerat Docker, kör du containern för att se det program som återges i webbläsaren innan du återskapar dess basavbildning. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `az acr login` eller `docker run`).

Logga först in i containerregistret med [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Kör nu containern lokalt med `docker run`. Ersätt **\<run-id\>** med det Run ID som finns i utdata från föregående steg (till exempel ”da6”).

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till http://localhost:8080 i webbläsaren. Du bör nu se versionsnumret för Node.js som återgavs på webbsidan, liknande nedan. I ett senare steg ökar du versionen genom att lägga till ett ”a” i versionssträngen.

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-01]

## <a name="list-the-builds"></a>Lista versionerna

Nu listar du de uppgiftskörningar som ACR Tasks har slutfört för ditt register med hjälp av kommandot [az acr task list-runs][az-acr-task-list-runs]:

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
$ az acr task list-builds --registry $ACR_NAME --output table

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
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till http://localhost:8081 i webbläsaren. Du bör nu se det uppdaterade versionsnumret för Node.js (med ett ”a”) på webbsidan:

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-02]

Observera att du har uppdaterat din **basavbildning** med ett nytt versionsnummer, men den senaste skapade **programavbildningen** visar den nya versionen. ACR Tasks hämtade din ändring av basavbildningen och återskapade din programavbildning automatiskt.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat i självstudieserien, inklusive containerregistret, containerinstansen, nyckelvalvet och tjänstens huvudnamn, genom att utfärda följande kommandon:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda en uppgift till att utlösa containeravbildningsversioner automatiskt när en basavbildning har uppdaterats. Nu kan du gå vidare till att lära dig mer om autentisering av containerregistret.

> [!div class="nextstepaction"]
> [Autentisering i Azure Container Registry](container-registry-authentication.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr#az-acr-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
