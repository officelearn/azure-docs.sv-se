---
title: Montera gitRepo-volym till behållargrupp
description: Lär dig hur du monterar en gitRepo-volym för att klona en Git-lagringsplats i behållarinstanserna
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252089"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo-volym i Azure Container-instanser

Lär dig hur du monterar en *gitRepo-volym* för att klona en Git-databas i behållarinstanserna.

> [!NOTE]
> Montering av en *gitRepo-volym* är för närvarande begränsad till Linux-behållare. Medan vi arbetar för att få alla funktioner till Windows-behållare, kan du hitta aktuella plattformsskillnader i [översikten](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>gitRepo volym

*GitRepo-volymen* monterar en katalog och klonar den angivna Git-lagringsplatsen i den vid start av behållaren. Genom att använda en *gitRepo-volym* i behållarinstanserna kan du undvika att lägga till koden för att göra det i dina program.

När du monterar en *gitRepo-volym* kan du ange tre egenskaper för att konfigurera volymen:

| Egenskap | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | Den fullständiga URL:en, inklusive `http://` eller `https://`, för den Git-databas som ska klonas.|
| `directory` | Inga | Katalog som databasen ska klonas till. Banan får inte innehålla eller`..`börja med " ".  Om du`.`anger " ", klonas databasen till volymens katalog. Annars klonas Git-databasen till en underkatalog till det angivna namnet i volymkatalogen. |
| `revision` | Inga | Begå hashen av revideringen som ska klonas. Om det inte `HEAD` anges klonas revideringen. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Mount gitRepo volym: Azure CLI

Om du vill montera en gitRepo-volym när du `--gitrepo-url` distribuerar behållarinstanser med [Azure CLI,](/cli/azure)anger du och `--gitrepo-mount-path` parametrar till kommandot az container [create.][az-container-create] Du kan också ange katalogen inom volymen`--gitrepo-dir`att klona till ( ) och`--gitrepo-revision`commit hash av revideringen som ska klonas ( ).

Det här exemplet kommandot klonar Microsoft [aci-helloworld][aci-helloworld] exempelprogram i `/mnt/aci-helloworld` i behållarinstansen:

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

För att kontrollera att gitRepo-volymen var monterad startar du ett skal i behållaren med [az-behållarens exec][az-container-exec] och listar katalogen:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Mount gitRepo-volym: Resurshanteraren

Om du vill montera en gitRepo-volym när du distribuerar behållarinstanser `properties` med en Azure Resource [Manager-mall](/azure/templates/microsoft.containerinstance/containergroups)fyller du först i matrisen `volumes` i avsnittet behållargrupp i mallen. För varje behållare i behållargruppen där du vill montera *volymen gitRepo* `volumeMounts` fyller du `properties` i matrisen i avsnittet i behållardefinitionen.

I följande Resource Manager-mall skapas till exempel en behållargrupp som består av en enda behållare. Behållaren klonar två GitHub-databaser som anges av *gitRepo-volymblocken.* Den andra volymen innehåller ytterligare egenskaper som anger en katalog att klona till och commit-hash för en specifik revision att klona.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Den resulterande katalogstrukturen för de två klonade repor som definieras i föregående mall är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Information om hur du visar ett exempel på distribution av behållarinstans med en Azure Resource [Manager-mall finns i Distribuera flerbehållaresgrupper i Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Privat Git-repoautentisering

Om du vill montera en gitRepo-volym för en privat Git-databas anger du autentiseringsuppgifter i databasens URL. Vanligtvis är autentiseringsuppgifter i form av ett användarnamn och en personlig åtkomsttoken (PAT) som ger begränsad åtkomst till databasen.

Azure CLI-parametern `--gitrepo-url` för en privat GitHub-databas skulle till exempel likna följande (där "gituser" är GitHub-användarnamnet och "abcdef1234fdsa4321abcdef" är användarens personliga åtkomsttoken):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

För en Azure Repos Git-databas anger du vilket användarnamn som helst (du kan använda "azurereposuser" som i följande exempel) i kombination med en giltig PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Mer information om personliga åtkomsttoken för GitHub och Azure Repos finns i följande:

GitHub: [Skapa en personlig åtkomsttoken för kommandoraden][pat-github]

Azure Repos: [Skapa personliga åtkomsttoken för att autentisera åtkomst][pat-repos]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresurs i Azure Container Instances](container-instances-volume-azure-files.md)
* [Montera en emptyDir-volym i Azure Container-instanser](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
