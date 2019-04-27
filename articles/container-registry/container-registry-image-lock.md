---
title: Låsa en avbildning i Azure Container Registry
description: Ange attribut för en behållaravbildning eller så inte kan tas bort eller över i ett Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828655"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Låsa en behållaravbildning i ett Azure container registry

I ett Azure container registry kan du låsa versionsnumret för avbildningen eller en databas så att den inte kan tas bort eller uppdaterats. Uppdatera dess attribut med hjälp av Azure CLI-kommando för att låsa en avbildning eller en databas, [az acr databasen update][az-acr-repository-update]. 

Den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

## <a name="scenarios"></a>Scenarier

Som standard en taggade avbildningen i Azure Container Registry är *föränderliga*, så med rätt behörighet kan du upprepade gånger uppdatera och skicka en avbildning med samma tagg till ett register. Behållaravbildningar kan också vara [bort](container-registry-delete.md) efter behov. Detta är användbart när du utvecklar avbildningar och måste vara en storlek för ditt register.

Men när du distribuerar en behållaravbildning till produktion kan du kanske måste en *oföränderligt* behållaravbildning. En oföränderlig avbildning är en som du inte kan ta bort eller skriva över av misstag. Använd den [az acr databasen update] [ az-acr-repository-update] kommando för att ange databasattribut så att du kan:

* Låsa en Bildversion eller en hel databas

* Skydda en Avbildningsversion eller databasen tas bort av, men Tillåt uppdateringar

* Förhindra läsåtgärder (pull) på en Bildversion eller en hel databas

Se exemplen i följande avsnitt.

## <a name="lock-an-image-or-repository"></a>Låsa en bild eller databasen 

### <a name="show-the-current-repository-attributes"></a>Visa de aktuella databasattribut
Om du vill se de aktuella attribut för en databas, kör du följande [az acr databasen show] [ az-acr-repository-show] kommando:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Visa aktuella bildattribut
Om du vill se de aktuella attribut för en tagg, kör du följande [az acr databasen show] [ az-acr-repository-show] kommando:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Låsa en avbildning efter tagg

För att låsa den *myrepo / myimage:tag* bild i *myregistry*, kör du följande [az acr databasen update] [ az-acr-repository-update] kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Låsa en avbildning med manifest sammanfattad

För att låsa en *myrepo/myimage* avbildningen som identifieras av manifest sammanfattad (SHA-256-hash som `sha256:...`), kör du följande kommando. (För att hitta manifestfilen sammanfattad som är associerade med en eller flera taggar, kör den [az acr databasen show-manifest] [ az-acr-repository-show-manifests] kommandot.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Låsa en databas

För att låsa den *myrepo/myimage* lagringsplatsen och alla avbildningar, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Skydda en bild eller databasen tas bort av

### <a name="protect-an-image-from-deletion"></a>Skydda en avbildning från borttagning

Att tillåta den *myrepo / myimage:tag* bilden som ska uppdateras, men inte ta bort, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Skydda en databas från borttagning

Följande kommando anger den *myrepo/myimage* databasen så att den inte kan tas bort. Enskilda bilder kan fortfarande uppdateras eller tas bort.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Förhindra läsåtgärder på en bild eller databasen

Att förhindra läsåtgärder (pull) på den *myrepo / myimage:tag* bild, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Att förhindra läsåtgärder på alla avbildningar i den *myrepo/myimage* databasen, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Låsa upp en bild eller databasen

Att återställa standardbeteendet för den *myrepo / myimage:tag* bild så att den kan tas bort och uppdateras, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Att återställa standardbeteendet för den *myrepo/myimage* lagringsplatsen och alla avbildningar så att de kan tas bort och uppdateras, kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om hur du använder den [az acr databasen update] [ az-acr-repository-update] kommando för att förhindra borttagning eller uppdatering av avbildningsversioner i en databas. Om du vill ange ytterligare attribut finns i den [az acr databasen update] [ az-acr-repository-update] kommandot referens.

Om du vill se attributen för en Avbildningsversion eller databasen, använder den [az acr databasen show] [ az-acr-repository-show] kommando.

Mer information om borttagning finns [ta bort avbildningar i Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

