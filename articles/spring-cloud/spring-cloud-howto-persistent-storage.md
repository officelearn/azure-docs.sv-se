---
title: Använda beständig lagring i Azure våren Cloud | Microsoft Docs
description: Använda beständig lagring i Azure våren Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: f14da7d8e64c58a54a9da4d851ac22fd710ea8ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163714"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Använda beständig lagring i Azure våren Cloud

Azure våren Cloud tillhandahåller två typer av lagring för ditt program: beständigt och temporärt.  Azure våren Cloud möjliggör tillfällig lagring som standard för varje program instans. Den tillfälliga lagringen är begränsad till 5 GB med en standard monterings Sök väg: `/tmp`.

> [!WARNING]
> Om du startar om en program instans raderas den tillhör ande tillfälliga lagringen permanent.

Beständig lagring är en fil resurs behållare som hanteras av Azure som tilldelas per program. Data som lagras i beständig lagring delas i alla instanser av programmet. En Azure våren moln tjänst instans kan ha högst 10 program med beständig disk aktive rad. Varje program får 50 GB beständig lagring. Standard monterings Sök vägen för beständigt lagring är `/persistent`.

> [!WARNING]
> Om du *inaktiverar* beständig lagring frigörs lagringen för programmet.  Alla data i det lagrings kontot kommer att gå förlorade. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Aktivera beständig lagring med hjälp av Azure Portal

1. Från Start skärmen i Azure Portal väljer du **alla resurser**.

     >![Leta upp ikonen alla resurser](media/portal-all-resources.jpg)

1. Hitta och välj moln resursen Azure våren som behöver beständigt lagrings utrymme.  I det här exemplet kallas programmet för *jpspring*.

    > ![Hitta din applicationb](media/select-service.jpg)

1. Under **inställnings** rubriken väljer du **appar**.

1. Dina våren Cloud-tjänster kommer att visas i tabellen.  Välj den tjänst som du vill lägga till beständig lagring för.  I det här exemplet ska vi välja vår **Gateway** -tjänst.

    > ![Välj din tjänst](media/select-gateway.jpg)

1. Från tjänstens konfigurations blad väljer du **konfiguration**

1. Välj fliken **beständig lagring** och Aktivera beständig lagring.

    > ![Aktivera beständig lagring](media/enable-persistent-storage.jpg)

När beständig lagring har Aktiver ATS visas dess storlek och sökväg både på den här sidan.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Använd Azure CLI för att ändra beständig lagring

Skapa en app med beständig disk aktive rad:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Aktivera beständig lagring i en befintlig app:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Inaktivera beständig lagring i en befintlig app:

> [!WARNING]
> Om du inaktiverar beständigt lagrings utrymme frigörs lagringen för programmet, och alla data som lagrats där går förlorade. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [program-och tjänst kvoter](spring-cloud-quotas.md)eller Lär dig hur du [manuellt skalar ditt program](spring-cloud-tutorial-scale-manual.md).