---
title: Montera en gitRepo volym Azure Container Instances
description: Lär dig hur du monterar en gitRepo volym för att klona en Git-lagringsplats till dina behållarinstanser
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: 1df251522c6ca14c5eb8967771fd609c04ffe2ea
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114951"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montera en gitRepo volym i Azure Container Instances

Lär dig hur du monterar en *gitRepo* volymen för att klona en Git-lagringsplats till dina behållarinstanser.

> [!NOTE]
> Montera en *gitRepo* volymen är för närvarande begränsade till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo-volymer

Den *gitRepo* volym monterar en katalog och klonar angivna Git-lagringsplatsen till den vid start. Med hjälp av en *gitRepo* volym i container instances, kan du undvika att lägga till kod för att göra det i dina program.

När du monterar en *gitRepo* volym, kan du ange tre egenskaper för att konfigurera volymen:

| Egenskap  | Krävs | Beskrivning |
| -------- | -------- | ----------- |
| `repository` | Ja | En fullständig URL, inklusive `http://` eller `https://`, Git-lagringsplatsen för kloning.|
| `directory` | Nej | Katalogen som dit databasen bör klonas. Sökvägen måste inte innehålla eller börja med ”`..`”.  Om du anger ”`.`”, databasen klonas till katalogen för den volymen. I annat fall klona på Git-lagringsplatsen i en underkatalog till det angivna namnet i katalogen volym. |
| `revision` | Nej | Commit-hash för revision kloning. Om inget anges den `HEAD` revision klonas. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo montera volymen: Azure CLI

Montera en gitRepo volym när du distribuerar behållarinstanser med den [Azure CLI](/cli/azure), varor den `--gitrepo-url` och `--gitrepo-mount-path` parametrar till den [az container skapa] [ az-container-create] kommando. Alternativt kan du ange katalogen i volymen för att klona till (`--gitrepo-dir`) och commit-hash för revision kloning (`--gitrepo-revision`).

Det här exemplet kommandot klonar den [aci-helloworld] [ aci-helloworld] exempelprogrammet i `/mnt/aci-helloworld` i container-instans:

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

Kontrollera gitRepo volymen har monterats genom att starta ett gränssnitt i behållaren med [az container exec] [ az-container-exec] och lista katalogen:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo montera volymen: Resource Manager

Montera en gitRepo volym när du distribuerar behållarinstanser med en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups), först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. Sedan för varje behållare i behållargruppen där du vill montera den *gitRepo* volym, Fyll i den `volumeMounts` matrisen i den `properties` avsnitt i behållardefinitionen.

Till exempel skapar följande Resource Manager-mallen en behållargrupp som består av en enskild behållare. Behållaren klonar två GitHub-lagringsplatser som anges av den *gitRepo* volymblock. Den andra volymen innehåller ytterligare egenskaper som anger en katalog för att klona till och commit-hash för en specifik version att klona.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json --> [!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Resulterande katalogstrukturen på två klonade databaser som definierats i föregående mall är:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ett exempel på distribution av behållarinstanser med en Azure Resource Manager-mall finns i [distribuera grupper med flera behållare i Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Privata Git-repo-autentisering

Ange autentiseringsuppgifter för att montera en gitRepo volym för en privata Git-lagringsplats i URL för databasen. Normalt autentiseringsuppgifter finns i form av ett användarnamn och en personlig åtkomsttoken (PAT) som ger begränsad åtkomst till databasen.

Till exempel Azure CLI `--gitrepo-url` parameter för en privat GitHub-lagringsplats skulle se ut som i följande (där ”gituser” är det GitHub-användarnamnet och ”abcdef1234fdsa4321abcdef” är användarens personliga åtkomsttoken):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Ange ett användarnamn (du kan använda ”azurereposuser” som i följande exempel) i kombination med en giltig PAT för en Azure-lagringsplatser Git-lagringsplatsen:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Mer information om personlig åtkomsttoken för GitHub och Azure-databaser finns i följande:

GitHub: [skapar en personlig åtkomsttoken för kommandoraden][pat-github]

Azure-databaser: [skapa personliga åtkomsttoken för att autentisera åtkomst][pat-repos]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper i Azure Container Instances:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en hemlig volym i Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
