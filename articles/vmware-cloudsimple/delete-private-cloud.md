---
title: Ta bort ett Azure VMware-lösningar (AVS) privat moln
description: Beskriver hur du tar bort ett privat AVS-moln.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024763"
---
# <a name="delete-an-avs-private-cloud"></a>Ta bort ett privat AVS-moln

AVS ger flexibiliteten att ta bort ett privat moln i AVS-molnet. Ett privat AVS-moln består av ett eller flera vSphere-kluster. Varje kluster kan ha 3 till 16 noder. När du tar bort ett moln privat moln tas alla kluster bort.

## <a name="before-you-begin"></a>Innan du börjar

Borttagning av ett privat AVS-moln tar bort hela det molnbaserade molnet. Alla komponenter i det privata AVS-molnet tas bort. Om du vill behålla någon av dessa data kontrollerar du att du har säkerhetskopierat data till lokal lagring eller Azure Storage.

Komponenterna i ett privat moln moln är:

* AVS-noder
* Virtuella maskiner
* VLAN/undernät
* Alla användar data som lagras i molnets privata moln
* Alla brand Väggs regler för bilagor till ett VLAN/undernät

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Ta bort ett privat AVS-moln

1. [Få åtkomst till AVS-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** .

3. Klicka på det AVS-privata moln som du vill ta bort

4. På sidan Sammanfattning klickar du på **ta bort**.

    ![Ta bort ett privat AVS-moln](media/delete-private-cloud.png)

5. På sidan bekräftelse anger du namnet på det privata AVS-molnet och klickar på **ta bort**. 

    ![Ta bort AVS-privat moln – bekräfta](media/delete-private-cloud-confirm.png)

Det privata AVS-molnet har marker ATS för borttagning. Borttagnings processen startar efter tre timmar och tar bort det privata AVS-molnet.

> [!CAUTION]
> Noder måste tas bort efter att det privata AVS-molnet har tagits bort. Mätning av noder kommer att fortsätta tills noder tas bort från din prenumeration.

## <a name="next-steps"></a>Nästa steg

* [Ta bort noder](delete-nodes.md)
