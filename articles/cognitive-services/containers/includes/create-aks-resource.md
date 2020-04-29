---
title: Skapa en Azure Kubernetes service-klusterresurs
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Kubernetes service-resurs (AKS).
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d01245a76c5210a1843307b543f98d4f1d59d2cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877866"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Skapa en Azure Kubernetes service-klusterresurs

1. Gå till [Azure Kubernetes-tjänsten](https://ms.portal.azure.com/#create/microsoft.aks)och välj **skapa**.

1. På fliken **grundläggande** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj en lämplig prenumeration.|
    |Resursgrupp|Välj en tillgänglig resurs grupp.|
    |Kluster namn för Kubernetes|Ange ett namn (gemener).|
    |Region|Välj en närliggande plats.|
    |Kubernetes-version|Vilket värde som är markerat som **(standard)**.|
    |DNS-namn-prefix|Skapas automatiskt, men du kan åsidosätta.|
    |Node-storlek|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Antal noder|Lämna skjutreglaget till standardvärdet.|

1. På fliken **skala** lämnar du de **virtuella noderna** och **VM Scale** set till standardvärdena.
1. På fliken **autentisering** lämnar du **tjänstens huvud namn** och **aktiverar RBAC** -inställningen till sina standardvärden.
1. På fliken **nätverk** anger du följande alternativ:

    |Inställning|Värde|
    |--|--|
    |Routning av HTTP-program|Nej|
    |Nätverks konfiguration|Basic|

1. Kontrol lera att **Aktivera behållar övervakning** är inställt på **Ja**på fliken **övervakning** och lämna **Log Analytics arbets yta** som standardvärde.
1. På fliken **taggar** lämnar du namn/värde-par tomma för tillfället.
1. Välj **Granska och skapa**.
1. När verifieringen har godkänts väljer du **skapa**.

> [!NOTE]
> Om verifieringen Miss lyckas kan det bero på ett "tjänstens huvud namn"-fel. Gå tillbaka till fliken **autentisering** och gå sedan tillbaka till **Granska + skapa**, där validering ska köras och skicka sedan.
