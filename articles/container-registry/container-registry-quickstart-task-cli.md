---
title: Snabbstart - Bygg & körningsbehållarevseende
description: Kör snabbt uppgifter med Azure Container Registry för att skapa och köra en Docker-behållaravbildning på begäran i molnet.
ms.topic: quickstart
ms.date: 01/31/2020
ms.openlocfilehash: f08f10dd170acaa8594ad5a47f5ef58e27288b10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76986282"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Snabbstart: Skapa och kör en behållaravbildning med Azure Container Registry Tasks

I den här snabbstarten använder du Azure Container Registry Tasks-kommandon för att snabbt skapa, skicka och köra en Docker-behållaravbildning internt i Azure, som visar hur du avlastar din utvecklingscykel för "inre loop" till molnet. [ACR-uppgifter][container-registry-tasks-overview] är en uppsättning funktioner i Azure Container Registry som hjälper dig att hantera och ändra behållaravbildningar över behållarens livscykel. 

Efter den här snabbstarten kan du utforska mer avancerade funktioner i ACR-uppgifter. ACR-uppgifter kan automatisera avbildningsversioner baserat på kod commits eller basavbildningsuppdateringar, eller testa flera behållare, parallellt, bland andra scenarier. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du vill använda den lokalt rekommenderas version 2.0.58 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du inte redan har ett behållarregister skapar du först en resursgrupp med kommandot [az group create.][az-group-create] En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Skapa ett behållarregister med kommandot [az acr create.][az-acr-create] Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används *myContainerRegistry008.* Uppdatera det här till ett unikt värde.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

I det här exemplet skapas ett *grundläggande* register, ett kostnadsoptimerat alternativ för utvecklare som lär sig mer om Azure Container Registry. Mer information om tillgängliga tjänstnivåer finns i [SKU:er för Azure Container Registry][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Skapa en avbildning från en Dockerfile

Använd nu Azure Container Registry för att skapa en avbildning. Skapa först en arbetskatalog och skapa sedan en Dockerfile med namnet *Dockerfile* med följande innehåll. Detta är ett enkelt exempel för att bygga en Linux-behållaravbildning, men du kan skapa din egen standard Dockerfile och bygga avbildningar för andra plattformar. Kommandoexempel i den här artikeln är formaterade för bash-skalet.

```bash
echo FROM hello-world > Dockerfile
```

Kör [az acr bygga][az-acr-build] kommandot för att bygga bilden. När den har skapats, är avbildningen skjuts till registret. I följande exempel `sample/hello-world:v1` skjuts bilden. I `.` slutet av kommandot anges dockerfilens plats, i det här fallet den aktuella katalogen.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

Utdata från en lyckad version och push liknar följande:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Kör bilden

Nu snabbt köra den bild du byggt och skjuts till registret. Här använder du [az acr kör][az-acr-run] för att köra behållarkommandot. I arbetsflödet för behållarutveckling kan det vara ett valideringssteg innan du distribuerar avbildningen, eller så kan du inkludera kommandot i en [YAML-fil][container-registry-tasks-multi-step]i flera steg . 

I följande `$Registry` exempel används för att ange registret där du kör kommandot:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Parametern `cmd` i det här exemplet kör behållaren i standardkonfigurationen, men `cmd` stöder ytterligare `docker run` parametrar eller till och med andra `docker` kommandon.

De utdata som genereras liknar följande:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [az group delete][az-group-delete] till att ta bort resursgruppen, containerregistret och containeravbildningarna som är lagrade där.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du funktioner i ACR-uppgifter för att snabbt skapa, skicka och köra en Docker-behållaravbildning internt i Azure, utan en lokal Docker-installation. Fortsätt till azure container registeruppgifter självstudier för att lära dig mer om hur du använder ACR-uppgifter för att automatisera avbildningsversioner och uppdateringar.

> [!div class="nextstepaction"]
> [Självstudier av Azure-behållarregisteruppgifter][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
