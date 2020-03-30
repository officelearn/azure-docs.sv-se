---
title: Så här använder du beständig lagring i Azure Spring Cloud | Microsoft-dokument
description: Så här använder du beständig lagring i Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278537"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Använda beständig lagring i Azure Spring Cloud

Azure Spring Cloud innehåller två typer av lagring för ditt program: beständig och tillfällig.

Som standard tillhandahåller Azure Spring Cloud tillfällig lagring för varje programinstans. Tillfällig lagring är begränsad till 5 GB per instans med standardmonterad bana /tmp.

> [!WARNING]
> Om du startar om en programinstans tas den associerade tillfälliga lagringen bort permanent.

Beständig lagring är en filresursbehållare som hanteras av Azure och allokeras per program. Data som lagras i beständig lagring delas av alla instanser av ett program. En Azure Spring Cloud-instans kan ha högst 10 program med beständig lagring aktiverad. Varje program tilldelas 50 GB beständig lagring. Standardmonterasökvägen för beständig lagring är /persistent.

> [!WARNING]
> Om du inaktiverar ett programs beständiga lagring frigörs all lagring och alla lagrade data går förlorade.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Använda Azure-portalen för att aktivera beständig lagring

1. Välj **Alla resurser**på **startsidan** för din Azure-portal .

    >![Leta reda på ikonen Alla resurser](media/portal-all-resources.jpg)

1. Välj azure spring cloud-resursen som behöver beständig lagring. I det här exemplet anropas det valda programmet **uppspjänande**.

    > ![Välj ditt program](media/select-service.jpg)

1. Under rubriken **Inställningar** väljer du **Appar**.

1. Dina Azure Spring Cloud-tjänster visas i en tabell.  Välj den tjänst som du vill lägga till beständig lagring till. I det här exemplet är **gateway-tjänsten** markerad.

    > ![Välj din tjänst](media/select-gateway.jpg)

1. Välj **Konfigurationssida** på tjänstens konfigurationssida

1. Välj fliken **Beständigt lagringsutrymme** och välj **Aktivera**.

    > ![Aktivera beständig lagring](media/enable-persistent-storage.jpg)

När beständig lagring har aktiverats visas dess storlek och sökväg på konfigurationssidan.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Använda Azure CLI för att ändra beständig lagring

Om det behövs installerar du springmoln-tillägget för Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Övriga verksamheter:

* Så här skapar du en app med beständig lagring aktiverad:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Så här aktiverar du beständig lagring för en befintlig app:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Så här inaktiverar du beständig lagring i en befintlig app:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Om du inaktiverar ett programs beständiga lagring frigörs all lagring och alla lagrade data går förlorade permanent.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [program- och tjänstkvoter](spring-cloud-quotas.md).
* Lär dig hur du [skalar programmet manuellt](spring-cloud-tutorial-scale-manual.md).
