---
title: ACR-uppgifts exempel
description: Exempel på Azure Container Registry uppgifter (ACR-aktiviteter) för att skapa, köra och uppdatera behållar avbildningar
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456095"
---
# <a name="acr-tasks-samples"></a>Exempel på ACR-uppgifter

Den här artikeln länkar till exempel `task.yaml` filer och tillhör ande Dockerfiles för flera [Azure Container Registry uppgifter](container-registry-tasks-overview.md) (ACR tasks). 

Fler exempel finns i Azure- [exemplen][task-examples] lagrings platsen.

## <a name="scenarios"></a>Scenarier

* **Bygg avbildning** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kör container** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Bygg och push-avbildning** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och kör avbildning** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygga och skicka flera avbildningar** -  [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygg och testa bilder parallellt** -  [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bygg och skicka avbildningar till flera register** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Nästa steg

Läs mer om ACR-uppgifter:

* [Aktiviteter med flera steg](container-registry-tasks-multi-step.md) – ACR uppgiftsbaserade arbets flöden för att skapa, testa och korrigera behållar avbildningar i molnet.
* [Uppgifts referens](container-registry-tasks-reference-yaml.md) – uppgifts typer, egenskaper och användning.
* [Cmd-lagrings platsen](https://github.com/AzureCR/cmd) – en samling behållare som kommandon för ACR-uppgifter.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
