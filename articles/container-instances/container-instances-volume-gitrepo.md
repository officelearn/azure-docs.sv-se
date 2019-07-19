---
title: Montera en gitRepo volym Azure Container Instances
description: Lär dig hur du monterar en gitRepo-volym för att klona en git-lagringsplats till dina behållar instanser
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: e8afa9e14941920cdcfb984e6660bdc666240716
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325445"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo volym i Azure Container Instances

Lär dig hur du monterar en *gitRepo* -volym för att klona en git-lagringsplats till dina behållar instanser.

> [!NOTE]
> Att montera en *gitRepo* volym är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att hämta alla funktioner till Windows-behållare kan du hitta aktuella plattforms skillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo volym

*GitRepo* -volymen monterar en katalog och klonar angiven git-lagringsplats till den vid behållarens start. Genom att använda en *gitRepo* -volym i behållar instanserna kan du undvika att lägga till koden för att göra det i dina program.

När du monterar en *gitRepo* -volym kan du ange tre egenskaper för att konfigurera volymen:

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | Den fullständiga URL: en `http://` , `https://`inklusive eller, av Git-lagringsplatsen som ska klonas.|
| `directory` | Nej | Katalogen där lagrings platsen ska klonas. Sökvägen får inte innehålla eller börja med "`..`".  Om du anger "`.`", klonas lagrings platsen i volymens katalog. Annars klonas git-lagringsplatsen i en under katalog med det aktuella namnet i volym katalogen. |
| `revision` | Nej | Commit hash för den revision som ska klonas. Om inget anges `HEAD` klonas ändringen. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montera gitRepo-volym: Azure CLI

Om du vill montera en gitRepo volym när du distribuerar container instances med [Azure CLI](/cli/azure), `--gitrepo-url` anger `--gitrepo-mount-path` du parametrarna och för kommandot [AZ container Create][az-container-create] . Du kan också ange katalogen i den volym som ska klonas till (`--gitrepo-dir`) och commit hash för den revision som ska klonas (`--gitrepo-revision`).

Det här exempel kommandot klonar Microsoft [ACI-HelloWorld-][aci-helloworld] exempel programmet `/mnt/aci-helloworld` till i behållar instansen:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

För att kontrol lera att gitRepo-volymen har monterats startar du ett gränssnitt i behållaren med [AZ container exec][az-container-exec] och listar katalogen:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montera gitRepo-volym: Resource Manager

Om du vill montera en gitRepo-volym när du distribuerar behållar instanser med en [Azure Resource Manager mall](/azure/templates/microsoft.containerinstance/containergroups), fyller du `volumes` först `properties` i matrisen i behållar grupp avsnittet i mallen. För varje behållare i behållar gruppen där du vill montera *gitRepo* -volymen fyller `volumeMounts` du sedan i matrisen i `properties` avsnittet i behållar definitionen.

Följande Resource Manager-mall skapar till exempel en behållar grupp som består av en enda behållare. Behållaren klonar två GitHub-databaser som anges av *gitRepo* -volym blocken. Den andra volymen innehåller ytterligare egenskaper som anger en katalog som ska klonas till och commit hash för en särskild revision att klona.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Den resulterande katalog strukturen för de två klonade databaser som definieras i föregående mall är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Om du vill se ett exempel på distribution av container instanser med en Azure Resource Manager-mall, se [distribuera grupper med flera behållare i Azure Container instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Privat git lagrings platsen-autentisering

Om du vill montera en gitRepo-volym för en privat git-lagringsplats anger du autentiseringsuppgifterna i lager platsens URL. Normalt är autentiseringsuppgifter i form av ett användar namn och en personlig åtkomsttoken (PAT) som ger begränsad åtkomst till lagrings platsen.

Azure CLI `--gitrepo-url` -parametern för en privat GitHub-lagringsplats skulle till exempel se ut ungefär så här (där "gituser" är GitHub användar namn och "abcdef1234fdsa4321abcdef" är användarens personliga åtkomsttoken):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

För en Azure databaser git-lagringsplats anger du ett användar namn (du kan använda "azurereposuser" som i följande exempel) i kombination med en giltig PAT:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Mer information om personliga åtkomsttoken för GitHub och Azure databaser finns i följande avsnitt:

GitHub: [Skapa en personlig åtkomsttoken för kommando raden][pat-github]

Azure Repos: [Skapa personliga åtkomsttoken för att autentisera åtkomst][pat-repos]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volym typer i Azure Container Instances:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
