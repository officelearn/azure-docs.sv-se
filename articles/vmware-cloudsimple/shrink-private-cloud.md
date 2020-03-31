---
title: Krympa Azure VMware-lösning av CloudSimple Private Cloud
description: Beskriver hur du krymper ett CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014274"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Krympa ett cloudsimple privat moln

CloudSimple ger flexibiliteten att dynamiskt krympa ett privat moln.  Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du krymper ett privat moln tar du bort en nod från det befintliga klustret eller tar bort ett helt kluster. 

## <a name="before-you-begin"></a>Innan du börjar

Följande villkor måste uppfyllas för krympning av ett privat moln.  Hanteringskluster (första klustret) som skapades när ett privat moln skapades kan inte tas bort.

* Ett vSphere-kluster måste ha tre noder.  Det går inte att krympa ett kluster med endast tre noder.
* Totalt lagringsutrymme som förbrukas bör inte överstiga den totala kapaciteten efter krympning av klustret.
* Kontrollera om några DRS-regler (Distributed Resource Scheduler) förhindrar vMotion av en virtuell dator.  Om det finns regler inaktiverar eller tar du bort reglerna.  DRS-regler inkluderar virtuella datorer som värd för tillhörighetsregler.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Krympa ett privat moln

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **Resurser.**

3. Klicka på det privata molnet som du vill krympa

4. Klicka på **Förminska**på sammanfattningssidan.

    ![Förminska privat moln](media/shrink-private-cloud.png)

5. Markera det kluster som du vill förminska eller ta bort. 

    ![Förminska privat moln – välj kluster](media/shrink-private-cloud-select-cluster.png)

6. Välj **Ta bort en nod** eller Ta bort hela **klustret**. 

7. Verifiera klusterkapaciteten

8. Klicka på **Skicka** om du vill förminska det privata molnet.

Krympa av det privata molnet startar.  Du kan övervaka förloppet i aktiviteter.  Krympprocessen kan ta några timmar beroende på data, som måste synkroniseras på vSAN.

> [!NOTE]
> 1. Om du krymper ett privat moln genom att ta bort det sista eller det enda klustret i datacentret tas inte datacentret bort.
> 2. Om någon DRS-regelöverträdelse inträffar tas noden inte bort från klustret och uppgiftsbeskrivningen visar att om du tar bort en nod bryter du mot DRS-reglerna i klustret.    


## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Läs mer om [privata moln](cloudsimple-private-cloud.md)
