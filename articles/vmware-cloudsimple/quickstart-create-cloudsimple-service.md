---
title: Azure VMware-lösning av CloudSimple snabb start – skapa tjänst
description: Lär dig hur du skapar CloudSimple-tjänsten, Purchase Nodes och Reserve Nodes
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574536"
---
# <a name="quickstart---create-cloudsimple-service"></a>Snabb start – skapa CloudSimple-tjänst

Kom igång genom att skapa Azure VMware-lösningen genom CloudSimple i Azure Portal.

Med CloudSimple-tjänsten kan du använda Azure VMware-lösningen från CloudSimple.  Genom att skapa tjänsten kan du köpa noder, reservera noder och skapa privata moln.  Du lägger till CloudSimple-tjänsten i varje Azure-region där CloudSimple-tjänsten är tillgänglig.  Tjänsten definierar Edge-nätverket för Azure VMware-lösningen från CloudSimple.  Det här Edge-nätverket används för tjänster som omfattar VPN, ExpressRoute och Internet anslutning till dina privata moln.

Om du vill lägga till CloudSimple-tjänsten måste du skapa ett Gateway-undernät. Gateway-undernätet används när du skapar Edge-nätverket och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något av dina lokala nätverks adress utrymmen eller adress utrymmet för det virtuella Azure-nätverket.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Aktivera Microsoft. VMwareCloudSimple Resource Provider

Följ stegen nedan för att aktivera resurs leverantören för CloudSimple-tjänsten.

1. Välj **Alla tjänster**.
2. Sök efter och välj **prenumerationer**.

    ![Välj prenumerationer](media/cloudsimple-service-select-subscriptions.png)

3. Välj den prenumeration där du vill aktivera CloudSimple-tjänsten.
4. Klicka på **resurs leverantörer** för prenumerationen.
5. Använd **Microsoft. VMwareCloudSimple** för att filtrera resurs leverantören.
6. Välj resurs leverantören **Microsoft. VMwareCloudSimple** och klicka på **Registrera**.

    ![Registrera resursprovidern](media/cloudsimple-service-enable-resource-provider.png)

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

## <a name="purchase-nodes"></a>Köpa noder

Om du vill ställa in betala per användning-kapacitet för en CloudSimple privat moln miljö ska du först etablera noder i Azure Portal.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-noder**.

    ![Sök CloudSimple-noder](media/create-cloudsimple-node-search.png)

3. Välj **CloudSimple-noder**.
4. Klicka på **Lägg till** för att skapa noder.

    ![Lägg till CloudSimple-noder](media/create-cloudsimple-node-add.png)

5. Välj den prenumeration där du vill köpa CloudSimple-noder.
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
* Läs mer om [CloudSimple-tjänsten](cloudsimple-service.md)
