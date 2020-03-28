---
title: Självstudiekurs - Utlösa bild bygga av privat bas bild uppdatering
description: I den här självstudien konfigurerar du en Azure Container Registry Task för att automatiskt utlösa behållaravbildningsversioner i molnet när en basavbildning i ett annat privat Azure-behållarregister uppdateras.
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77617704"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Självstudiekurs: Automatisera behållaravbildningen när en basavbildning uppdateras i ett annat privat behållarregister 

ACR-uppgifter stöder automatiserade avbildningsversioner när en behållares [basavbildning uppdateras,](container-registry-tasks-base-images.md)till exempel när du korrigerar os- eller programramverket i en av basavbildningarna. 

I den här självstudien får du lära dig hur du skapar en ACR-uppgift som utlöser en version i molnet när en behållares basavbildning skjuts till ett annat Azure-behållarregister. Du kan också prova en självstudiekurs för att skapa en ACR-uppgift som utlöser en avbildningsversion när en basavbildning skjuts till [samma Azure-behållarregister](container-registry-tutorial-base-image-update.md).

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa basavbildningen i ett basregister
> * Skapa en programversionsuppgift i ett annat register för att spåra basavbildningen 
> * Uppdatera basavbildningen till att utlösa en programavbildningsuppgift
> * Visa den utlösta uppgiften
> * Kontrollera den uppdaterade programavbildningen

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt måste du ha Azure CLI version **2.0.68** eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI][azure-cli].

## <a name="prerequisites"></a>Krav

### <a name="complete-the-previous-tutorials"></a>Slutför de tidigare självstudierna

I den här självstudien förutsätts det att du redan har slutfört stegen i de två första självstudierna i serien, med följande innehåll:

* Skapa Azure Container Registry
* Förgrena en exempellagringsplats
* Klona en exempellagringsplats
* Skapa en personlig åtkomsttoken för GitHub

Om du inte redan har gjort det slutför du följande självstudier innan du fortsätter:

[Skapa containeravbildningar i molnet med Azure Container Registry-uppgifter](container-registry-tutorial-quick-task.md)

[Automatisera containeravbildningar med Azure Container Registry-uppgifter](container-registry-tutorial-build-task.md)

Förutom behållarregistret som skapats för tidigare självstudier måste du skapa ett register för att lagra basavbildningarna. Om du vill kan du skapa det andra registret på en annan plats än det ursprungliga registret.

### <a name="configure-the-environment"></a>Konfigurera miljön

Fyll i de här gränssnittsmiljövariablerna med värden som är lämpliga för din miljö. Det här steget är inte obligatoriskt, men det gör det lite enklare att köra de flerradiga Azure CLI-kommandona i den här självstudien. Om du inte fyller i dessa miljövariabler måste du manuellt ersätta varje värde var det än visas i exempelkommandona.

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Uppdateringsscenario för basavbildningar

Den här självstudien vägleder dig genom ett uppdateringsscenario för basavbildningen. Det här scenariot återspeglar ett utvecklingsarbetsflöde för att hantera basavbildningar i ett gemensamt, privat behållarregister när du skapar programavbildningar i andra register. Basavbildningarna kan ange vanliga operativsystem och ramverk som används av ett team, eller till och med vanliga tjänstkomponenter.

Utvecklare som utvecklar programavbildningar i sina egna register kan till exempel komma åt en uppsättning basavbildningar som underhålls i det gemensamma basregistret. Basregistret kan finnas i en annan region eller till och med georecenseras.

[Kodexemplet][code-sample] innehåller två Docker-filer: en programavbildning och en avbildning som anges som bas. I följande avsnitt skapar du en ACR-uppgift som automatiskt utlöser en version av programavbildningen när en ny version av basavbildningen flyttas till ett annat Azure-behållarregister.

* [Dockerfile-app][dockerfile-app]: En liten Node.js-webbapp som återger en statisk webbsida som visar vilken Node.js-version den är baserad på. Versionssträngen är simulerad: den visar innehållet i miljövariabeln `NODE_VERSION`, som definieras i basavbildningen.

* [Dockerfile-base][dockerfile-base]: Avbildningen som `Dockerfile-app` anger som bas. Den är baserad på en [nod][base-node]avbildning och inkluderar miljövariabeln `NODE_VERSION`.

I följande avsnitt skapar du en uppgift, uppdaterar värdet `NODE_VERSION` i basavbildningen Dockerfile och använder sedan ACR Tasks för att skapa basavbildningen. När ACR-uppgiften skickar den nya basavbildningen till registret utlöser den automatiskt en version av programavbildningen. Du kan också köra programmets containeravbildning lokalt om du vill se andra versionssträngar i versionsavbildningarna.

