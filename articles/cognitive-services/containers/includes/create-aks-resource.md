---
title: Skapa en Klusterresurs för Azure Kubernetes-tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en AKS-resurs (Azure Kubernetes Service).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 4e3102912e88ef904fed3e680f8cdd23242b1f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383444"
---
## <a name="create-an-azure-kubernetes-service-cluster-resource"></a>Skapa en Klusterresurs för Azure Kubernetes-tjänst

1. Gå till [Azure Kubernetes Service](https://ms.portal.azure.com/#create/microsoft.aks)och välj **Skapa**.

1. Ange följande information på fliken **Grunderna:**

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj en lämplig prenumeration.|
    |Resursgrupp|Välj en tillgänglig resursgrupp.|
    |Kubernetes klusternamn|Ange ett namn (gemener).|
    |Region|Välj en plats i närheten.|
    |Kubernetes version|Oavsett vilket värde som markeras som **(standard)**.|
    |Prefix för DNS-namn|Skapad automatiskt, men du kan åsidosätta.|
    |Nodstorlek|Standard DS2 v2:<br>`2 vCPUs`, `7 GB`|
    |Antal nod|Lämna skjutreglaget på standardvärdet.|

1. Lämna **virtuella noder** och **vm-skaluppsättningar** inställda på standardvärdena på fliken **Skala.**
1. Lämna **tjänstens huvudnamn** och **Aktivera RBAC** till standardvärdena på fliken **Autentisering.**
1. Ange följande val på fliken **Nätverk:**

    |Inställning|Värde|
    |--|--|
    |Routning av HTTP-program|Inga|
    |Nätverkskonfiguration|Basic|

1. Kontrollera att **Aktivera behållarövervakning** är inställt på **Ja**och lämna **Log Analytics-arbetsytan** som standardvärde på fliken **Övervakning.**
1. Lämna namn-/värdeparen tomma för tillfället på fliken **Taggar.**
1. Välj **Granska och skapa**.
1. När valideringen har gått väljer du **Skapa**.

> [!NOTE]
> Om valideringen misslyckas kan det bero på felet "Tjänstens huvudnamn". Gå tillbaka till fliken **Autentisering** och gå sedan tillbaka till **Granska + skapa**, där valideringen ska köras och sedan passera.
