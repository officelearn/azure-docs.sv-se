---
title: Montera en gitRepo volym Azure Behållarinstanser
description: Lär dig hur du monterar en gitRepo volym för att klona en Git-lagringsplats i behållarinstanser
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo volym i Azure Container instanser

Lär dig hur du monterar en *gitRepo* volymen för att klona en Git-lagringsplats i behållarinstanser.

> [!NOTE]
> Montera en *gitRepo* volym är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo volym

Den *gitRepo* volym monterar en katalog och klonar på angivna Git-lagringsplatsen till den vid start av behållare. Med hjälp av en *gitRepo* volym i behållarinstanser, du kan undvika att lägga till kod för att göra det i dina program.

När du monterar en *gitRepo* volym, kan du ange tre egenskaper att konfigurera volymen:

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | En fullständig URL inklusive `http://` eller `https://`, av Git-lagringsplats kloning.|
| `directory` | Nej | Katalogen där databasen ska klonas. Sökvägen måste inte innehålla eller börja med ”`..`”.  Om du anger ”`.`”, databasen klonas till katalogen för den volymen. I annat fall klonas på Git-lagringsplatsen i en underkatalog till det angivna namnet i katalogen volym. |
| `revision` | Nej | Commit-hash med revision kloning. Om inget anges den `HEAD` revision klonas. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montera gitRepo volym: Azure CLI

Montera en gitRepo volym när du distribuerar behållarinstanser med den [Azure CLI](/cli/azure), varor i `--gitrepo-url` och `--gitrepo-mount-path` parametrar för att den [az behållaren skapa] [ az-container-create] kommando. Du kan också ange volymen för att klona i katalogen (`--gitrepo-dir`) och commit-hash för revision kloning (`--gitrepo-revision`).

Detta kommando klonar den [aci helloworld] [ aci-helloworld] exempelprogrammet i `/mnt/aci-helloworld` i behållaren-instans:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Kontrollera gitRepo volymen har monterats genom att starta ett gränssnitt i behållaren med [az behållaren exec] [ az-container-exec] lista över katalogen och:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montera gitRepo volym: Resource Manager

Montera en gitRepo volym när du distribuerar behållarinstanser med en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups), först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. Sedan för varje behållare i behållargruppen där du vill montera den *gitRepo* volym, Fyll i den `volumeMounts` matris i den `properties` avsnitt i definition av behållare.

Till exempel skapas följande Resource Manager-mall en behållare grupp bestående av en enskild behållare. Behållaren klonar två GitHub-lagringsplatser som anges av den *gitRepo* volymblock. Den andra volymen innehåller ytterligare egenskaper som anger en katalog att klona till och commit-hash för en specifik version att klona.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Katalogstruktur för två klonade repor som definierats i mallen för föregående är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ett exempel på distribution av behållare-instans med en Azure Resource Manager-mall finns i [distribuera flera behållare grupper i Azure Behållarinstanser](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper av i Azure Container instanser:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container instanser](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec