---
title: Självstudie – Skapa behållaravbildningar i molnet med Azure Container Registry Build
description: I den här självstudien lär du dig att skapa en Docker-behållaravbildning i Azure med Azure Container Registry Build (ACR Build) och sedan distribuera den till Azure Container Instances.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Självstudie: Skapa behållaravbildningar i molnet med Azure Container Registry Build

**ACR Build** är en uppsättning funktioner i Azure Container Registry som ger effektiva Docker-behållaravbildningar i Azure. I den här artikeln får du lära dig att använda funktionen *Quick Build* (Snabbskapa) i ACR Build. Med Quick Build (Snabbskapa) förlänger du utvecklingens ”inre loop” till molnet, vilket ger verifiering och automatisk överföring av skapade avbildningar till behållarregistret. Avbildningarna skapas internt i molnet nära registret, vilket ger snabbare distribution.

Alla dina kunskaper om Dockerfile är direkt överföringsbara till ACR Build. Du behöver inte ändra dina Docker-filer för att kunna skapa i molnet med ACR Build, bara de kommandon som du kör.

I den här självstudien, som är del ett i en serie:

> [!div class="checklist"]
> * Hämta källkod för exempelprogram
> * Skapa en behållaravbildning i Azure
> * Distribuera en behållare till Azure Container Instances

I kommande självstudier lär du dig att använda versionsuppgifter i ACR Build för automatiska behållaravbildningar som bygger på kodgenomförande och uppdatering av basavbildningar.

