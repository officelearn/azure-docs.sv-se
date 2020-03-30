---
title: Ta bort en Azure VMware-lösning av CloudSimple Private Cloud
description: Beskriver hur du tar bort ett CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024763"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Ta bort ett cloudsimple privat moln

CloudSimple ger flexibiliteten att ta bort ett privat moln.  Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du tar bort ett privat moln tas alla kluster bort.

## <a name="before-you-begin"></a>Innan du börjar

Borttagning av ett privat moln tar bort hela det privata molnet.  Alla komponenter i det privata molnet tas bort.  Om du vill behålla någon av data, se till att du har säkerhetskopierat data till lokalt lagringsutrymme eller Azure-lagring.

Komponenterna i ett privat moln inkluderar:

* CloudSimple-noder
* Virtuella datorer
* VLAN/undernät
* Alla användardata som lagras i det privata molnet
* Alla brandväggsregelbilagor till ett VLAN/undernät

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Ta bort ett privat moln

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **Resurser.**

3. Klicka på det privata moln som du vill ta bort

4. Klicka på Ta **bort**på sammanfattningssidan .

    ![Ta bort privat moln](media/delete-private-cloud.png)

5. På bekräftelsesidan anger du namnet på det privata molnet och klickar på **Ta bort**. 

    ![Ta bort privat moln - bekräfta](media/delete-private-cloud-confirm.png)

Det privata molnet är markerat för borttagning.  Borttagningsprocessen startar efter tre timmar och tar bort det privata molnet.

> [!CAUTION]
> Noder måste tas bort efter borttagning av det privata molnet.  Mätning av noder fortsätter tills noder tas bort från din prenumeration.

## <a name="next-steps"></a>Nästa steg

* [Ta bort noder](delete-nodes.md)
