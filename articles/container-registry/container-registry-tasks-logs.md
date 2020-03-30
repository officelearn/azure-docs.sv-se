---
title: Visa aktivitetskörningsloggar - Uppgifter
description: Så här visar och hanterar du körningsloggar som genereras av ACR-uppgifter.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246973"
---
# <a name="view-and-manage-task-run-logs"></a>Visa och hantera aktivitetskörningsloggar

Varje aktivitet som körs i [Azure Container Registry-uppgifter](container-registry-tasks-overview.md) genererar loggutdata som du kan granska för att avgöra om aktivitetsstegen kördes. 

I den här artikeln beskrivs hur du visar och hanterar aktivitetskörningsloggar.

## <a name="view-streamed-logs"></a>Visa strömmade loggar

När du utlöser en aktivitet manuellt strömmas loggutdata direkt till konsolen. När du till exempel utlöser en aktivitet manuellt med hjälp av kommandot [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run)eller az [acr task run](/cli/azure/acr/task#az-acr-task-run) visas loggutdata som strömmas till konsolen. 

Följande exempel på [az acr-körningskommando](/cli/azure/acr#az-acr-run) utlöser manuellt en aktivitet som kör en behållare som hämtats från samma register:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Strömmad logg:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Visa lagrade loggar 

Azure Container Registry lagrar köra loggar för alla uppgifter. Du kan visa lagrade körningsloggar i Azure-portalen. Du kan också använda kommandot [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) för att visa en markerad logg. Som standard behålls loggarna i 30 dagar.

Om en uppgift utlöses automatiskt, till exempel genom en källkodsuppdatering, är åtkomst till de lagrade loggarna det *enda* sättet att visa körningsloggarna. Automatiska uppgiftsutlösare inkluderar källkodsförseningar eller pull-begäranden, basavbildningsuppdateringar och timerutlösare.

Så här visar du körningsloggar i portalen:

1. Navigera till behållarregistret.
1. Välj **Uppgifter** > **körs**i **Tjänster**.
1. Välj ett **kör-ID** för att visa körningsstatus och köra loggar. Loggen innehåller samma information som en strömmad logg, om en sådan genereras.

![Visa inloggningsportal för aktivitetskörning](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Om du vill visa en logg med Azure CLI kör du [az acr-aktivitetsloggar](/cli/azure/acr/task#az-acr-task-logs) och anger ett körnings-ID, ett aktivitetsnamn eller en specifik avbildning som skapas av en bygguppgift. Om ett aktivitetsnamn anges visas loggen för den senast skapade körningen.

I följande exempel matas loggen ut med ID *cf4:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternativ logglagring

Du kanske vill lagra aktivitetskörningsloggar i ett lokalt filsystem eller använda en alternativ arkiveringslösning som Azure Storage.

Skapa till exempel en lokal *tasklogs-katalog* och omdirigera utdata från [az acr-aktivitetsloggar](/cli/azure/acr/task#az-acr-task-logs) till en lokal fil:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Du kan också spara lokala loggfiler i Azure Storage. Använd till exempel [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md)eller andra metoder för att överföra filer till ett lagringskonto.


## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure-behållarregisteruppgifter](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
