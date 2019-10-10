---
title: Azure VMware-lösning av CloudSimple – skapa CloudSimple-tjänst
description: Beskriver hur du skapar CloudSimple-tjänsten i Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173510"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Skapa Azure VMware-lösningen av CloudSimple-tjänsten

För att komma igång med Azure VMware-lösningen från CloudSimple skapar du Azure VMware-lösningen från CloudSimple-tjänsten i Azure Portal.

> [!IMPORTANT]
> Innan du skapar CloudSimple-tjänsten måste du registrera Resource-providern för Microsoft. VMwareCloudSimple på din Azure-prenumeration. Följ stegen i [Aktivera resurs leverantören Microsoft. VMwareCloudSimple på din Azure-prenumeration](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-the-service"></a>Skapa tjänsten

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
    ![Search CloudSimple service @ no__t-1
3. Välj **CloudSimple-tjänster**.
4. Klicka på **Lägg till** för att skapa en ny tjänst.
    ![Add CloudSimple service @ no__t-1
5. Välj den prenumeration där du vill skapa CloudSimple-tjänsten.
6. Välj resurs grupp för tjänsten. Om du vill lägga till en ny resurs grupp klickar du på **Skapa ny**.
7. Ange namnet för att identifiera tjänsten.
8. Ange CIDR för service Gateway. Ange ett/28-undernät som inte överlappar med något av dina lokala undernät, Azure-undernät eller planerade CloudSimple-undernät. Du kan inte ändra CIDR när tjänsten har skapats.

    ![Skapa CloudSimple-tjänsten](media/create-cloudsimple-service.png)
9. Klicka på **OK**

Tjänsten skapas och läggs till i listan över tjänster.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig hur du [skapar ett privat moln](create-private-cloud.md)
* Lär dig hur du [konfigurerar en privat moln miljö](quickstart-create-private-cloud.md)
