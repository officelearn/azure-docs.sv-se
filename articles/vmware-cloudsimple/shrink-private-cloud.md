---
title: Minska Azure VMware-lösningen genom CloudSimple privat moln
description: Beskriver hur du krymper ett privat CloudSimple-moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ae2f87a3719853f4a91cb8ba801be6d578597d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825691"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Krymp ett privat CloudSimple-moln

CloudSimple ger flexibiliteten att dynamiskt minska ett privat moln.  Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du krymper ett privat moln tar du bort en nod från det befintliga klustret eller tar bort ett helt kluster. 

## <a name="before-you-begin"></a>Innan du börjar

Följande villkor måste vara uppfyllda för krympning av ett privat moln.  Hanterings kluster (det första klustret) som skapades när ett privat moln skapades går inte att ta bort.

* Ett vSphere-kluster måste ha tre noder.  Det går inte att krympa ett kluster med tre noder.
* Den totala förbrukade lagringen bör inte överskrida den totala kapaciteten efter krympning av klustret. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Krympa ett privat moln

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** .

3. Klicka på det privata moln som du vill krympa

4. På sidan Sammanfattning klickar du på **Krymp**.

    ![Krymp privat moln](media/shrink-private-cloud.png)

5. Välj det kluster som du vill krympa eller ta bort. 

    ![Krymp privat moln – Välj kluster](media/shrink-private-cloud-select-cluster.png)

6. Välj **ta bort en nod** eller **ta bort hela klustret**. 

7. Verifiera kluster kapaciteten

8. Klicka på **Skicka** för att krympa det privata molnet.

Krympningen av det privata molnet startar.  Du kan övervaka förloppet i aktiviteterna.  Krympnings processen kan ta några timmar beroende på vilka data som behöver synkroniseras om på virtuellt San.

> [!NOTE]
> Om du krymper ett privat moln genom att ta bort det sista eller det enda klustret i data centret tas data centret inte bort.  


## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
