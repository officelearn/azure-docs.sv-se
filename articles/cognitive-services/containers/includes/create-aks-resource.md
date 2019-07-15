---
title: Skapa en resurs för Azure Kubernetes Service-kluster
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en resurs i Azure Kubernetes Service (AKS).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: ab7ce8b4a538e6a286a00285069a22878c5d88d9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877450"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Skapa en resurs för Azure Kubernetes Service-kluster

1. Gå till [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks) och välj **skapa**.

1. På den **grunderna** ange följande information:

    |Inställning|Value|
    |--|--|
    |Subscription|Välj en lämplig prenumeration|
    |Resource group|Välj en tillgänglig resursgrupp|
    |Kubernetes-klusternamn|Ange ett namn (små)|
    |Region|Välj en närliggande plats|
    |Kubernetes-version|1.12.8 (standard)|
    |DNS-namnprefix|Skapas automatiskt, men du kan åsidosätta|
    |Nodstorlek|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Antal noder|Lämna skjutreglaget på standardvärdet|

1. På den **skala** fliken, lämna **virtuella noder** och **skalningsuppsättningar för virtuella datorer (förhandsversion)** inställd på standardvärdena.
1. På den **autentisering** fliken, lämna **tjänstens huvudnamn** och **aktivera RBAC** inställd på standardvärdena.
1. På den **nätverk** ange följande val:

    |Inställning|Värde|
    |--|--|
    |Routning av HTTP-program|Nej|
    |Nätverkskonfigurationen|Basic|

1. På den **övervakning** fliken, se till att **aktivera behållarövervakning** är inställd på **Ja**, och lämna **Log Analytics-arbetsyta** som den standardvärde.
1. På den **taggar** fliken, inget namn/värde-par för tillfället.
1. Välj **granska och skapa**.
1. När valideringen är godkänd väljer **skapa**.

> [!NOTE]
> Om valideringen misslyckas, kanske på grund av felet ”tjänstens huvudnamn”. Gå tillbaka till den **autentisering** fliken och sedan tillbaka till **granska + skapa**, där verifiering bör köra och sedan skicka.
