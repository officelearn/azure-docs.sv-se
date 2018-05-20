---
title: Självstudie – Automatisera behållaravbildningsversioner på basavbildningsuppdateringar med Azure Container Registry Build
description: I självstudien får du lära dig att konfigurera en versionsuppgift som utlöser behållaravbildningsversioner i molnet automatiskt när en basavbildning uppdateras.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 531aeaacf0bd70521d70afb45d141fc3296ebb04
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Självstudie: Automatisera avbildningsversioner på basavbildningsuppdateringar med Azure Container Registry Build

ACR Build stöder automatisk versionskörning när en behållares basavbildning uppdateras, till exempel när du korrigerar operativsystemet eller ett programramverk i någon av basavbildningarna. I självstudien får du lära dig att skapa en versionsuppgift i ACR Build som utlöser en version i molnet när en behållares basavbildning har skickats till registret.

I den här självstudien, som är den avslutande delen i serien:

> [!div class="checklist"]
> * Skapa basavbildningen
> * Skapa en versionsuppgift för programavbildningen
> * Uppdatera basavbildningen till att utlösa en programavbildningsversion
> * Visa den utlösta versionen
> * Kontrollera den uppdaterade programavbildningen

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt, måste du ha Azure CLI version **2.0.32** eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="complete-previous-tutorials"></a>Fullständiga tidigare självstudier

I den här självstudien förutsätts det att du redan har slutfört stegen i de två första självstudierna i serien, med följande innehåll:

* Skapa Azure Container Registry
* Förgrena en exempellagringsplats
* Klona en exempellagringsplats
* Skapa en personlig åtkomsttoken för GitHub

Om du inte redan gjort det, slutför du de två första självstudierna innan du fortsätter:

[Skapa behållaravbildningar i molnet med Azure Container Registry Build](container-registry-tutorial-quick-build.md)

