---
title: Azure VMware-lösning från CloudSimple - Skapa CloudSimple-tjänst
description: Beskriver hur du skapar CloudSimple-tjänsten i Azure-portalen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024831"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Skapa Azure VMware-lösningen via CloudSimple-tjänsten

Om du vill komma igång med Azure VMware Solution by CloudSimple skapar du Azure VMware-lösningen via CloudSimple-tjänsten i Azure-portalen.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett /28 CIDR-block för gateway-undernätet. Ett gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där den skapas. Gateway-undernätet används för edge-nätverkstjänster och kräver ett /28 CIDR-block. Gateway-undernätsadressutrymmet måste vara unikt. Den får inte överlappa med något nätverk som kommunicerar med CloudSimple-miljön. De nätverk som kommunicerar med CloudSimple inkluderar lokala nätverk och virtuella Azure-nätverk.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
    ![Sök CloudSimple-tjänst](media/create-cloudsimple-service-search.png)
3. Välj **CloudSimple Services**.
4. Klicka på **Lägg till** om du vill skapa en ny tjänst.
    ![Lägg till CloudSimple-tjänst](media/create-cloudsimple-service-add.png)
5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resursgruppen för tjänsten. Om du vill lägga till en ny resursgrupp klickar du på **Skapa ny**.
7. Ange namn för att identifiera tjänsten.
8. Ange CIDR för tjänstgatewayen. Ange ett /28-undernät som inte överlappar något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)
9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [etablerar noder](create-nodes.md)
* Lär dig hur du [skapar ett privat moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en privat molnmiljö](quickstart-create-private-cloud.md)
