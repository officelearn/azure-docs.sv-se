---
title: Ta bort en Azure VMware-lösning via CloudSimple privat moln
description: Lär dig hur du tar bort ett privat CloudSimple-moln. När du tar bort ett privat moln tas alla kluster bort.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 11fda35f5b236a4930b3d90eb7e3a62ea60207cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142238"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Ta bort ett privat CloudSimple-moln

CloudSimple ger flexibiliteten att ta bort ett privat moln.  Ett privat moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du tar bort ett privat moln tas alla kluster bort.

## <a name="before-you-begin"></a>Innan du börjar

Om du tar bort ett privat moln tas hela det privata molnet bort.  Alla komponenter i det privata molnet tas bort.  Om du vill behålla någon av dessa data kontrollerar du att du har säkerhetskopierat data till lokal lagring eller Azure Storage.

Komponenterna i ett privat moln är:

* CloudSimple-noder
* Virtuella datorer
* VLAN/undernät
* Alla användar data som lagras i det privata molnet
* Alla brand Väggs regler för bilagor till ett VLAN/undernät

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Ta bort ett privat moln

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** .

3. Klicka på det privata moln som du vill ta bort

4. På sidan Sammanfattning klickar du på **ta bort**.

    ![Ta bort privat moln](media/delete-private-cloud.png)

5. På sidan bekräftelse anger du namnet på det privata molnet och klickar på **ta bort**. 

    ![Ta bort privat moln – bekräfta](media/delete-private-cloud-confirm.png)

Det privata molnet har marker ATS för borttagning.  Borttagnings processen startar efter tre timmar och tar bort det privata molnet.

> [!CAUTION]
> Noder måste tas bort efter att det privata molnet har tagits bort.  Mätning av noder fortsätter till noder tas bort från din prenumeration.

## <a name="next-steps"></a>Nästa steg

* [Ta bort noder](delete-nodes.md)
