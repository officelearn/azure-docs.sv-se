---
title: Azure VMware-lösning av CloudSimple snabb start – skapa tjänst
description: Lär dig att skapa CloudSimple-tjänsten, etablera noder och reservera noder
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 13b07b3b50bdb03373275ca9594baa6357e9f66f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812287"
---
# <a name="quickstart---create-service"></a>Snabb start – skapa tjänst

Kom igång genom att skapa Azure VMware-lösningen genom CloudSimple i Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-lösning av CloudSimple-service – översikt

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple.  Genom att skapa tjänsten kan du etablera noder, reservera noder och skapa privata moln.  Du lägger till CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple.  Det här Edge-nätverket används för tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina privata moln.

Om du vill lägga till CloudSimple-tjänsten måste du skapa ett Gateway-undernät. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något av dina lokala nätverks adress utrymmen eller adress utrymmet för det virtuella Azure-nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernät.  Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för gräns nätverks tjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön.  De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänst**.

    ![Sök CloudSimple-tjänst](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="provision-nodes"></a>Etablera noder

Om du vill ställa in betala per användning-kapacitet för en CloudSimple privat moln miljö ska du först etablera noder i Azure Portal.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-noder**.

    ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill etablera CloudSimple-noder.
6. Välj resurs grupp för noderna. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange prefixet för att identifiera noderna.
8. Välj platsen för nodens resurser.
9. Välj den dedikerade plats som ska vara värd för nodens resurser.
10. Välj nodtyp. Du kan välja [alternativet CS28 eller CS36](cloudsimple-node.md). Det senare alternativet inkluderar den maximala beräknings-och minnes kapaciteten.
11. Välj antalet noder som ska etableras.
12. Välj **Granska + Skapa**.
13. Granska inställningarna. Klicka på **föregående**om du vill ändra inställningarna.
14. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett privat moln och konfigurera miljön](quickstart-create-private-cloud.md)
* Läs mer om [CloudSimple-tjänsten](https://docs.azure.cloudsimple.com/cloudsimple-service)
