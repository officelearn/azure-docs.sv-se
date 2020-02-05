---
title: Minska Azure VMware-lösningar (AVS) privat moln
description: Beskriver hur du krymper ett privat moln moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014274"
---
# <a name="shrink-an-avs-private-cloud"></a>Krymp ett privat AVS-moln

AVS ger flexibiliteten att dynamiskt minska ett privat moln i AVS. Ett privat AVS-moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du krymper ett privat moln moln tar du bort en nod från det befintliga klustret eller tar bort ett helt kluster. 

## <a name="before-you-begin"></a>Innan du börjar

Följande villkor måste vara uppfyllda innan du krymper ett privat moln. Hanterings klustret (det första klustret) skapas när det privata moln molnet skapades. Det går inte att ta bort den.

* Ett vSphere-kluster måste ha tre noder. Det går inte att krympa ett kluster med tre noder.
* Den totala förbrukade lagringen bör inte överskrida den totala kapaciteten efter krympning av klustret.
* Kontrol lera om några DRS-regler (Distributed Resource Scheduler) förhindrar vMotion av en virtuell dator. Om det finns regler inaktiverar eller tar du bort reglerna. DRS-regler inkluderar virtuell dator som värd tillhörighets regler.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Minska ett privat AVS-moln

1. [Få åtkomst till AVS-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** .

3. Klicka på det AVS-privata moln som du vill krympa

4. På sidan Sammanfattning klickar du på **Krymp**.

    ![Minska det privata molnet i AVS-molnet](media/shrink-private-cloud.png)

5. Välj det kluster som du vill krympa eller ta bort. 

    ![Krymp molnets privata moln – Välj kluster](media/shrink-private-cloud-select-cluster.png)

6. Välj **ta bort en nod** eller **ta bort hela klustret**. 

7. Verifiera kluster kapaciteten

8. Klicka på **Skicka** för att krympa det privata moln molnet.

Minskning av molnets privata moln startar. Du kan övervaka förloppet i aktiviteterna. Krympnings processen kan ta några timmar beroende på vilka data som behöver synkroniseras om på virtuellt San.

> [!NOTE]
> 1. Om du krymper ett privat moln genom att ta bort det sista eller det enda klustret i data centret tas data centret inte bort.
> 2. Om någon DRS regel överträdelse uppstår tas noden inte bort från klustret och uppgifts beskrivningen visar att borttagning av en nod bryter mot DRS-regler i klustret.    


## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer i Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig mer om [moln privata moln](cloudsimple-private-cloud.md)
