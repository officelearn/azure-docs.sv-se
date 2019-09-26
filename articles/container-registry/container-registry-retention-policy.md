---
title: Princip för att behålla otaggade manifest i Azure Container Registry
description: Lär dig hur du aktiverar en bevarande princip i ditt Azure Container Registry för automatisk borttagning av otaggade manifest efter en definierad period.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306008"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ange en bevarande princip för otaggade manifest

Azure Container Registry ger dig möjlighet att ange en *bevarande princip* för lagrade avbildnings manifest som inte har några associerade Taggar (*otaggade manifest*). När en bevarande princip aktive ras raderas otaggade manifest i registret automatiskt efter ett antal dagar som du anger. Den här funktionen förhindrar att registret fyller i artefakter som inte behövs och hjälper dig att spara pengar på lagrings kostnaderna. Om attributet för ett otaggade manifest är inställt på `false`, kan manifestet inte tas bort och bevarande principen gäller inte. `delete-enabled`

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra kommando exemplen i den här artikeln. Om du vill använda det lokalt, krävs version 2.0.74 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Ange en bevarande princip med försiktighet-borttagna bilddata kan inte ÅTERSTÄLLAs. Om du har system som hämtar bilder av manifest sammandrag (i stället för avbildnings namn) bör du inte ange en bevarande princip för otaggade manifest. Om du tar bort otaggade bilder så förhindras dessa system från att hämta avbildningarna från registret. Överväg att använda ett unikt taggnings schema i stället för att hämta ett *unikt taggnings* schema, en [rekommenderad metod](container-registry-image-tag-version.md).

Om du vill ta bort enstaka bildtaggar eller manifest med hjälp av Azure CLI-kommandon, se [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Endast ett **Premium** container Registry kan konfigureras med en bevarande princip. Information om register tjänst nivåer finns i [Azure Container Registry SKU: er](container-registry-skus.md).
* Du kan bara ange en bevarande princip för otaggade manifest.

## <a name="set-a-retention-policy---cli"></a>Ange en bevarande princip – CLI

I följande exempel visas hur du använder Azure CLI för att ange en bevarande princip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en bevarande princip

Som standard är ingen bevarande princip inställd i ett behållar register. Om du vill ange eller uppdatera en bevarande princip kör du kommandot [AZ ACR config rekvarhållning Update][az-acr-config-retention-update] i Azure CLI. Du kan ange ett antal dagar mellan 0 och 365 för att behålla de otaggade manifesten. Om du inte anger ett antal dagar anger kommandot standardvärdet 7 dagar. Alla otaggade manifest i registret tas bort automatiskt efter kvarhållningsperioden.

I följande exempel anges en bevarande princip på 30 dagar för otaggade manifest *i registret registret:*

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

I följande exempel anges en princip för att ta bort alla manifest i registret så fort den är otaggade. Skapa den här principen genom att ange en kvarhållningsperiod på 0 dagar:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Inaktivera en bevarande princip

Om du vill se bevarande principen som angetts i ett register kör du kommandot [AZ ACR config rekvarhållning show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --name myregistry
```

Om du vill inaktivera en bevarande princip i ett register kör du kommandot [AZ ACR config rekvarhållning Update][az-acr-config-retention-update] och anger `--status disabled`:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Ange en bevarande princip – Portal

Du kan också ange ett registers bevarande princip i [Azure Portal](https://portal.azure.com). I följande exempel visas hur du använder portalen för att ställa in en bevarande princip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en bevarande princip

1. Navigera till ditt Azure Container Registry. Under **principer**väljer du **kvarhållning** (för hands version).
1. I **status**väljer du **aktive rad**.
1. Välj ett antal dagar mellan 0 och 365 för att behålla de otaggade manifesten. Välj **Spara**.

![Aktivera en bevarande princip i Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Inaktivera en bevarande princip

1. Navigera till ditt Azure Container Registry. Under **principer**väljer du **kvarhållning** (för hands version).
1. I **status**väljer du **inaktive rad**. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om alternativ för att [ta bort bilder och databaser](container-registry-delete.md) i Azure Container Registry

* Lär dig hur du [automatiskt tömmer](container-registry-auto-purge.md) markerade bilder och manifest från ett register

* Lär dig mer om alternativ för att [låsa bilder och manifest](container-registry-image-lock.md) i ett register

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
