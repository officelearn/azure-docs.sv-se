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
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039093"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Använda beständig lagring i Azure våren Cloud

Azure våren Cloud tillhandahåller två typer av lagring för ditt program: beständigt och temporärt.  Azure våren Cloud möjliggör tillfällig lagring som standard för varje program instans. Den tillfälliga lagringen är begränsad till 5 GB och dess standard monterings Sök väg är `/tmp`.

> [!WARNING]
> Om du startar om en program instans tas dess associerade temporära lagring permanent bort.

Beständig lagring är en fil resurs behållare som hanteras av Azure och som tilldelas per program-omfattning. Data som lagras i beständig lagring delas i alla instanser av programmet. En Azure våren moln tjänst instans kan ha högst 10 program med beständig disk aktive rad och varje program får 50 GB beständig lagring. Standard monterings vägen för beständigt lagrings utrymme är `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Aktivera beständig lagring med hjälp av Azure Portal

1. På sidan Azure våren Cloud Service väljer du **program instrument panel**och väljer sedan det program som kräver beständig lagring.
1. Leta upp attributet **beständig lagring** på fliken **Översikt** och välj **inaktive rad**.
1. Klicka på **Aktivera** för att aktivera beständig lagring och välj **OK** om du vill tillämpa ändringen.

När beständig lagring har Aktiver ATS visas dess storlek och sökväg i attributet **beständig lagring** på sidan **Översikt** .

> [!WARNING]
> Om du *inaktiverar* beständig lagring frigörs lagringen för programmet.  Alla data i det lagrings kontot kommer att gå förlorade. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Aktivera beständig lagring med hjälp av Azure CLI

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