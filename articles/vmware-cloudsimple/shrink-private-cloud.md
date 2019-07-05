---
title: Minska Azure VMware-lösning av CloudSimple privat moln
description: Beskriver hur du minska ett CloudSimple privat moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544522"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Minska ett CloudSimple privat moln

CloudSimple ger dig flexibilitet att krympa dynamiskt ett privat moln.  Ett privat moln består av en eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När ett privat moln, ta bort en nod från det befintliga klustret eller ta bort ett helt kluster. 

## <a name="before-you-begin"></a>Innan du börjar

Följande villkor måste uppfyllas för förminskas för ett privat moln.  Hantering av kluster (första kluster) skapas när ett privat moln har skapats kan inte tas bort.

* Ett vSphere-kluster måste ha tre noder.  Ett kluster med bara tre noder kan inte krympas.
* Totalt lagringsutrymme som förbrukas bör inte överskrida den totala kapaciteten efter förminskas i klustret. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Minska ett privat moln 

1. [Få åtkomst till portalen CloudSimple](access-cloudsimple-portal.md).

2. Öppna den **resurser** sidan.

3. Klicka på det privata molnet som du vill minska

4. På sidan Sammanfattning klickar du på **minska**.

    ![Minska privat moln](media/shrink-private-cloud.png)

5. Välj det kluster som du vill minska eller ta bort. 

    ![Minska privat moln - väljer kluster](media/shrink-private-cloud-select-cluster.png)

6. Välj **ta bort en nod** eller **ta bort hela klustret**. 

7. Verifiera klusterkapacitet

8. Klicka på **skicka** att minska det privata molnet.

Förminskas för privat moln startar.  Du kan övervaka förloppet i uppgifter.  Förminskas-processen kan ta några timmar beroende på data som måste vara resynced på virtuellt SAN-nätverk.

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)