I den här självstudien skapar och skickar ACR-uppgiften en programbehållarevbildning som anges i en Dockerfile. ACR-uppgifter kan också köra [flerstegsaktiviteter](container-registry-tasks-multi-step.md)med hjälp av en YAML-fil för att definiera steg för att skapa, pusha och eventuellt testa flera behållare.

## <a name="build-the-base-image"></a>Skapa basavbildningen

Börja med att bygga basavbildningen med en *snabb uppgift*för ACR-uppgifter med [az acr build][az-acr-build]. Enligt beskrivningen i den [första självstudien](container-registry-tutorial-quick-task.md) i serien skapar denna process inte bara avbildningen, utan skickar den även till containerregistret om den lyckas. I det här exemplet skjuts avbildningen till basavbildningsregistret.

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Skapa en uppgift för att spåra den privata basbilden

Skapa sedan en uppgift i programavbildningsregistret med [az acr-aktivitetsskapande][az-acr-task-create], aktivera en [hanterad identitet](container-registry-tasks-authentication-managed-identity.md). Den hanterade identiteten används i senare steg så att uppgiften autentiseras med basavbildningsregistret. 

I det här exemplet används en systemtilldelad identitet, men du kan skapa och aktivera en användartilldelad hanterad identitet för vissa scenarier. Mer information finns [i Autentisering av korsregister i en ACR-uppgift med hjälp av en Azure-hanterad identitet](container-registry-tasks-cross-registry-authentication.md).

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```


Den här uppgiften liknar den uppgift som skapades i [föregående självstudie .](container-registry-tutorial-build-task.md) Den instruerar ACR Tasks att utlösa en avbildningsversion när incheckningar skickas till den lagringsplats som anges i `--context`. Medan Dockerfile som används för att skapa avbildningen i`FROM node:9-alpine`föregående självstudie anger en offentlig basavbildning ( ), anger Dockerfile i den här uppgiften, [Dockerfile-app][dockerfile-app], en basavbildning i basavbildningsregistret:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Den här konfigurationen gör det enkelt att simulera en ramkorrigering i basavbildningen senare i den här självstudien.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Ge behörighet för identitetshandtag till basregistret

Om du vill ge aktivitetens hanterade identitetsbehörighet för att hämta avbildningar från basavbildningsregistret visas först [az acr-uppgiften][az-acr-task-show] för att hämta tjänstens huvud-ID för identiteten. Kör sedan [az acr visa][az-acr-show] för att få resurs-ID för basregistret:

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Tilldela behörigheterna för hanterad identitets pull till registret genom att köra [az-rolltilldelning skapa:][az-role-assignment-create]

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Lägga till autentiseringsuppgifter för målregister i uppgiften

Kör [az acr-aktivitetsautentiseringstillägg för][az-acr-task-credential-add] att lägga till autentiseringsuppgifter i aktiviteten. Skicka `--use-identity [system]` parametern för att ange att aktivitetens systemtilldelade hanterade identitet kan komma åt autentiseringsuppgifterna.

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Använd [az acr-aktivitetskörning][az-acr-task-run] för att manuellt utlösa uppgiften och skapa programavbildningen. Det här steget behövs så att aktiviteten spårar programbildens beroende av basavbildningen.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

När uppgiften är klar antecknar du **Run ID** (till exempel ”da6”) om du vill slutföra följande valfria steg.

### <a name="optional-run-application-container-locally"></a>Valfritt: Köra programcontainern lokalt

Om du arbetar lokalt (inte i Cloud Shell) och har installerat Docker, kör du containern för att se det program som återges i webbläsaren innan du återskapar dess basavbildning. Hoppa över det här avsnittet om du använder Cloud Shell (Cloud Shell stöder inte `az acr login` eller `docker run`).

Först autentisera till din behållare registret med [az acr inloggning:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Kör nu containern lokalt med `docker run`. Ersätt ** \<run-id\> med run-ID** som hittades i utdata från föregående steg (till exempel "da6"). Det här exemplet `myapp` namnger `--rm` behållaren och innehåller parametern för att ta bort behållaren när du stoppar den.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Gå till `http://localhost:8080` i webbläsaren. Du bör nu se versionsnumret för Node.js som återgavs på webbsidan, liknande nedan. I ett senare steg ökar du versionen genom att lägga till ett ”a” i versionssträngen.

![Skärmbild av ett exempelprogram som återges i en webbläsare][base-update-01]

Om du vill stoppa och ta bort behållaren kör du följande kommando:

```bash
docker stop myapp
```

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
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
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

Om du vill stoppa och ta bort behållaren kör du följande kommando:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda en uppgift till att utlösa containeravbildningsversioner automatiskt när en basavbildning har uppdaterats. Gå nu vidare till nästa självstudiekurs för att lära dig hur du utlöser aktiviteter enligt ett definierat schema.

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
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
