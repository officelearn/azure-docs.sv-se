---
title: Skapa en Azure Kubernetes service-klusterresurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Kubernetes service-resurs (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 5e442f38a8e81170eeeac2f31f00a7d0eeb08d2b
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377425"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Skapa en Azure Kubernetes service-klusterresurs

1. Gå till [Azure Kubernetes-tjänsten](https://ms.portal.azure.com/#create/microsoft.aks)och välj **skapa**.

1. På fliken **grundläggande** anger du följande information:

    |Inställning|Value|
    |--|--|
    |Subscription|Välj en lämplig prenumeration.|
    |Resource group|Välj en tillgänglig resurs grupp.|
    |Kubernetes-klusternamn|Ange ett namn (gemener).|
    |Region|Välj en närliggande plats.|
    |Kubernetes-version|1.12.8 (standard).|
    |DNS-namnprefix|Skapas automatiskt, men du kan åsidosätta.|
    |Nodstorlek|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Nodantal|Lämna skjutreglaget till standardvärdet.|

1. På fliken **skala** lämnar du **virtuella noder** och **skalnings uppsättningar för virtuella datorer (för hands version)** inställda till standardvärdena.
1. På fliken **autentisering** lämnar du **tjänstens huvud namn** och **aktiverar RBAC** -inställningen till standardvärdena.
1. På fliken **nätverk** anger du följande alternativ:

    |Inställning|Value|
    |--|--|
    |HTTP-programroutning|Nej|
    |Nätverks konfiguration|Basic|

1. Kontrol lera att **Aktivera behållar övervakning** är inställt på **Ja**på fliken **övervakning** och lämna **Log Analytics arbets yta** som standardvärde.
1. På fliken **taggar** lämnar du namn/värde-par tomma för tillfället.
1. Välj **Granska och skapa**.
1. När verifieringen har godkänts väljer du **skapa**.

> [!NOTE]
> Om verifieringen Miss lyckas kan det bero på ett "tjänstens huvud namn"-fel. Gå tillbaka till fliken **autentisering** och gå sedan tillbaka till **Granska + skapa**, där validering ska köras och skicka sedan.
