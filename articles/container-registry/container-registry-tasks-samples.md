---
title: EXEMPEL PÅ ACR-uppgift
description: Exempel på Azure Container Registry Tasks (ACR-uppgifter) för att skapa, köra och korrigera behållaravbildningar
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456095"
---
# <a name="acr-tasks-samples"></a>Exempel på ACR-uppgifter

Den här artikeln `task.yaml` länkar till exempelfiler och associerade Dockerfiler för flera ACR-uppgifter [(Azure Container Registry)](container-registry-tasks-overview.md) scenarier. 

Ytterligare exempel finns i repo-eran för [Azure-exempel.][task-examples]

## <a name="scenarios"></a>Scenarier

* **Skapa avbildning** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kör behållaren** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Skapa och pusha avbildning** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och köra avbildning** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och driva flera bilder** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och testa avbildningar parallellt** -  [med YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Skapa och skicka avbildningar** - till flera register[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Nästa steg

Läs mer om ACR-uppgifter:

* [Flerstegsuppgifter](container-registry-tasks-multi-step.md) - ACR-uppgiftsbaserade arbetsflöden för att skapa, testa och korrigera behållaravbildningar i molnet.
* [Aktivitetsreferens](container-registry-tasks-reference-yaml.md) - Stegtyper för uppgift, deras egenskaper och användning.
* [Cmd repo](https://github.com/AzureCR/cmd) - En samling behållare som kommandon för ACR-uppgifter.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
