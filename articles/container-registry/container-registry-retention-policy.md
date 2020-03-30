---
title: Princip för att behålla otaggade manifest
description: Lär dig hur du aktiverar en bevarandeprincip i ditt Azure-behållarregister, för automatisk borttagning av otaggade manifest efter en definierad period.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454808"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ange en bevarandeprincip för otaggade manifest

Azure Container Registry ger dig möjlighet att ange en *bevarandeprincip* för lagrade avbildningsmanifest som inte har några associerade taggar (*otaggade manifest*). När en bevarandeprincip är aktiverad tas otaggade manifest i registret automatiskt bort efter ett antal dagar som du anger. Den här funktionen förhindrar att registret fylls med artefakter som inte behövs och hjälper dig att spara på lagringskostnader. Om `delete-enabled` attributet för ett otaggat `false`manifest är inställt på kan manifestet inte tas bort och bevarandeprincipen gäller inte.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra kommandoexemplen i den här artikeln. Om du vill använda den lokalt krävs version 2.0.74 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!IMPORTANT]
> Den här funktionen är för närvarande i förhandsversion och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

> [!WARNING]
> Ange en bevarandeprincip med vård--borttagna bilddata är unrecoverable. Om du har system som hämtar bilder genom manifestsammandrag (i motsats till bildnamn) bör du inte ange en bevarandeprincip för otaggade manifest. Om du tar bort otaggade avbildningar hindras dessa system från att dra bilderna från registret. Istället för att dra genom manifest, överväga att anta ett *unikt taggningsschema,* en [rekommenderad bästa praxis](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

* Endast ett **Premium-behållarregister** kan konfigureras med en bevarandeprincip. Information om registertjänstnivåer finns i [Azure Container Registry SKU: er](container-registry-skus.md).
* Du kan bara ange en bevarandeprincip för otaggade manifest.
* Bevarandeprincipen gäller för närvarande endast för manifest som inte har taggats *när* principen har aktiverats. Befintliga otaggade manifest i registret omfattas inte av principen. Information om hur du tar bort befintliga otaggade manifest finns i Exempel i [Ta bort behållaravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Om bevarandeprincipen

Azure Container Registry refererar till inventering för manifest i registret. När ett manifest är otaggat kontrollerar det bevarandeprincipen. Om en bevarandeprincip är aktiverad ställs en manifestborttagningsåtgärd i kö, med ett visst datum, beroende på antalet dagar som anges i principen.

Ett separat köhanteringsjobb bearbetar ständigt meddelanden och skalar efter behov. Anta till exempel att du har taggat två manifest med en timmes mellanrum i ett register med en bevarandeprincip på 30 dagar. Två meddelanden skulle köas. Sedan, 30 dagar senare, cirka 1 timmes mellanrum, skulle meddelandena hämtas från kön och bearbetas, förutsatt att principen fortfarande var i kraft.

## <a name="set-a-retention-policy---cli"></a>Ange en bevarandeprincip - CLI

I följande exempel visas hur du använder Azure CLI för att ange en bevarandeprincip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en bevarandeprincip

Som standard anges ingen bevarandeprincip i ett behållarregister. Om du vill ange eller uppdatera en bevarandeprincip kör du kommandot [az acr config retention update][az-acr-config-retention-update] i Azure CLI. Du kan ange ett antal dagar mellan 0 och 365 om du vill behålla de otaggade manifesten. Om du inte anger ett antal dagar anger kommandot en standard på 7 dagar. Efter kvarhållningsperioden tas alla otaggade manifest i registret bort automatiskt.

I följande exempel anges en bevarandeprincip på 30 dagar för otaggade manifest i registret *i mitt register:*

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

I följande exempel anges en princip för att ta bort alla manifest i registret så snart det är otaggat. Skapa den här principen genom att ange en kvarhållningsperiod på 0 dagar. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Validera en bevarandeprincip

Om du aktiverar föregående princip med en kvarhållningsperiod på 0 dagar kan du snabbt kontrollera att otaggade manifest tas bort:

1. Skicka en `hello-world:latest` testavbildningsavbildning till registret eller ersätt en annan testavbildning som du väljer.
1. Ta bort `hello-world:latest` bild av bilden, till exempel med kommandot [az acr repository untag.][az-acr-repository-untag] Det otaggade manifestet finns kvar i registret.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Inom några sekunder tas det otaggade manifestet bort. Du kan verifiera borttagningen genom att lista manifest i databasen, till exempel med kommandot [az acr-databas visa manifest.][az-acr-repository-show-manifests] Om testavbildningen var den enda i databasen tas själva databasen bort.

### <a name="disable-a-retention-policy"></a>Inaktivera en bevarandeprincip

Om du vill visa bevarandeprincipuppsättningen i ett register kör du kommandot [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Om du vill inaktivera en bevarandeprincip i ett register kör du kommandot [az acr config retention update][az-acr-config-retention-update] och anger: `--status disabled`

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Ange en bevarandeprincip - portal

Du kan också ange en registerbevarandeprincip i [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du använder portalen för att ange en bevarandeprincip för otaggade manifest i ett register.

### <a name="enable-a-retention-policy"></a>Aktivera en bevarandeprincip

1. Navigera till ditt Azure-behållarregister. Under **Principer**väljer du **Kvarhållning** (förhandsgranskning).
1. Välj **Aktiverad**i **Status**.
1. Välj ett antal dagar mellan 0 och 365 om du vill behålla de otaggade manifesten. Välj **Spara**.

![Aktivera en bevarandeprincip i Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Inaktivera en bevarandeprincip

1. Navigera till ditt Azure-behållarregister. Under **Principer**väljer du **Kvarhållning** (förhandsgranskning).
1. Välj **Inaktiverad**i **Status**. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

* Läs mer om alternativ för att [ta bort avbildningar och databaser](container-registry-delete.md) i Azure Container Registry

* Lär dig hur du [automatiskt rensar](container-registry-auto-purge.md) valda avbildningar och manifest från ett register

* Läs mer om alternativ för att [låsa avbildningar och manifest](container-registry-image-lock.md) i ett register

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
