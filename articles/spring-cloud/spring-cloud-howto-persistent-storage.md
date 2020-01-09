---
title: Använda beständig lagring i Azure våren Cloud | Microsoft Docs
description: Använda beständig lagring i Azure våren Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 68f893c694369d95dd82b9e5af3d08d67be78884
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461648"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Använda beständig lagring i Azure Spring Cloud

Azure våren Cloud tillhandahåller två typer av lagring för ditt program: beständigt och temporärt.

Som standard tillhandahåller Azure våren Cloud tillfällig lagring för varje program instans. Den tillfälliga lagringen är begränsad till 5 GB per instans med standard monterings Sök vägen/tmp.

> [!WARNING]
> Om du startar om en program instans tas den tillhör ande tillfälliga lagringen bort permanent.

Beständig lagring är en fil resurs behållare som hanteras av Azure och tilldelas per program. Data som lagras i beständig lagring delas av alla instanser av ett program. En Azure våren-moln instans kan ha högst 10 program med beständig lagring aktive rad. Varje program tilldelas 50 GB beständig lagring. Standard monterings vägen för beständigt lagrings utrymme är/persistent.

> [!WARNING]
> Om du inaktiverar ett programs permanenta lagrings utrymme frigörs all lagring och alla lagrade data går förlorade.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Använd Azure Portal för att aktivera beständig lagring

1. Välj **alla resurser**på **Start** sidan för din Azure Portal.

    >![Leta upp ikonen alla resurser](media/portal-all-resources.jpg)

1. Välj moln resursen Azure våren som behöver beständigt lagrings utrymme. I det här exemplet kallas det valda programmet för att bli av **med.**

    > ![Välj ditt program](media/select-service.jpg)

1. Under **inställnings** rubriken väljer du **appar**.

1. Dina Azure våren Cloud-tjänster visas i en tabell.  Välj den tjänst som du vill lägga till beständig lagring för. I det här exemplet är **Gateway** -tjänsten vald.

    > ![Välj din tjänst](media/select-gateway.jpg)

1. På sidan konfiguration av tjänsten väljer du **konfiguration**

1. Välj fliken **beständig lagring** och välj **Aktivera**.

    > ![Aktivera beständig lagring](media/enable-persistent-storage.jpg)

När beständig lagring har Aktiver ATS visas dess storlek och sökväg på konfigurations sidan.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Använd Azure CLI för att ändra beständig lagring

Om det behövs installerar du våren Cloud-tillägget för Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Andra åtgärder:

* Så här skapar du en app med beständig lagring aktive rad:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Så här aktiverar du beständigt lagrings utrymme för en befintlig app:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Så här inaktiverar du beständig lagring i en befintlig app:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Om du inaktiverar ett programs permanenta lagrings utrymme frigörs all lagring och alla lagrade data går förlorade permanent.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [program-och tjänst kvoter](spring-cloud-quotas.md).
* Lär dig hur du [skalar ditt program manuellt](spring-cloud-tutorial-scale-manual.md).
