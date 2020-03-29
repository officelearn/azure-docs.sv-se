---
title: Autentisering över registret från ACR-uppgift
description: Konfigurera en AZURE-behållarregisteruppgift (ACR-uppgift) för att komma åt ett annat privat Azure-behållarregister med hjälp av en hanterad identitet för Azure-resurser
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47b2a50784cf56b089fea0981e5a06d581b8ba3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842509"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autentisering över registret i en ACR-uppgift med hjälp av en Azure-hanterad identitet 

I en [ACR-aktivitet](container-registry-tasks-overview.md)kan du [aktivera en hanterad identitet för Azure-resurser](container-registry-tasks-authentication-managed-identity.md). Uppgiften kan använda identiteten för att komma åt andra Azure-resurser, utan att behöva ange eller hantera autentiseringsuppgifter. 

I den här artikeln får du lära dig hur du aktiverar en hanterad identitet i en uppgift för att hämta en avbildning från ett register som skiljer sig från det som används för att köra uppgiften.

För att skapa Azure-resurser kräver den här artikeln att du kör Azure CLI version 2.0.68 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Scenarioöversikt

Exempeluppgiften hämtar en basavbildning från ett annat Azure-behållarregister för att skapa och skicka en programavbildning. Om du vill hämta basavbildningen konfigurerar du uppgiften med en hanterad identitet och tilldelar den lämpliga behörigheter. 

I det här exemplet visas steg med antingen en användartilldelad eller systemtilldelad hanterad identitet. Ditt val av identitet beror på organisationens behov.

I ett verkligt scenario kan en organisation underhålla en uppsättning basavbildningar som används av alla utvecklingsteam för att skapa sina program. Dessa basavbildningar lagras i ett företagsregister, där varje utvecklingsteam bara har pull-rättigheter. 

## <a name="prerequisites"></a>Krav

I den här artikeln behöver du två Azure-behållarregister:

* Du kan använda det första registret för att skapa och köra ACR-uppgifter. I den här artikeln heter det här registret *mitt register*. 
* Det andra registret är värd för en basavbildning som används för att skapa en avbildning. I den här artikeln heter det andra registret *mybaseregistry*. 

Ersätt med dina egna registernamn i senare steg.

Om du inte redan har de nödvändiga Azure-behållarregister kan du se [Snabbstart: Skapa ett privat behållarregister med Azure CLI](container-registry-get-started-azure-cli.md). Du behöver inte skicka avbildningar till registret ännu.

## <a name="prepare-base-registry"></a>Förbereda basregistret

Skapa först en arbetskatalog och skapa sedan en fil med namnet Dockerfile med följande innehåll. Det här enkla exemplet bygger en Node.js-basavbildning från en offentlig avbildning i Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
I den aktuella katalogen kör du kommandot [az acr build][az-acr-build] för att skapa och skicka basavbildningen till basregistret. I praktiken kan ett annat team eller en annan process i organisationen underhålla basregistret.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definiera aktivitetssteg i YAML-fil

Stegen för den här [exempelaktiviteten i flera steg](container-registry-tasks-multi-step.md) definieras i en [YAML-fil](container-registry-tasks-reference-yaml.md). Skapa en `helloworldtask.yaml` fil som heter i den lokala arbetskatalogen och klistra in följande innehåll. Uppdatera värdet `REGISTRY_NAME` för i byggsteget med servernamnet för basregistret.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Byggsteget `Dockerfile-app` använder filen i [Azure-Samples/acr-build-helloworld-nod](https://github.com/Azure-Samples/acr-build-helloworld-node.git) repo för att skapa en avbildning. Referenserna `--build-arg` till basregistret för att hämta basavbildningen. När den har skapats, är avbildningen skjuts till registret som används för att köra uppgiften.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Alternativ 1: Skapa uppgift med användartilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en användartilldelad identitet. Om du vill aktivera en systemtilldelad identitet i stället läser du [Alternativ 2: Skapa uppgift med systemtilldelad identitet](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask* genom att köra följande [az acr-aktivitetsskapande][az-acr-task-create] kommando. Aktiviteten körs utan en källkodskontext och kommandot `helloworldtask.yaml` refererar till filen i arbetskatalogen. Parametern `--assign-identity` skickar resurs-ID för den användartilldelade identiteten. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Alternativ 2: Skapa uppgift med systemtilldelad identitet

Stegen i det här avsnittet skapar en uppgift och aktiverar en systemtilldelad identitet. Om du vill aktivera en användartilldelad identitet i stället läser du [Alternativ 1: Skapa uppgift med användartilldelade identitet](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Skapa uppgift

Skapa uppgiften *helloworldtask* genom att köra följande [az acr-aktivitetsskapande][az-acr-task-create] kommando. Aktiviteten körs utan en källkodskontext och kommandot `helloworldtask.yaml` refererar till filen i arbetskatalogen. Parametern `--assign-identity` utan värde aktiverar den systemtilldelade identiteten för aktiviteten. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Ge behörighet för identitetshandtag till basregistret

I det här avsnittet ger du de hanterade identitetsbehörigheterna för att hämta från basregistret, *mybaseregistry*.

Använd kommandot [az acr visa][az-acr-show] för att hämta resurs-ID för basregistret och lagra det i en variabel:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Använd kommandot [az role assignment create][az-role-assignment-create] `acrpull` för att tilldela rollen rollen till basregistret. Den här rollen har endast behörighet att hämta avbildningar från registret.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Lägga till autentiseringsuppgifter för målregister i uppgiften

Använd nu kommandot [az acr task-autentiseringsuppgifter][az-acr-task-credential-add] för att aktivera uppgiften att autentisera med basregistret med identitetens autentiseringsuppgifter. Kör kommandot som motsvarar den typ av hanterad identitet som du har aktiverat i aktiviteten. Om du har aktiverat en användartilldelad identitet skickar du `--use-identity` med klient-ID för identiteten. Om du har aktiverat en systemtilldelad identitet passerar du `--use-identity [system]`.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Kör uppgiften manuellt

Om du vill kontrollera att aktiviteten där du aktiverade en hanterad identitet körs korrekt utlöser du aktiviteten manuellt med kommandot [az acr-aktivitetskörning.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Om aktiviteten körs korrekt liknar utdata:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Kör kommandot [az acr-databasvisningstaggar][az-acr-repository-show-tags] för att verifiera att avbildningen har skapats och har skickats till *mitt register:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Exempel på utdata:

```console
cf10
```

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
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
