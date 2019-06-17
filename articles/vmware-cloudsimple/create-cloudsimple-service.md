---
title: Skapa Azure VMware-lösning genom CloudSimple - tjänsten
description: Beskriver hur du skapar tjänsten CloudSimple i Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676960"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Skapa Azure VMware-lösning av CloudSimple - tjänsten

Skapa Azure VMware-lösningen för att komma igång med Azure VMware-lösningen med CloudSimple av CloudSimple-tjänsten i Azure-portalen.

> [!NOTE]
> Innan du skapar tjänsten CloudSimple måste du registrera resursprovidern Microsoft.VMwareCloudSimple på din Azure-prenumeration. Följ stegen i [aktivera Microsoft.VMwareCloudSimple resursprovidern på Azure-prenumerationen](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Services**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Välj **CloudSimple Services**.

4. Klicka på **Lägg till** att skapa en ny tjänst.

    ![Add CloudSimple Service](media/create-cloudsimple-service-add.png)

5. Välj den prenumeration där du vill skapa den CloudSimple-tjänsten.

6. Välj resursgrupp för tjänsten. Lägg till en ny resursgrupp genom att klicka på **Skapa ny**.

7. Ange namn för att identifiera tjänsten.

8. Ange CIDR för service-gateway. Ange en/28 undernät som inte överlappar med något av dina befintliga undernät.  Dessa omfattar den lokala undernät, Azure undernät, eller någon planerat CloudSimple undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänst](media/create-cloudsimple-service.png)

9. Klicka på **OK**.

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapa ett privat moln](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Lär dig hur du [konfigurera en privat molnmiljö](quickstart-create-private-cloud.md)