> [!IMPORTANT]
> ACR Build finns just nu i en förhandsversion och stöds endast av Azure-behållarregister i regionerna **USA, östra** (eastus) och **Europa, västra** (westeurope). Förhandsversioner blir tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill använda Azure CLI lokalt, måste du ha Azure CLI version **2.0.32** eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa mer i [Installera Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="github-account"></a>GitHub-konto

Skapa ett konto på https://github.com om du inte redan har ett. Den här självstudieserien använder en GitHub-lagringsplats för att visa automatiserade avbildningsversioner i ACR Build.

### <a name="fork-sample-repository"></a>Förgrena en exempellagringsplats

Därefter använder du GitHubs användargränssnitt för att förgrena exempellagringsplatsen till ditt GitHub-konto. I självstudien skapar du en behållaravbildning från källan på lagringsplatsen och i nästa självstudie skickar du en incheckning till din lagringsplatsförgrening för att starta en automatiserad version.

Förgrena den här lagringsplatsen: https://github.com/Azure-Samples/acr-build-helloworld-node

![Skärmbild av knappen Förgrening (markerad) i GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klona din förgrening

När du har förgrenat lagringsplatsen kan du klona din förgrening och ange den katalog som innehåller din lokala klon.

Klona lagringsplatsen med `git`, samt ersätt **\<your-github-username\>** med ditt GitHub-användarnamn:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Ange den katalog som innehåller källkoden:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-gränssnitt

Kommandona i den här självstudieserien är formaterade för Bash-gränssnittet. Om du föredrar att använda PowerShell, kommandotolken eller något annat gränssnitt, kan du behöva justera radfortsättningen och miljövariabelns format därefter.

## <a name="build-in-azure-with-acr-build"></a>Skapa i Azure med ACR Build

Nu när du har hämtat källkoden till din dator, följer du dessa steg för att skapa ett behållarregister och en behållaravbildning med ACR Build.

För att göra det enklare att köra exempelkommandona, använder självstudierna i serien gränssnittets miljövariabler. Kör följande kommando för att ange variabeln `ACR_NAME`. Ersätt **\<registry-name\>** med ett unikt namn för ditt nya behållarregister. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. De andra resurser som du skapar i självstudien baseras på det här namnet, så du behöver bara ändra den första variabeln.

```azurecli-interactive
ACR_NAME=<registry-name>
```

När behållarregistrets miljövariabel har fyllts i, bör du nu kunna kopiera och klistra in resten av kommandona i självstudien utan att redigera några värden. Kör följande kommandon för att skapa en resursgrupp och ett behållarregister:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Nu när du har ett register kan du använda ACR Build till att skapa en behållaravbildning från exempelkoden. Kör kommandot [az acr build][az-acr-build] för att utföra *Quick Build* (Snabbskapa):

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

Utdata från kommandot [az acr build][az-acr-build] liknar följande. Du kan se uppladdningen av källkoden (”sammanhanget”) till Azure, samt information om åtgärden `docker build` som ACR Build kör i molnet. Eftersom ACR Build använder `docker build` till att skapa dina avbildningar, krävs inga ändringar av dina Docker-filer för att du ska kunna börja använda ACR Build omedelbart.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

I slutet av utdatan visar ACR Build de beroenden som har identifierats för avbildningen. Detta gör att ACR Build kan automatisera avbildningsversioner av basavbildningsuppdateringar, t.ex när en basavbildning uppdateras med OS- eller ramverkskorrigeringar. Du lär dig mer om stödet i ACR Build för basavbildningsuppdateringar senare i den här självstudieserien.

## <a name="deploy-to-azure-container-instances"></a>Distribuera till Azure Container Instances

ACR Build skickar automatiskt skapade avbildningar till registret som standard, vilket innebär att du kan distribuera dem från registret med en gång.

I det här avsnittet skapar du ett Azure Key Vault och tjänstens huvudnamn. Sedan distribuerar du behållaren till Azure Container Instances (ACI) med hjälp av autentiseringsuppgifterna för tjänstens huvudnamn.

### <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

Alla produktionsscenarier bör använda [huvudnamnen för tjänsten][service-principal-auth] för att få åtkomst till ett behållarregister i Azure. Med tjänstens huvudnamn får du rollbaserad åtkomstkontroll till behållaravbildningarna. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

#### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](/azure/key-vault/), skapar du ett med Azure CLI och följande kommandon.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Du måste nu skapa ett huvudnamn för tjänsten och lagra dess autentiseringsuppgifter i nyckelvalvet.

Använd kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] till att skapa tjänstens huvudnamn och [az keyvault secret set][az-keyvault-secret-set] för att lagra **lösenordet** till tjänstens huvudnamn i valvet:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Argumentet `--role` i föregående kommando konfigurerar huvudnamnet för tjänsten med rollen *läsare*, vilket endast ger den hämtningsåtkomst till registret. Om du vill bevilja både sändnings- och hämtningsåtkomst ändrar du argumentet `--role` till *deltagare*.

Därefter lagrar du *appId* för tjänstens huvudnamn i valvet, vilket är det **användarnamn** du skickar till Azure Container Registry för autentisering:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure Key Vault och lagrat två hemligheter i det:

* `$ACR_NAME-pull-usr`: ID för tjänstens huvudnamn som ska användas som behållarregistrets **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenord för tjänstens huvudnamn som ska användas som behållarregistrets **lösenord**.

Nu kan du referera till dessa hemligheter efter namn när du eller dina program och tjänster hämtar avbildningar från registret.

### <a name="deploy-container-with-azure-cli"></a>Distribuera behållare med Azure CLI

Nu när autentiseringsuppgifterna för tjänstens huvudnamn lagras som Azure Key Vault-hemligheter, kan dina program och tjänster använda dem för att få åtkomst till ditt privata register.

Kör kommandot [az container create][az-container-create] för att distribuera en behållarinstans. Kommandot använder de autentiseringsuppgifter för tjänstens huvudnamn som lagras i Azure Key Vault för autentiseringen till behållarregistret.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Värdet `--dns-name-label` måste vara unikt i Azure, så föregående kommando lägger till behållarregistrets namn i behållarens DNS-namnetikett. Utdata från kommandot visar behållarens fullständiga domännamn (FQDN), till exempel:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Anteckna behållarens FQDN – du behöver det i nästa avsnitt.

### <a name="verify-deployment"></a>Verifiera distributionen

Om du vill se startprocessen i behållaren använder du kommandot [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

Utdatan `az container attach` visar först behållarens status när den hämtar avbildningen och startas. Därefter binder den STDOUT och STDERR för din lokala konsol till behållarens.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

När `Server running at http://localhost:80` visas går du till behållarens FQDN i webbläsaren för att se programmet som körs:

![Skärmbild av exempelprogram som visas i en webbläsare][quick-build-02-browser]

Om du vill ta bort konsolen från behållaren trycker du på `Control+C`.

## <a name="clean-up-resources"></a>Rensa resurser

Stoppa behållarinstansen med kommandot [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Om du vill ta bort *alla* resurser som du har skapat i den här självstudien, inklusive behållarregistret, nyckelvalvet och tjänstens huvudnamn, utfärdar du följande kommandon. Dessa resurser används dock i [nästa självstudie](container-registry-tutorial-build-task.md) i serien, så du kan behålla dem om du går direkt vidare till nästa självstudie.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

Nu när du har testat din inre loop med att snabbskapa kan du konfigurera en **versionsuppgift** som utlöser behållaravbildningsversioner när du checkar in källkod på en Git-lagringsplats:

> [!div class="nextstepaction"]
> [Utlösa automatiska versioner med versionsuppgifter](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
