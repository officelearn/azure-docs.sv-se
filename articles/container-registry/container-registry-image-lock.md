---
title: Låsa avbildningar
description: Ange attribut för en behållaravbildning eller databas så att den inte kan tas bort eller skrivas över i ett Azure-behållarregister.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659704"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Låsa en behållaravbildning i ett Azure-behållarregister

I ett Azure-behållarregister kan du låsa en avbildningsversion eller en databas så att den inte kan tas bort eller uppdateras. Om du vill låsa en avbildning eller en databas uppdaterar du dess attribut med azure CLI-kommandot [az acr repository update][az-acr-repository-update]. 

Den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!IMPORTANT]
> Den här artikeln gäller inte för att låsa ett helt register, till exempel med `az lock` hjälp av Inställningar > **Lås** i Azure-portalen eller kommandon i Azure CLI. Om du låser en registerresurs hindras inte du från att skapa, uppdatera eller ta bort data i databaser. Om du låser ett register påverkas endast hanteringsåtgärder som att lägga till eller ta bort replikeringar eller ta bort själva registret. Mer information i [Lås resurser för att förhindra oväntade ändringar](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenarier

Som standard kan en taggad avbildning i Azure Container Registry *gå inte att stänga av,* så med lämpliga behörigheter kan du upprepade gånger uppdatera och skicka en avbildning med samma tagg till ett register. Behållaravbildningar kan också [tas bort](container-registry-delete.md) efter behov. Det här problemet är användbart när du utvecklar avbildningar och behöver behålla en storlek för registret.

Men när du distribuerar en behållaravbildning till produktion kan du behöva en *oföränderlig* behållaravbildning. En oföränderlig bild är en bild som du inte av misstag kan ta bort eller skriva över.

Se [Rekommendationer för taggning och versionshantering av behållaravbildningar](container-registry-image-tag-version.md) för strategier för att tagga och versionsavbildningar i registret.

Använd kommandot [az acr-databasuppdatering][az-acr-repository-update] för att ange databasattribut så att du kan:

* Låsa en bildversion eller en hel databas

* Skydda en bildversion eller databas från borttagning, men tillåt uppdateringar

* Förhindra läs-(pull-) åtgärder på en bildversion eller en hel databas

Mer om följande avsnitt finns i följande avsnitt. 

## <a name="lock-an-image-or-repository"></a>Låsa en bild eller databas 

### <a name="show-the-current-repository-attributes"></a>Visa de aktuella databasattributen
Om du vill visa de aktuella attributen för en databas kör du följande kommando [för az acr-databasvisning:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Visa aktuella bildattribut
Om du vill visa de aktuella attributen för en tagg kör du följande kommando [för az acr-databasvisning:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Låsa en bild efter tagg

Om du vill låsa *myrepo/myimage:tag-avbildningen* i *mitt register*kör du följande kommando för [uppdatering av az-lagringsplatsen:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Låsa en bild genom manifestsammandrag

Om du vill låsa en *myrepo/myimage-bild* som identifierats av manifestsammandrag (SHA-256 hash, representerad som `sha256:...`), kör följande kommando. (Om du vill hitta manifestsammandraget som är associerat med en eller flera bildtaggar kör du kommandot [az acr repository show-manifests.)][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Låsa en databas

Om du vill låsa *myrepo/myimage-databasen* och alla bilder i den kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Skydda en bild eller databas från borttagning

### <a name="protect-an-image-from-deletion"></a>Skydda en bild från borttagning

Om du vill att *myrepo/myimage:tag-avbildningen* ska uppdateras men inte tas bort kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Skydda en databas från borttagning

Följande kommando anger *myrepo/myimage-databasen* så att den inte kan tas bort. Enskilda bilder kan fortfarande uppdateras eller tas bort.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Förhindra läsåtgärder i en bild eller databas

Om du vill förhindra att du läser (drar) på *myrepo/myimage:tag-avbildningen* kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Om du vill förhindra att läsåtgärder utförs på alla bilder i *myrepo/myimage-databasen* kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Låsa upp en bild eller databas

Om du vill återställa standardbeteendet för *myrepo/myimage:tag-avbildningen* så att den kan tas bort och uppdateras kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Om du vill återställa standardbeteendet för *myrepo/myimage-databasen* och alla bilder så att de kan tas bort och uppdateras kör du följande kommando:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig mer om hur du använder kommandot [az acr repository update][az-acr-repository-update] för att förhindra borttagning eller uppdatering av bildversioner i en databas. Information om hur du anger ytterligare attribut finns i kommandoreferensen för [az acr-databasuppdatering.][az-acr-repository-update]

Om du vill visa attributuppsättningen för en bildversion eller databas använder du kommandot [az acr repository show.][az-acr-repository-show]

Mer information om borttagningsåtgärder finns [i Ta bort behållaravbildningar i Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