[Automatisera behållaravbildningsversioner med Azure Container Registry Build](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Konfigurera miljön

Fyll i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Detta är inte obligatoriskt, men gör körningen av flerradiga Azure CLI-kommandon i den här självstudien lite enklare. Om du inte fyller i dessa måste du manuellt ersätta varje värde, oavsett var de visas i exempelkommandona.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Basavbildningar

Docker-filer som definierar de flesta behållaravbildningarna anger en överordnad avbildning som de är baserade på. Detta kallas ofta för en *basavbildning*. Basavbildningar innehåller vanligtvis operativsystemet, till exempel [Alpine Linux][base-alpine] eller [Windows Nano Server][base-windows], där resten av behållarens lager tillämpas. De kan även innehålla programramverk som [Node.js][base-node] eller [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Uppdateringar av basavbildningar

En basavbildning uppdateras ofta av avbildningens underhållare med nya funktioner och förbättringar av operativsystem eller ramverk i avbildningen. Säkerhetskorrigeringar är en annan vanlig orsak till att en basavbildning uppdateras.

När en basavbildning har uppdaterats kan du behöva återskapa behållaravbildningar i registret med de nya funktionerna och korrigeringarna. I ACR Build finns möjligheten att automatiskt skapa avbildningar när en behållares basavbildning har uppdaterats.

### <a name="base-image-update-scenario"></a>Uppdateringsscenario för basavbildningar

Den här självstudien vägleder dig genom ett uppdateringsscenario för basavbildningen. [Kodexemplet][code-sample] innehåller två Docker-filer: en programavbildning och en avbildning som anges som bas. I avsnitten nedan skapar du en ACR Build-uppgift som automatiskt utlöser en version av programavbildningen när en ny version av basavbildningen skickas till behållarregistret.

[Dockerfile-app][dockerfile-app]: En liten Node.js-webbapp som återger en statisk webbsida som visar vilken Node.js-version den är baserad på. Versionssträngen är simulerad och visar innehållet i miljövariabeln `NODE_VERSION`, som definieras i basavbildningen.

[Dockerfile-base][dockerfile-base]: Avbildningen som `Dockerfile-app` anger som bas. Den är baserad på en [nod][base-node]avbildning och inkluderar miljövariabeln `NODE_VERSION`.

I följande avsnitt skapar du en versionsuppgift, uppdaterar värdet `NODE_VERSION` i basavbildningen Dockerfile och använder sedan ACR Build för att skapa basavbildningen. När ACR Build skickar den nya basavbildningen till registret, utlöser den automatiskt en version av programavbildningen. Du kan också köra programmets behållaravbildning lokalt om du vill se andra versionssträngar i versionsavbildningarna.

## <a name="build-base-image"></a>Versionens basavbildning

Starta genom att skapa basavbildningen med ACR Build *Quick Build* (Snabbskapa). Enligt den [första självstudien](container-registry-tutorial-quick-build.md) i serien skapar detta inte bara avbildningen, utan skickar den även till behållarregistret om den lyckas.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Skapa versionsuppgift

Skapa sedan en versionsuppgift med [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Versionsuppgiften liknar den uppgift som skapades i [föregående självstudie](container-registry-tutorial-build-task.md). Den instruerar ACR Build att utlösa en avbildningsversion när incheckningar skickas till den lagringsplats som anges i `--context`.

Om den skiljer sig i sitt beteende utlöser den också en version för avbildningen när dess *basavbildning* uppdateras. Den Dockerfile som anges av `--file`-argumentet, [Dockerfile-app][dockerfile-app], har stöd för att ange en avbildning i samma register som basen:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

När du kör en versionsuppgift identifierar ACR Build beroenden för avbildningen. Om basavbildningen har angetts i `FROM`-instruktionen i samma register, lägger den till en hook för att säkerställa att avbildningen återskapas varje gång som basen uppdateras.

> [!NOTE]
> I förhandsversionen har ACR Build endast stöd för att utlösa en härledd avbildningsversion när både basavbildningen och avbildningen som refererar till den finns i samma Azure-behållarregister.

## <a name="build-application-container"></a>Skapa programbehållare

För att kunna aktivera ACR Build till att fastställa och spåra en behållaravbildnings beroenden – som inkluderar dess basavbildning – **måste** du först utlösa dess versionsuppgift **minst en gång**.

Använd [az acr build-task run][az-acr-build-task-run] till att manuellt utlösa versionsuppgiften och skapa programavbildningen:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Om du vill utföra följande valfria steg skriver du ned **versions-ID:t** (till exempel ”aa6”) när versionen har skapats.

### <a name="optional-run-application-container-locally"></a>Valfritt: Köra programbehållaren lokalt

Om du arbetar lokalt (inte i Cloud Shell) och har installerat Docker, kör du behållaren för att se det program som återges i webbläsaren innan du återskapar dess basavbildning. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `az acr login` eller `docker run`).

Logga först in på behållarregistret med [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Kör nu behållaren lokalt med `docker run`. Ersätt **\<build-id\>** med versions-ID:t som returnerades i föregående steg (till exempel ”aa6”).

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Gå till http://localhost:8080 i webbläsaren. Du bör nu se versionsnumret för Node.js som återgavs på webbsidan, liknande nedan. I ett senare steg ökar du versionen genom att lägga till ett ”a” i versionssträngen.

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-01]

## <a name="list-builds"></a>Versionslista

Därefter gör du en lista med de versioner som ACR Build har slutfört för registret:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Om du slutförde föregående självstudie (och inte tog bort registret) bör du nu se utdata som liknar följande. Anteckna antalet versioner och senaste versions-ID för att kunna jämföra utdatan när du har uppdaterat basavbildningen i nästa avsnitt.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>Uppdatera basavbildning

Här simulerar du en ramverkskorrigering i basavbildningen. Redigera **Dockerfile-base** och lägg till ett ”a” efter versionsnumret som definieras i `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Kör Quick Build (Snabbskapa) i ACR Build för att skapa den ändrade basavbildningen. Anteckna ditt **Versions-ID** i utdatan.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

När versionen är klar och ACR Build har skickat den nya basavbildningen till registret, utlöser den automatiskt en version av programavbildningen. Det kan ta en stund innan ACR Build-uppgiften som du skapade tidigare utlöser programmets avbildningsversion, eftersom den måste identifiera den nyligen slutförda och skickade basavbildningen.

## <a name="list-builds"></a>Versionslista

Nu när du har uppdaterat basavbildningen skapar du återigen en lista med dina versioner för att kunna jämföra med föregående lista. Om utdatan inte skiljer sig åt kan du köra kommandot flera gånger för att se när den nya versionen visas i listan.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

De utdata som genereras liknar följande. UTLÖSARE för den senast körda versionen ska vara ”Image Update” (Avbildningsuppdatering), vilket anger att denna versionsuppgift har startats av basavbildningens Quick Build (Snabbskapa).

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

Om du vill utföra följande valfria steg och köra den nya behållaren för att visa det uppdaterade versionsnumret skriver du ned **versions-ID:t** för versionen som skapades av avbildningsuppdateringen (”aa8” i föregående utdata).

### <a name="optional-run-newly-built-image"></a>Valfritt: Köra nyskapad avbildning

Om du arbetar lokalt (inte i Cloud Shell) och du har installerat Docker, kör du den nya programavbildningen när dess version är klar. Ersätt `<build-id>` med det versions-ID som du hämtade i föregående steg. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Gå till http://localhost:8081 i webbläsaren. Du bör nu se det uppdaterade versionsnumret för Node.js (med ett ”a”) på webbsidan:

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-02]

Observera att du har uppdaterat din **basavbildning** med ett nytt versionsnummer, men den senaste skapade **programavbildningen** visar den nya versionen. ACR Build hämtade ändringen av basavbildningen och återskapade din programavbildning automatiskt.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat i självstudieserien, inklusive behållarregistret, behållarinstansen, nyckelvalvet och tjänstens huvudnamn, genom att utfärda följande kommandon:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

I självstudien lärde du dig att använda en versionsuppgift till att utlösa behållaravbildningsversioner automatiskt när en basavbildning har uppdaterats. Nu kan du gå vidare till att lära dig mer om autentisering av behållarregistret.

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
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png