---
title: Extern autentisering från ACR-uppgift
description: Konfigurera en AZURE-administratörsregisteruppgift (ACR-uppgift) för att läsa Docker Hub-autentiseringsuppgifter som lagras i ett Azure-nyckelvalv med hjälp av en hanterad identitet för Azure-resurser.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842528"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Extern autentisering i en ACR-uppgift med hjälp av en Azure-hanterad identitet 

I en [ACR-aktivitet](container-registry-tasks-overview.md)kan du [aktivera en hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). Uppgiften kan använda identiteten för att komma åt andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en uppgift som kommer åt hemligheter som lagras i ett Azure-nyckelvalv. 

För att skapa Azure-resurser kräver den här artikeln att du kör Azure CLI version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Scenarioöversikt

Exempeluppgiften läser Docker Hub-autentiseringsuppgifter som lagras i ett Azure-nyckelvalv. Autentiseringsuppgifterna gäller för ett Docker Hub-konto med skriv-behörighet (push)-behörigheter till en privat Docker Hub-databas. Om du vill läsa autentiseringsuppgifterna konfigurerar du uppgiften med en hanterad identitet och tilldelar den lämpliga behörigheter. Uppgiften som är associerad med identiteten skapar en avbildning och loggar in på Docker Hub för att skicka avbildningen till den privata reporäntan. 

I det här exemplet visas steg med antingen en användartilldelad eller systemtilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan ett företag publicera bilder till en privat repa i Docker Hub som en del av en byggprocess. 

## <a name="prerequisites"></a>Krav

Du behöver ett Azure-behållarregister där du kör uppgiften. I den här artikeln heter det här registret *mitt register*. Ersätt med ditt eget registernamn i senare steg.

Om du inte redan har ett Azure-behållarregister läser du [Snabbstart: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md). Du behöver inte skicka avbildningar till registret ännu.

Du behöver också en privat databas i Docker Hub och ett Docker Hub-konto med behörighet att skriva till strömförsörjningen. I det här exemplet heter den här repoen *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Skapa ett nyckelvalv och lagra hemligheter

Om du behöver skapa du först en resursgrupp med namnet *myResourceGroup* på *eastus-platsen* med följande kommando [för az-gruppge:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az keyvault create][az-keyvault-create] för att skapa ett nyckelvalv. Var noga med att ange ett unikt nyckelvalvnamn. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Lagra de nödvändiga Docker Hub-autentiseringsuppgifterna i nyckelvalvet med kommandot [az keyvault secret set.][az-keyvault-secret-set] I dessa kommandon skickas värdena i miljövariabler:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

I ett verkligt scenario skulle hemligheter sannolikt fastställas och underhållas i en separat process.

## <a name="define-task-steps-in-yaml-file"></a>Definiera aktivitetssteg i YAML-fil

Stegen för den här exempeluppgiften definieras i en [YAML-fil](container-registry-tasks-reference-yaml.md). Skapa en `dockerhubtask.yaml` fil som namnges i en lokal arbetskatalog och klistra in följande innehåll. Var noga med att ersätta nyckelvalvets namn i filen med namnet på nyckelvalvet.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Aktivitetsstegen gör följande:

* Hantera hemliga autentiseringsuppgifter för att autentisera med Docker Hub.
* Autentisera med Docker Hub genom `docker login` att skicka hemligheterna till kommandot.
* Skapa en avbildning med hjälp av ett exempel på Dockerfile i repoan [azure-samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Skicka avbildningen till den privata Docker Hub-databasen.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användartilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användartilldelad identitet. Om du vill aktivera en systemtilldelad identitet i stället läser du [Alternativ 2: Skapa uppgift med systemtilldelad identitet](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa kommandot *Task dockerhubtask* genom att köra följande kommando [för att skapa a-filer.][az-acr-task-create] Aktiviteten körs utan en källkodskontext och kommandot `dockerhubtask.yaml` refererar till filen i arbetskatalogen. Parametern `--assign-identity` skickar resurs-ID för den användartilldelade identiteten. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med systemtilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en systemtilldelad identitet. Om du vill aktivera en användartilldelad identitet i stället läser du [Alternativ 1: Skapa uppgift med användartilldelade identitet](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Skapa uppgift

Skapa kommandot *Task dockerhubtask* genom att köra följande kommando [för att skapa a-filer.][az-acr-task-create] Aktiviteten körs utan en källkodskontext och kommandot `dockerhubtask.yaml` refererar till filen i arbetskatalogen. Parametern `--assign-identity` utan värde aktiverar den systemtilldelade identiteten för aktiviteten.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Bevilja identitetsåtkomst till nyckelvalvet

Kör följande [az keyvault set-policy-kommando][az-keyvault-set-policy] för att ange en åtkomstprincip för nyckelvalvet. I följande exempel kan identiteten läsa hemligheter från nyckelvalvet. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Om du vill kontrollera att aktiviteten där du aktiverade en hanterad identitet körs korrekt utlöser du aktiviteten manuellt med kommandot [az acr-aktivitetskörning.][az-acr-task-run] Parametern `--set` används för att skicka det privata repanamnet till aktiviteten. I det här exemplet är platshållarrespondnamnet *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

När aktiviteten körs visar utdata lyckad autentisering till Docker Hub och avbildningen har skapats och skjuts till den privata repoen:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Kontrollera om det finns en markering om`cf24` bilden trycks genom att leta efter taggen (i det här exemplet) i den privata Repo-strömförsörjningen för Docker Hub.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du aktiverar en hanterad identitet i en ACR-uppgift](container-registry-tasks-authentication-managed-identity.md).
* Se [YAML-referensen för ACR-uppgifter](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
