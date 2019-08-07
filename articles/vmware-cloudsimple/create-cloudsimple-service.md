---
title: Skapa en Azure VMware-lösning av CloudSimple-service
description: Beskriver hur du skapar CloudSimple-tjänsten i Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812439"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Skapa en Azure VMware-lösning av CloudSimple-service

För att komma igång med Azure VMware-lösningen från CloudSimple skapar du Azure VMware-lösningen från CloudSimple-tjänsten i Azure Portal.

## <a name="before-you-begin"></a>Innan du börjar

Allokera ett/28 CIDR-block för gateway-undernät.  Ett Gateway-undernät krävs per CloudSimple-tjänst och är unikt för den region där det skapats. Gateway-undernätet används för gräns nätverks tjänster och kräver ett/28 CIDR-block. Adress utrymmet för Gateway-under nätet måste vara unikt. Det får inte överlappa något nätverk som kommunicerar med CloudSimple-miljön.  De nätverk som kommunicerar med CloudSimple omfattar lokala nätverk och virtuella Azure-nätverk.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple-tjänster**.

    ![Sök CloudSimple-tjänst](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple-tjänster**.

4. Klicka på **Lägg till** för att skapa en ny tjänst.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.

6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.

7. Ange namnet för att identifiera tjänsten.

8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina befintliga undernät.  Detta omfattar lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md)
