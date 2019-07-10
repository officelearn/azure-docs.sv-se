---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar ett azure kubernetes (AKS) resursen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: eb2d609c3a7dfd769c6a19854366ac5e45fddb7d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712472"
---
## <a name="create-an-azure-kubernetes-service-aks-cluster-resource"></a>Skapa en klusterresurs för Azure Kubernetes Service (AKS)

1. Gå till den [skapa](https://ms.portal.azure.com/#create/microsoft.aks) för Kubernetes-tjänster.

1. På den **grunderna** ange följande information:

    |Inställning|Value|
    |--|--|
    |Subscription|Välj lämplig prenumeration|
    |Resource group|Välj en tillgänglig resursgrupp|
    |Kubernetes-klusternamn|Önskat namn (gemener)|
    |Region|Välj en närliggande plats|
    |Kubernetes-version|1.12.8 (standard)|
    |DNS-namnprefix|Skapas automatiskt, men du kan åsidosätta|
    |Nodstorlek|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Antal noder|Lämna skjutreglaget på standardvärdet|

1. På den **skala** fliken, lämna den *virtuella noder* och *VM-skalningsuppsättningar* standardvärdena.
1. På den **autentisering** fliken, lämna *tjänstens huvudnamn* och *aktivera RBAC* standardvärdena.
1. På den **nätverk** ange följande val:

    |Inställning|Value|
    |--|--|
    |Routning av HTTP-program|Nej|
    |Nätverkskonfigurationen|Basic|

1. På den **övervakning** fliken, se till att *aktivera behållarövervakning* är inställd på **Ja** och lämna den *Log Analytics-arbetsyta* som dess standardvärde
1. På den **taggar** fliken, inget namn/värde-par för tillfället.
1. Klicka på **granska och skapa**.
1. När valideringen lyckats klickar du på **skapa**.

> [!NOTE]
> Om valideringen misslyckas kan det bero på grund av en *tjänstens huvudnamn* fel. Gå tillbaka till den *autentisering* och sedan tillbaka till *granska + skapa* var verifiering bör köra och sedan skicka.
