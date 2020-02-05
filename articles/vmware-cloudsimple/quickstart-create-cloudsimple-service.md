---
title: Azure VMware-lösningar (AVS) snabb start – skapa tjänst
description: Lär dig hur du skapar AVS-tjänsten, Purchase Nodes och Reserve Nodes
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024440"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Snabb start – skapa Azure VMware-lösningar (AVS)-tjänst

Kom igång genom att skapa Azure VMware-lösningarna (AVS) i Azure Portal.

## <a name="vmware-solutions-avs---service-overview"></a>VMware-lösningar (AVS) – Översikt över tjänster

Med AVS-tjänsten kan du använda Azure VMware-lösningen enligt AVS. Genom att skapa tjänsten kan du etablera noder, reservera noder och skapa moln privata moln. Du lägger till AVS-tjänsten i varje Azure-region där AVS-tjänsten är tillgänglig. Tjänsten definierar Edge-nätverket för Azure VMware-lösningen enligt AVS. Det här Edge-nätverket används för tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina moln privata moln.

Om du vill lägga till AVS-tjänsten måste du skapa ett Gateway-undernät. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något av dina lokala nätverks adress utrymmen eller adress utrymmet för det virtuella Azure-nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernät. Ett Gateway-undernät krävs per AVS-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för Azure VMware-lösning av AVS Edge-nätverkstjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med AVS-miljön. De nätverk som kommunicerar med AVS inkluderar lokala nätverk och virtuella Azure-nätverk.

Granska [nätverks krav](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **AVS-tjänst**.

    ![Sök efter AVS-tjänst](media/create-cloudsimple-service-search.png)

3. Välj **AVS-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.

    ![Lägg till AVS-tjänst](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa AVS-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade AVS-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa AVS-tjänsten](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="provision-nodes"></a>Etablera noder

Om du vill ställa in betala per användning-kapacitet för en moln miljö i molnet, etablerar du först noder i Azure Portal.

1. Välj **Alla tjänster**.
2. Sök efter **AVS-noder**.

    ![Sök i AVS-noder](media/create-cloudsimple-node-search.png)

3. Välj **AVS-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till AVS-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera AVS-noder.
6. Välj resurs grupp för noderna. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj platsen för nodens resurser.
9. Välj den dedikerade plats som ska vara värd för nodens resurser.
10. Välj [nodtyp](cloudsimple-node.md).
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Klicka på **föregående**om du vill ändra inställningarna.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett privat AVS-moln och konfigurera miljön](quickstart-create-private-cloud.md)
* Läs mer om [AVS-tjänsten](https://docs.azure.cloudsimple.com/cloudsimple-service)
