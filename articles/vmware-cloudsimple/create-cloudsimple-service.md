---
title: Azure VMware-lösningar (AVS) – skapa AVS-tjänst
description: Beskriver hur du skapar AVS-tjänsten i Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024831"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Skapa tjänsten Azure VMware-lösningar (AVS)

Kom igång med Azure VMware-lösningar (AVS) genom att skapa Azure VMware-lösningar (AVS) i Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernätet. Ett Gateway-undernät krävs per AVS-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för gräns nätverks tjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med AVS-miljön. De nätverk som kommunicerar med AVS inkluderar lokala nätverk och virtuella Azure-nätverk.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **AVS-tjänster**.
    ](media/create-cloudsimple-service-search.png) för ![Sök i AVS-tjänsten
3. Välj **AVS-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.
    ![lägga till](media/create-cloudsimple-service-add.png) för AVS-tjänst
5. Välj den prenumeration där du vill skapa AVS-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade AVS-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa AVS-tjänsten](media/create-cloudsimple-service.png)
9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig hur du [skapar ett privat AVS-moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en moln miljö för privata moln](quickstart-create-private-cloud.md)
